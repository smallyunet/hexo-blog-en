---
title: "Using Scala to Reimplement Java: A Shallow Practice"
date: 2018-12-14 12:36:36
tags: Programming Languages
---

### Origin

I wanted to implement a Markdown parser using Java, and I have currently completed the parsing of multi-level headings. Essentially, it involves using regular expressions to match and replace the corresponding content. The program is relatively simple at this stage, and the main process is as follows:

![main process](mainProcess.png)

### Reimplementation

Following the same process, I used Scala to implement this functionality, and I will continue to develop it using Scala. First, let's read the file content. For IO operations, I referred to the "Scala Cookbook," and it can be done in a single line of code:

```scala
var srcLines = Source.fromFile(srcFile).getLines().toList
```

Compared to verbose Java, Scala is indeed more concise. This is the method I previously used to read files in Java:

```java
/**
  * Read file content
  *
  * @param src File path to read
  * @return File content
  */
private static String readFile(String src) throws IOException {
    StringBuffer content = new StringBuffer();
    InputStream is = null;
    BufferedReader reader = null;

    is = new FileInputStream(src);
    reader = new BufferedReader(new InputStreamReader(is));

    String line = reader.readLine();
    while (line != null) {
        content.append(line);
        content.append("\n");
        line = reader.readLine();
    }

    if (reader != null) {
        reader.close();
    }
    if (is != null) {
        is.close();
    }
    return content.toString();
}
```

The reason for converting the string to list operations in the Scala version is that there are differences between Scala and Java in using regex matching and replacement APIs. Java uses the Matcher object for iteration, which has methods for finding and replacing:

![replace process](replaceProcess.png)

In contrast, Scala's Regex object has methods like findAllMatchIn and replaceAllIn, but the find method's object is only for searching, and the replace method cannot locate the matched content. Therefore, in Scala, the file is read into a list, and the text content is traversed with indexes as follows:

```scala
List.range(0, srcLines.size).foreach(index => {
  srcLines = srcLines.updated(index, regexReplace)
})
```

Whether or not there are matches, the loop performs an updated operation on the list, updating the original content to the content after regex replacement. This approach may be slightly inadequate, and I will continue to monitor and rectify performance issues. It can be seen that the programming philosophy of Scala differs somewhat from the typical OOP in Java.

Finally, regarding file writing, the SDK does not provide a specialized operation object, so you can use the PrintWriter from JDK:

```scala
val pw = new PrintWriter(new File(outFile))
pw.write(outString)
pw.close()
```

### Future Work

"Scala is a language that grows with developers," and I will use it to complete my graduation project.
