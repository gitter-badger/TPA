## TPA - Total Precision Arithmetic

[![npm](https://img.shields.io/npm/v/npm.svg)](https://www.npmjs.com/package/TPA)
[![Build Status](https://secure.travis-ci.org/dthwaite/TPA.png)](http://travis-ci.org/dthwaite/TPA)
[![Coverage Status](https://coveralls.io/repos/github/dthwaite/TPA/badge.svg?branch=master)](https://coveralls.io/github/dthwaite/TPA?branch=master)
[![bitHound Overall Score](https://www.bithound.io/github/dthwaite/TPA/badges/score.svg)](https://www.bithound.io/github/dthwaite/TPA)
[![bitHound Code](https://www.bithound.io/github/dthwaite/TPA/badges/code.svg)](https://www.bithound.io/github/dthwaite/TPA)

##### tpa.js performs basic arithmetic operations with total precision.

Available on [GitHub](https://github.com/dthwaite/TPA), details on [JSDocs](http://dthwaite.github.io/tpa/docs/). See it working: [Demonstration](http://dthwaite.github.io/tpa/)

The main features are:

* Simplicity - one library, add/subtract/multiply/divide
* Performance - optimised to perform operations very, very fast
* Limitless - represents and operates on rational numbers of any size and precision
* Expressive - inputs/outputs numbers using decimal or fractional forms
* Quality - comprehensively tested and documented

For a terse list of methods go to the end of this readme. The usage section below is more descriptive.

There are many similar libraries available. I wrote this one partly as an exercise. But I also wrote it because it achieves all the features listed above as well as, or considerably better (IMHO ;-), than the others.

Installation:
`npm install`

Test:
`npm test`

Coverage:
`npm run coverage`
`open coverage/lcov-report/index.html`

To use in Node:
```javascript
var Tpa = require('Tpa');

var n=new Tpa(100.123);
console.log(n.toString()); // Outputs '123.123'
```
To use in the browser:
```javascript
<script src ="https://cdn.rawgit.com/dthwaite/TPA/V1.0.1/lib/tpa.min.js"></script>
<script>
var n=new Tpa(100.123);
console.log(n.toString()); // Outputs '123.123'
</script>
```

### Usage

#### Set up
```javascript
var n1=new Tpa();                // new integer set to zero
var n2=new Tpa(123);             // new integer set to 123
var n3=new Tpa(213.5);           // new fraction set to 123.5
var n4=new Tpa('123');           // new integer set to 123
var n5=new Tpa('123.3[3]');      // new fraction set to 123 1/3
var n6=new Tpa('123 1/3');       // new fraction set to 123 1/3
var n7=new Tpa('-4 538/1284');   // new fraction set to to -4.41900311...
var n8=new Tpa('-.2[512]');      // new fraction
n8.set(-9);                      // Sets an existing number to a new value
n8.set();                        // resets an existing number to zero
n8.set('-4 538/1284');           // resets an existing number 4.41900311...
n8.set(n2);                      // Sets an existing number to equal another (takes a copy)
```
As can be seen above, setting a number with a string representation is the best way as you can express any rational number with complete accuracy using either a decimal form (with optional recurring digits) or a fractional form.
#### Outputs
Numbers can be output in decimal (`toDecimal()`) or fractional (`toFraction()`) form. Decimal places are limited to 100 unless specified in the `toString()` or `toDecimal()` methods.
```javascript
console.log(n1.toString());      // '0'
console.log(n2.toString());      // '123'
console.log(n2.value());         // 123.0
console.log(n3.toString());      // '123.5'
console.log(n3.toDecimal());     // '123.5' (alias for toString())
console.log(n3.toFraction());    // '123 5/10'
n3.simplify();
console.log(n3.toFraction());    // '123 1/2'
console.log(n5.toDecimal());     // '123.[3]
console.log(n5.toFraction());    // '123 30/90'
console.log(n7.toFraction());    // '-4 538/1284'
n7.simplify();
console.log(n7.toFraction());    // '-4 269/642'
console.log(n7.toDecimal());     // '-4.4[19003115264797507788161993769470404984423676012461059]'
console.log(n7.toDecimal(20));   // '-4.41900311526479750778...' (limit dp's to 20)
```
Note that there is a `value()` method that gives the number as a javascript floating point number. This will clearly be an approximation in many cases.
#### Operations
Methods `add()`, `subtract()`, `multiply()` and `divide()` all operate in-situ on the number on which they are called. They return the number to allow for chaining of operations. Each takes a parameter that may either be an existing `Tpa` object (which is not changed) or a number or string that is a valid representation. Aliases for the above are: `plus()`, `sub()`, `minus()`, `mult()`, `times()`, `div()`.
```javascript
console.log(n2.add(n2).toString());         // '246'
console.log(n2.subtract(123).toString());   // '123'
n2.subtract('200');
console.log(n2.toDecimal());                // '-77'
n2.add(new Tpa(200));
console.log(n2.toDecimal());                // '123'
console.log(n5.multiply(n3).toString());    // '26331.[6]' (123 3/9 * 123.5)
n5.divide(n3);
console.log(n5.toString());                 // '123.[3]'
n5.subtract('23 1/3').divide(2).add('48 2/1').divide(-100);
console.log(n5.toString());                 // '-1'
```
#### Integer and Fractions
Tpa numbers are declared either *integer* or *fractional*. If integer then all operations performed on them will only use the integer part of their operands. Whether a number is integer or fractional is inferred from its initialisation. But you can force the issue by passing `true` (for integer) or `false` (for fractional) as an additional parameter to the `Tpa` constructor
```javascript
var a=new Tpa(3);                           // Constructs a to be integer
var b=new Tpa(7.8);                         // Constructs b to be fractional
a.add(b);
console.log(a.toString());                  // '10' (a is an integer and ignores fractional operands)
var c=new Tpa(3,false);                     // Explicitly set a to be fractional
c.add(b);
console.log(c.toString());                  // '10.8' (c is fractional and so operates on fractional operands)
var d=new Tpa(b,true);                      // Explicitly set d to be integer
console.log(d.toString());                  // '7' (d was constructed to ignore any fractional part)
var e=new Tpa('23 100/23',true);            // Explicitly set e to be integer
console.log(e.value());                     // 27 (e took on the integer evaluation of the initialising string)
console.log(e.set(3,false).value());        // Sets an existing number to a new value and to be fractional
```
You can find out what type a number is with the `isInteger()` and `isFractional()` methods and you can convert a number to one or other representation with the `makeInteger()` and `makeFractional()` methods:
```javascript
var a=new Tpa('33 2/3');
console.log(a.isInteger());                 // false
console.log(a.makeInteger().value());       // 33
console.log(a.isInteger());                 // true
var b=new Tpa(10);
console.log(b.makeFractional().subtract(11.5).value()); // -1.5
console.log(b.isFractional());                 // true
console.log(b.makeInteger().toDecimal());    // '-1'
```
The reason Tpa makes a distinction is that a common requirement is simply to deal with integers. Processing fractions for the four main operations is a significant overhead. In fact it can quite quickly lead to massive numerators and denominators in fractional parts of numbers in order to maintain total precision. Keep this in mind.

Fractions are never automatically simplified. However, the `simplify()` method makes its best attempt to simplify the fractional part of a number. Reducing a very large fraction is compute intensive (just as well because most encryption mechanisms rely on this fact!) as it essentially involves trying to find common prime factors.
```javascript
var n=new Tpa('1/3');
n.multiply('3/5').multiply('9/7').multiply('23/45').multiply('12 45/87').divide('99.75');
console.log(n.toString(25));                // '0.0164924626838031038186599...'
console.log(n.toFraction());                // '0 67626900/4100473125'
console.log(n.simplify());                  // true - indicates that simplification was fully achieved
console.log(n.toFraction());                // '0 11132/674975'
n=new Tpa('234789789167435342333343/4239123411142533478912');
console.log(n.simplify());                  // false - defaults to 100 ms which is probably not enough time
console.log(n.toFraction());                // '55 1638001554596000993183/4239123411142533478912'
console.log(n.simplify(0));                 // true - achieved full simplification
console.log(n.toFraction());                // '55 1638001554596000993183/4239123411142533478912'
```
It is often the case that the fractional form is more terse than a decimal read out. The decimal form of the number resulting from that chain of computations has a recurring decimal section of 252 digits while the simplified fraction involves considerably less digits.

The `simplify()` method does not look for any common prime factors above 33,554,393. It also limits its computation to 100 milliseconds. You can bypass this by passing in the number of milliseconds you are prepared to wait or 0 to indicate no limit. The system builds up its own inventory of prime numbers and it may take several seconds to simplify the first time (assuming you permit it) as it creates this inventory. But subsequent simplifications will generally be achieved within one second. `simplify()` returns true if the fraction has been fully simplified otherwise it may or may not have been fully simplified as either the fraction may have a common factor above 33,554,393 or time has run out.
#### Comparisons
There are a selection of comparison methods, namely: `isZero()`, `isPositive()`, `isNegative()`, `lt()`, `lte()`, `gt()`, `gte()` and  `eq()`
```javascript
var a=Tpa(3);
var b=Tpa(3.5);
var c=Tpa('4 1/4');
var d=Tpa('3 5/4');
var f=Tpa();
console.log(a.isZero());            // false
console.log(a.isPositive());        // true
console.log(f.isPositive());        // false (it's zero)
console.log(b.isNegative());        // false
console.log(a.lt(b));               // false (a is an integer and ignores fractional operands)
console.log(a.lt(c));               // true
console.log(d.lte(c));              // true (they are equal)
console.log(d.gte(c));              // true (ditto)
console.log(d.gt(c));               // false
console.log(d.eq(c));               // true
```
#### Other methods

* `sign()` returns -1, 0 or 1 if the number is negative, zero or positive respectively
* `hasFraction()` return true if the number has a non zero fractional part
* `frac()` removes the integer value from the number
* `int()` removes the fractional value from the number
* `modulus()` set this number to the modulus of the number passed in
* `abs()` set this number to its absolute value

```javascript
console.log(Tpa(-3).sign());                        // -1
console.log(Tpa(3.3).hasFraction());                // true
console.log(Tpa('-3 1/3').frac().toFraction());     // '-0 1/3'
console.log(Tpa('-3 1/3').int().toFraction());      // '-3'
console.log(Tpa(22).modulus(3).toString());             // '1'
console.log(Tpa(-33.5).abs().value());              // 33.5
```
#### Static methods
Typically the arithmetical operations change the number on which they are called. Alternatively you can choose to not mutate existing numbers to return a new number which is the result of the operation. This is achieved with static functions as follows:
* `Tpa.add(a,b)` adds a and b and returns the result in a new number (aliases: `plus()`)
* `Tpa.subtract(a,b)` subtracts b from a and returns the result in a new number (aliases: `sub()` & `minus()`)
* `Tpa.multiply(a,b)` multiplies two numbers and returns the result in a new number (aliases: `times()` & `mult()`)
* `Tpa.divide(a,b)` divides a by b and returns the result in a new number (aliases: `div()`)
* `Tpa.modulus(a,b)` performs a modulus b and returns the result in a new number (aliases: `mod()`)
* `Tpa.frac(a)` takes the fractional part of a and returns it in a new number
* `Tpa.int(a)` takes the integer part of a and returns it in a new number
* `Tpa.abs(a)` takes the absolute value of a and returns it in a new number

```javascript
var a=Tpa(5);
var b=Tpa(12.5,false);
console.log(Tpa.add(a,b).value());        // 17
console.log(Tpa.subtract(a,b).value());   // -7
console.log(Tpa.multiply(a,b).value());   // 60
console.log(Tpa.divide(b,a).toFraction());// '2 25/50'
console.log(Tpa.modulus(a,b).value());        // 5
console.log(Tpa.frac(b).value());         // 0.5
console.log(Tpa.int(b).value());          // 12
console.log(Tpa.abs(-23).value());        // 23
```
Note that for methods that take two arguments the first one dictates whether the result is integer or fractional. Thus the subtraction of 12.5 from 5 yields 7 as the operation is only working on integer parts. As opposed to the division that takes the type of b which is fractional.
### Performance
I invested some effort in ensuring speed of operations. I have performed extensive comparisons with other libraries and can confidently say that this library is orders of magnitude faster in many areas. I have measured performance against two "competitors" for add, subtract, multiply and divide using numbers of various sizes ranging from a few decimal digits long to tens of thousands. Performance can also vary according to the order in which these numbers are presented. I have optimised in all these permutations and I have detailed timings for each. I present here a short summary (this all for integer values only):

##### Addition & subtraction

* Around 5 million per second for either number < 500 digits
* Around 1 million per second for both numbers in the region of 1,000 digits
* Around 150 thousand per second for both numbers in the region of 10,000 digits

##### Multiplication

* Around 10 million per second for small numbers (<10 digits)
* Around 1 million per second for both numbers in the region of 25 digits
* Around 1/2 million per second for both numbers in the region of 100 digits
* Around 10 thousand per second for both numbers in the region of 1,000 digits
* Around 150 per second for both numbers in the region of 10,000 digits

##### Division

* Around 2-3 million per second for both numbers < 25 digits
* Around 1 million per second for both numbers in < 100 digits
* Around 300 thousand per second dividing 1,000 digit numbers by <8 digit numbers
* Around 40 thousand per second dividing 10,000 digit numbers by <8 digit numbers
* Around 15 thousand per second for dividing 1,000 digit numbers by 100 digit numbers
* Around 1 thousand per second for dividing 10,000 digit numbers by 100 digit numbers
* Around 250 per second for dividing 10,000 digit numbers by 1,000 digit numbers

See `performance.txt` for full details of a test that compared this library (referred to as `Dom`) with two others (referred to `Alex` and `Mike`).

### Method index
Construction and mutators take numbers as parameters in the following forms:

1. Tpa object
2. Javascript number
3. Javascript string (decimal or fractional format)
  
##### Construction & setting
* `new Tpa()` or `Tpa()`
* `set()`

##### Mutators
* Unary
    * `frac()`
    * `int()`
    * `abs()`
    * `makeInteger()`
    * `makeFractional()`

* Binary
    * `add()` or `plus()`
    * `subtract()` or `sub()` or `minus()`
    * `multiply()` or `mult()` or `times()`
    * `divide()` or `div()`
    * `modulus()` or `mod()`

##### Enquirers
* Unary
    * `sign()`
    * `hasFraction()`
    * `isZero()`
    * `isPositive()`
    * `isNegative()`
    * `isInteger()`
    * `isFractional()`
* Binary
    * `lt()`
    * `lte()`
    * `gt()`
    * `gte()`
    * `eq()`

##### Output
* `toDecimal()` or `toString()`
* `toFraction()`
* `value()`

##### Miscellaneous
* `simplify()`

Note that all mutators are available as static methods to preserve the original value as per this example
```javascript
var x=Tpa(100);
var y=Tpa(50);
Tpa.divide(x,y);         // Returns a new number = x/y, x and y remain unchanged
x.divide(y);             // Returns x having been divided by y, only y remains unchanged
```







