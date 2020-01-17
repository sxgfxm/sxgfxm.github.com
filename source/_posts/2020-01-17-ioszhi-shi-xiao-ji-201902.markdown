---
layout: post
title: "iOS知识小集-201902"
date: 2020-01-17 11:14:35 +0800
comments: true
categories: iOS
keywords: sxgfxm
description: Dart
---

## 2019.02.15

Dart 语法基础：  

```dart
//	Impport library: import 'dart:xxx' / 'package:xxx'
//	Specifying a library prefix: import 'package:lib2/lib2.dart' as lib2;
//	Importing only part of a library: import 'package:lib1/lib1.dart' show/hide foo;
//	Lazily loading a library: import 'package:greetings/hello.dart' deferred as hello;

import 'dart:math';

void main() {
  testVariables();
  testBuiltInTypes();
  testFunctions();
  testOperators();
  testControlFlow();
  testExceptions();
  testClasses();
  testGenerics();
  testAsynchrony();
  test();
}

//	Variables
void testVariables() {
  print("Test Variables\n");

  //	Constants
  final name = "Light";
  const age = 26;
  print("name: $name  age: $age");

  //	Variables
  var weather = "clear";
  weather = "cloudy";
  print("weather: $weather");

  print("\n");
}

//	Built-in types
void testBuiltInTypes() {
  print("Test Built-in types\n");

  //	Numbers
	var integer = 2;
  var pi = 3.1415926;
  var one = int.parse('1');
  assert(one == 1);
  var onePointOne = double.parse('1.1');
  assert(onePointOne == 1.1);
  var twoAsString = integer.toString();
  assert(twoAsString == '2');
  var piAsString = pi.toStringAsFixed(2);
  assert(piAsString == '3.14');
  assert((3 << 1) == 6);
  assert((3 >> 1) == 1);
  assert((3 | 4) == 7);

  //	Strings
  var s1 = 'hello dart !';
  var s2 = "how are you";
  print('say hello: $s1');
  print('say grate: ${s2.toUpperCase() + ' TOM ?'}');
  var s3 = '''

  multy line string
  multy line string
  ''';
  print(s3);
  var s4 = 'In a raw string, not even \n gets special treatment';
  print('not raw string: $s4');
  var s5 = r'In a raw string, not even \n gets special treatment';
  print('raw string: $s5');

  //	Booleans
  var fullName = '';
  assert(fullName.isEmpty);
  var hitPoints = 0;
  assert(hitPoints <=0);
  var unicorn;
  assert(unicorn == null);
  var iMeantToDoThis = 0 / 0;
  assert(iMeantToDoThis.isNaN);

  //	Lists
  var list = [1, 2, 3];
  print('list: $list');
  print('list length: ${list.length}');

  //	Maps
  var personInfo = {
    'name': 'Light',
    'age': '26',
    'sex': 'male'
  };
  print('map: $personInfo');
  personInfo['birthday'] = '1992.12.09';
  print('map: $personInfo');
  print('map length: ${personInfo.length}');

  //	Runes
  var clapping = '\u{1f44f}';
  print('clapping: $clapping');
  print('clapping code units: ${clapping.codeUnits}');
  print('clapping runes: ${clapping.runes.toList()}');
  Runes input = new Runes('hello \u2665  \u{1f605}  \u{1f60e}  \u{1f47b}  \u{1f596}  \u{1f44d}');
  print('runes: $input');
	print('string from runes: ${new String.fromCharCodes(input)}');

  //	Symbols
  print(#radix);
  print(#bar);

  print("\n");
}

//	Functions
void testFunctions() {
  print("Test Functions\n");

  //	Optional parameters
  	//	Optional named parameters {}
  	//	Optional positional parameters []
  	//	Default parameter values = compile time const

  //	The main() function
  	//	a top-level function, which serves as the entrypoint to the app

  //	Function as first-class objects
  	//	pass a function as a parameter to another function
  	//	assign a function to a variable

  //	Anonymous functions
  	//	([Type] param1[, ...]) { codeBlock}

  //	Lexical scope
  	//	"follow the curly braces outwards" to see if a variable is in scope

  //	Lexical closures
  	//	A closure is a function object that has access to variables in its lexical scope

  //	Return values
  	//	All function return a value. default return value is null

  print("\n");
}

//	Operators
void testOperators() {
  print("Test Operators\n");

  print('5 / 2 = ${5 / 2}');
  print('5 ~/ 2 = ${5 ~/ 2}');
  print('5 % 2 = ${5 % 2}');
  //	as is is!
  //	??=
  //	^ ~expr
  //	expr1 ?? expr2
  //	. ?. ..

  print("\n");
}

//	Control flow statements
void testControlFlow() {
  print("Test Control flow\n");

  //	for loops
  var message = StringBuffer('Dart is fun');
  for (var i = 0; i < 5; i++) {
    message.write('!');
  }
  print(message);
  //	for in
  for (var x in [1, 2, 3]) {
    print(x);
  }
  //	forEach
  ['a', 'b', 'c'].forEach((char) => print(char));

  //	switch
  var command = 'CLOSED';
	switch (command) {
    case 'CLOSED':
      print('Closed');
      continue open;
    open:
    case 'OPEN':
      print('Open');
      break;
    default:
      print('Default');
  }

  print("\n");
}

//	Exceptions
void testExceptions() {
  print("Test Exceptions\n");

  //	throw
  //	rethrow
  //	try on catch finally

  print("\n");
}


class Point {
  //	instantce variables
  num x, y;

  //	static variables
  static var pointColor = Color.red;

  //	convenient constructors
  Point(this.x, this.y);

  //	named constructors with initializer list
  Point.origin()
    : x = 0,
  		y = 0 {
    print('create point (0, 0)');
  }

  //	redirecting constructors
  Point.alongXAxis(num x) : this(x, 0);

  //	instance method
  distanceTo(Point p) => sqrt((this.x - p.x) * (this.x - p.x) + (this.y - p.y) * (this.y - p.y));

  //	getter
  get distanceToOrigin => distanceTo(Point.origin());

  //	static method
  static num distanceBetween(Point a, Point b) {
    var dx = a.x - b.x;
    var dy = a.y - b.y;
    return sqrt(dx * dx + dy * dy);
  }
}

abstract class Doer {
  // Define instance variables and methods...

  void doSomething(); // Define an abstract method.
}

class EffectiveDoer extends Doer {
  void doSomething() {
    // Provide an implementation, so the method is not abstract here...
  }
}

// A person. The implicit interface contains greet().
class Person {
  // In the interface, but visible only in this library.
  final \_name;

  // Not in the interface, since this is a constructor.
  Person(this.\_name);

  // In the interface.
  String greet(String who) => 'Hello, $who. I am $\_name.';
}

// An implementation of the Person interface.
class Impostor implements Person {
  get \_name => '';

  String greet(String who) => 'Hi $who. Do you know who I am?';
}

String greetBob(Person person) => person.greet('Bob');

enum Color { red, green, blue }

//	Classes
void testClasses() {
  print("Test Classes\n");

  //	Using constructors
  var p = Point.origin();

  //	Using class members
  //	Instance variables
  print('point: $p');
  print('distance between ${p.toString()} and (3, 4): ${p.distanceTo(Point(3, 4))}');

  //	Getting an objects's type
  print('The type of 1 is ${1.runtimeType}');

  //	Constructors
  	//	Constructors aren’t inherited
  	//	Invoking a non-default superclass constructor
      //	initializer list
      //	superclass's no-arg constructor
      //	main class's no-arg constructor
  	//	Specify the superclass constructor after a colon (:), just before the constructor body.
  	//	Initializer list
  	//	Redirecting constructors
  	//	Factory constructors, have no access to this

  //	Methods
  print('Point (5, 6) distance to origin: ${Point(5, 6).distanceToOrigin}');

  //	Abstract classes
  	//	Abstract methods can only exist in abstract classes.
  	//	Abstract classes are useful for defining interfaces, often with some implementation.

  //	Implicit interfaces
  print(greetBob(Person('Kathy')));
  print(greetBob(Impostor()));

  //	Extends a class
  	//	extends super methods
  	//	override super methods
  	//	override operators
  	//	noSuchMethod()

  //	Enumerated types
  print(Color.values);
  print(Color.red);
  print(Color.red.index);

  //	Adding features to a class: mixins
  	//	Mixins are a way of reusing a class’s code in multiple class hierarchies

  //	Class variables and methods
  print('Point color: ${Point.pointColor}');
	print('distance between (2, 6) and (3, 4): ${Point.distanceBetween(Point(2, 6), Point(3, 4))}');

  print("\n");
}

//	Generics
void testGenerics() {
  print("Test Generics\n");

  //	Using collection literals
  var names = <String>['Seth', 'Kathy', 'Lars'];
  var pages = <String, String>{
    'index.html': 'Homepage',
    'robots.txt': 'Hints for web robots',
    'humans.txt': 'We are people, not machines'
  };
  print(names);
  print(pages);
  print(names.runtimeType);

  //	Restricting the parameterized type
  //	Using generic methods

  print("\n");
}

//	Asynchrony support
void testAsynchrony() {
  print("Test Asynchrony\n");

  //	Handling Futures
  //	To use await, code must be in an async function—a function marked as async

  //	Declaring async functions
  //	An async function is a function whose body is marked with the async modifier

  //	Handling Streams
  //

  print("\n");
}



//
void test() {
  print("Test \n");

  print("\n");
}
```