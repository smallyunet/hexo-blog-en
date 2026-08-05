---
title: Does JavaScript Have Associative Arrays?
date: 2019-05-18 18:50:32
tags: Programming Language
---

If you have worked with PHP, you are certainly familiar with associative arrays. In C or Java, array indices start from 0, whereas in PHP, you can use strings as array indices in addition to numbers. Arrays with numeric indices are called indexed arrays, and arrays with string indices are called associative arrays. Both are valid arrays.

```PHP
<?php
$arr[0] = 1;        // Indexed array
$arr["a"] = "b";    // Associative array

echo $arr[0];       // 1
echo $arr["a"];     // b
```
In JavaScript, you can also use strings as array indices:

```JavaScript
let arr = []
arr[0] = 1
arr['a'] = 'b'
```
Yesterday, my pretty colleague and I encountered a puzzling issue when using arrays with string indices in JavaScript.

### The Origin

In Express.js framework's route handling, using `res.json()` to return an array, arrays with numeric indices are returned normally, but arrays with string indices always return empty. Here is a minimal code to describe the process:

```JavaScript
app.get('/', (req, res) => {
  let arr = []
  arr['a'] = 'b'

  console.log(arr)  // [a: 'b']
  res.json(arr)     // []
})
```
The expected returned array `arr` contains one element. The text content printed directly to the console with `console.log()` is `[a: 'b']`, which matches the expectation. However, if you make a page request to the route, the returned content is `[]`, which is puzzling. In other words, `res.json()` swallows the array content.

### Exploration

To find the true cause of the problem, I looked into the definition of `res.json()` in the framework:

```JavaScript
res.json = function json(obj) {
  var val = obj;
  // ...
  var body = stringify(val, replacer, spaces, escape)
  // ...
  return this.send(body);
};
```
The returned content `body` is processed by the `stringify()` method, which calls the `JSON.stringify()` method from JavaScript's standard JSON library:

```JavaScript
function stringify (value, replacer, spaces, escape) {
  var json = replacer || spaces
    ? JSON.stringify(value, replacer, spaces)
    : JSON.stringify(value);
  // ...
}
```
This means that the return value of `JSON.stringify()` will ignore array elements with string indices. To verify this phenomenon, a simple demo was tested:

```JavaScript
let arr1 = [], arr2 = []
arr1[0] = 1
arr2['a'] = 'b'

JSON.stringify(arr1)    // "[1]"
JSON.stringify(arr2)    // "[]"
```
So the question arises: why does JavaScript's standard library method `JSON.stringify()` ignore elements with string indices in arrays? Is it intentional, because the official stance is against using strings as indices, or is it due to unavoidable reasons that prevent implementation? To find the root cause, I tried to locate the definition of `JSON.stringify()` in Chrome's JavaScript engine, [V8](https://github.com/v8/v8).

V8 engine is written in C++, and the definition of `JSON.stringify()` should be this segment of code:

```JavaScript
// ES6 section 24.3.2 JSON.stringify.
BUILTIN(JsonStringify) {
  HandleScope scope(isolate);
  JsonStringifier stringifier(isolate);
  Handle<Object> object = args.atOrUndefined(isolate, 1);
  Handle<Object> replacer = args.atOrUndefined(isolate, 2);
  Handle<Object> indent = args.atOrUndefined(isolate, 3);
  RETURN_RESULT_OR_FAILURE(isolate,
                           stringifier.Stringify(object, replacer, indent));
}
```
It can be inferred that `object` is the content processed and returned by `JSON.stringify()`, which is wrapped by the `args.atOrUndefined()` method before returning. Here, `atOrUndefined()` is called repeatedly with two parameters. It can be understood that the first parameter `isolate` holds complete parameter information, and the second parameter is the data index. Combined, the `atOrUndefined()` method processes the complete data.

Then, looking at the definition of `atOrUndefined()`, it calls the `at()` method, which in turn calls the `Arguments` class's `at` method:

```C++
Handle<Object> atOrUndefined(Isolate* isolate, int index) {
  if (index >= length()) {
    return isolate->factory()->undefined_value();
  }
  return at<Object>(index);
}

Handle<S> at(int index) {
  DCHECK_LT(index, length());
  return Arguments::at<S>(index);
}
```
In the `Arguments::at()` method, the pointer `value` gets the memory address of the parameter to be processed, and then uses `reinterpret_cast` to perform a type conversion on the value of `value`.

```C++
Handle<S> at(int index) {
  Object** value = &((*this)[index]);
  // This cast checks that the object we're accessing does indeed have the
  // expected type.
  S::cast(*value);
  return Handle<S>(reinterpret_cast<S**>(value));
}
```
Here, the value is returned, but it does not explain why elements with string indices in arrays are ignored. As long as it is the same array, its values will be stored in a contiguous address space. Even if `reinterpret_cast` processes pointer variables, it should output everything normally.

### The Truth

Finally, through Google, I found a question and answer about using string indices in arrays ([String index in js array](https://stackoverflow.com/questions/10326635/string-index-in-js-array)), which explained why arrays with string indices are so special. This is because there are no associative arrays in JavaScript!

```JavaScript
let arr1 = [], arr2 = []
arr1[0] = 1
arr2['a'] = 'b'

arr1.length     // 1
arr2.length     // 0
```
When an array is assigned a value using a string as an index, the array length does not change, and the assigned value is not stored as an array element. The reason you can use strings as indices to get and set array values is that JavaScript stores the strings as properties of the array.

```JavaScript
let arr = []
arr['a'] = 'b'

arr.hasOwnProperty('a')   // true
```
Therefore, `JSON.stringify()` processes the contents of the array, and `reinterpret_cast` only performs type conversion based on pointers to the array content. Of course, properties will not be output!

### Follow-up

1. Why can `console.log()` output array properties? How is the content to be output defined?

2. Why is the value of `typeof []` in JavaScript `"object"`, meaning that the type of an array is an object, but the properties of an object are processed, while an array is not?
