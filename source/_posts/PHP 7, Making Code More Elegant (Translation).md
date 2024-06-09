---
title: PHP 7, Making Code More Elegant (Translation)
date: 2018-11-01 22:19:31
tags: Programming Languages
---

PHP 7 has been released for a long time, and it can make code more concise. Let's take a look at its features.

### Scalar Type Declarations

Scalars refer to string, int, float, and bool. Before PHP 7, to validate a function's parameter types, you needed to manually check and throw exceptions:

```php
<?php
function add($num1, $num2) {
    if (!is_int($num1)) {
        throw new Exception("$num1 is not an integer");
    }
    if (!is_int($num2)) {
        throw new Exception("$num2 is not an integer");
    }

    return ($num1 + $num2);
}

echo add(2, 4);     // 6
echo add(1.5, 4);   // Fatal error: Uncaught Exception
```
Now, you can directly declare the parameter types:

```php
<?php
function add(int $num1, int $num2) {
    return ($num1 + $num2);
}
echo add(2, 4);     // 6
echo add("2", 4);   // 6
echo add("something", 4);   // Fatal error: Uncaught TypeError
```
Since PHP runs in coercive mode by default, "2" is successfully parsed as 2. You can enable strict mode using the declare function:

```php
<?php
declare(strict_types=1);

function add(int $num1, int $num2) {
    return ($num1 + $num2);
}
echo add(2, 4);     // 6
echo add("2", 4);   // Fatal error: Uncaught TypeError
```
### Return Type Declarations

Just like parameters, return values can now have specified types:

```php
<?php
function add($num1, $num2): int {
    return ($num1 + $num2);
}

echo add(2, 4);     // 6
echo add(2.5, 4);   // 6
```
2.5 + 4 returns the int type 6, which is implicit type conversion. To avoid implicit conversion, you can use strict mode to throw exceptions:

```php
<?php
declare(strict_types=1);

function add($num1, $num2): int {
    return ($num1 + $num2);
}

echo add(2, 4); // 6
echo add(2.5, 4); // Fatal error: Uncaught TypeError
```
### Null Coalescing Operator

In PHP 5, to check a variable and assign a default value if it's not defined, you needed lengthy code:

```php
$username = isset($_GET['username']) ? $_GET['username'] : '';
```
In PHP 7, you can use the new "??" operator:

```php
$username = $_GET['username'] ?? '';
```
This is just syntactic sugar but it makes our code much simpler.

### Spaceship Operator

Also called the combined comparison operator, it compares two expressions. It returns -1, 0, or 1 when $a is less than, equal to, or greater than $b, respectively.

```php
echo 1 <=> 1;   // 0
echo 1 <=> 2;   // -1
echo 2 <=> 1;   // 1
```
### Grouped Namespace Declarations

Classes, functions, and constants within the same namespace can now be imported using a single use expression:

```php
<?php
// Before PHP 7
use net\smallyu\ClassA;
use net\smallyu\ClassB;
use net\smallyu\ClassC as C;

use function net\smallyu\funA;
use function net\smallyu\funB;
use function net\smallyu\funC;

use const net\smallyu\ConstA;
use const net\smallyu\ConstB;
use const net\smallyu\ConstC;

// PHP 7
use net\smallyu\{ClassA, ClassB, ClassC};
use function net\smallyu\{funA, funB, funC};
use const net\smallyu\{ConstA, ConstB, ConstC};
```
### Generator Features

Generators in PHP have the same form as regular functions. Generators are used in foreach iterations and are more memory-efficient than arrays. Here is an example of a generator:

```php
<?php
// Returns a generator
function getValues($max) {
    for ($i = 0; $i < $max; $i++) {
        yield $i * 2;
    }
}

// Using the generator
foreach(getValues(99999) as $value) {
    echo "Values: $value \n";
}
```
In the code, the yield expression appears, which acts like return, returning a value from the function once and resuming from where it left off.

Before PHP 7, generators couldn't use return to return a value. Now, they can, and return doesn't affect yield's normal iteration. The return value can also be retrieved using $gen->getReturn():

```php
<?php
$gen = (function() {
    yield "First Yield";
    yield "Second Yield";

    return "return Value";
})();

foreach ($gen as $val) {
    echo $val, PHP_EOL;
}

echo $gen->getReturn();
```
PHP 7 also supports generator delegation, allowing one generator to call another generator:

```php
<?php
function gen() {
    yield "yield 1 from gen1";
    yield "yield 2 from gen1";
    yield from gen2();
}

function gen2() {
    yield "yield 3 from gen2";
    yield "yield 4 from gen2";
}

foreach (gen() as $val) {
    echo $val, PHP_EOL;
}
```
### Anonymous Classes

PHP 7 also introduces anonymous classes.

### Closures

PHP 7 offers better support for closures:

```php
<?php
class A { private $x = 1; }

// Before PHP 7
$getAFun = function() { return $this->x; };
$getA = $getAFun->bindTo(new A, 'A'); // Intermediate closure
echo $getA();

// PHP 7 and later
$getA = function() { return $this->x; };
echo $getA->call(new A);
```
### Nullable Types

Nullable types are one of the new features in PHP 7.1. By adding a question mark before a type declaration, the parameter can be of the specified type or NULL. This can be used for return types:

```php
<?php

function testReturn(): ?string {
    return 'testing';
}
var_dump(testReturn());     // string(7) "testing"

function testReturn2(): ?string {
    return null;
}
var_dump(testReturn2());    // NULL
```
It can also be used for parameter types:

```php
<?php
function test(?string $name) {
    var_dump($name);
}

test('testing');    // string(7) "testing"
test(null);         // NULL
test();     // Fatal error: Uncaught ArgumentCountError
```
### Array Destructuring

Simplified list() syntax:

```php
<?php

$records = [
    [1, 'smallyu'],
    [2, 'bigyu'],
];

// list() style
list($firstId, $firstName) = $records[0];

// [] style, PHP 7.1
[$firstId, $firstName] = $records[0];

var_dump($firstId);     // int(1)
var_dump($firstName);   // string(7) "smallyu"
```
Another new feature is that both list() and [] now support keys:

```php
<?php

$records = [
    ["id" => 1, "name" => 'smallyu'],
    ["id" => 2, "name" => 'bigyu'],
];

// list() style
list("id" => $firstId, "name" => $firstName) = $records[0];

// [] style, PHP 7.1
["id" => $firstId, "name" => $firstName] = $records[0];

var_dump($firstId);     // int(1)
var_dump($firstName);   // string(7) "smallyu"
```
### References

- "Building RESTful Web Services with PHP 7" Chapter 2: PHP 7, To Code It Better

- [PHP: New Features - Manual](http://php.net/manual/en/migration70.new-features.php)
