---
title: Getting One Piece Update Information with Python
date: 2018-12-02 18:08:00
tags: Programming Language
---

December 2nd, sunny, One Piece is on hiatus.

### Problem

As a dedicated otaku, I must watch One Piece and Fairy Tail every week. Both of these anime are available on iQIYI and are VIP content. Every weekend, I look for anime resources on YouTube or other websites. The problem is that these resource websites are often not updated in a timely manner, and I frequently need to Google “anime name + latest episode number,” such as “One Piece 864.”

Remembering the exact latest episode number of an anime each week is not something a normal otaku would do, especially for two series. This means that every time before searching for resources, I need to go to iQIYI, search for One Piece, see the latest episode number, close the page, and then go to Google to search. The same steps are needed for Fairy Tail.

Moreover, seeing the latest episode number on iQIYI does not tell me if the show is on hiatus. I still need to find the "update time" tag on the complicated PC page to check the update status. As for the mobile page or app, there is no way to check the update status.

Another way to get the latest episode number is to search the anime name directly on Baidu, which shows the latest episodes in reverse order on the homepage (Baidu is better than Google in this regard), but it still does not indicate whether the updates are normal. Furthermore, my browser defaults to Google, so searching on Baidu requires typing "baidu," pressing TAB to switch to the Baidu search engine, entering the search content, and hitting enter. This process is equally cumbersome.

### Solution

Write a simple Python script to fetch information from the iQIYI page so I can directly access the program to know the anime update status. First, import the necessary packages:

```python
import urllib.request
from bs4 import BeautifulSoup
from wsgiref.simple_server import make_server

# One Piece page URL
url = "http://www.iqiyi.com/a_19rrhb3xvl.html?vfm=2008_aldbd"
```

`urllib` is used to send HTTP requests and receive page data; `bs4` (BeautifulSoup) is used to parse the page and easily retrieve content; `wsgiref` is used to set up an HTTP server to provide web services. The URL is a global variable storing the link to the One Piece page.

```python
# Get data from the page
def reciveData(url):
    # Fetch page content
    response = urllib.request.urlopen(url)
    html = response.read()
    # Parser
    soup = BeautifulSoup(html, "html.parser", from_encoding="utf-8")
    # Update time
    p = soup.find('p', class_="episodeIntro-update")
    # Latest episode number
    i = soup.find('i', class_="title-update-num")
    return p, i
```

These lines of code send a request and retrieve information from the page. Here, the update time and the latest episode number are obtained. Next, create an HTTP server to output the content to the page:

```python
# Server environment handler function
def application(environ, start_response):
    # Get data
    p, i = reciveData(url)
    # Construct the page content
    start_response('200 OK', [('Content-Type', 'text/html')])
    content = ('<h3>One Piece</h3>'
               + 'msg: ' + p.contents[2].get_text().strip() 
               + '<br>'
               + 'num: ' + i.get_text())
    return [bytes(content, encoding="utf-8")]
```

Finally, start a local server, and visit port 8010 to see the page. You can deploy the program to a server and then directly access it:

```python
# Start the server
httpd = make_server('', 8010, application) 
httpd.serve_forever()
```

The result is shown as follows:

![preview](preview.png)

### Expansion

Based on this idea, the program can be expanded. One approach is to get comprehensive anime update information from various websites and provide the service proactively; another is to offer customized anime update information based on user input; or combine both to offer a comprehensive, bookmarkable, and customizable service. Although this idea is somewhat pointless.

### Correction

There was a simple mistake in the previous code. The program only makes a network request the first time it runs, and since the web service remains running, the returned page content is always the initial data. Solving this problem is easy: encapsulate the network request operation in a function and call that function in the application function. (The code has been corrected. To keep it concise, the Fairy Tail part of the code and console log output have been removed.)
