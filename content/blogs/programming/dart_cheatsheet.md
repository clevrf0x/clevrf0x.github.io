+++
title = 'Dart Cheatsheet'
date = 2022-10-05T16:25:54+05:30
description = "This cheatsheet provides a comprehensive overview of the Dart programming language, covering essential syntax, data structures, and core features."

# Author
author = "Favas M"
authorTwitter = "clevrf0x"

# Categories and Tags
categories = ["Programming", "Dart"]
tags = ["dart", "coding", "reference"]

aliases = ["/dart-guide", "/learn-dart"]

# Series
series = ["Programming Languages"]
series_weight = 1

# Localization
language = "en"

# SEO
keywords = ["dart", "programming", "cheatsheet"]
seoTitle = "Dart Programming Language Cheatsheet"
+++

## Basic Program

In Dart Programming Language, main() function is the entry point for all function.

```dart
  void main() {
    print('Hello World');
  }
```

## Variables and Data Types

> If a variable is being initialized during declaration, it is considered better practice to use the var keyword instead of explicitly typing the variable. However, if a variable is not being initialized during declaration, giving it an explicit type is considered good practice.

### final vs const

In Dart, final and const are used for declaring immutable variables.

The differences between final and const are:

final variables can be set once and their value is evaluated at runtime.
const variables are evaluated at compile-time and are immutable throughout the program's execution.
const can only be used with values that are known at compile-time, while final can be used with values that are determined at runtime.

```dart
  // Numbers
  int age = 30;
  double height = 1.75;
  num weight = 68.5; // both int and double will be valid
  print('Age: $age, Height: $height, Weight: $weight');

  // Strings
  String name = 'Alice';
  String message = 'Hello, $name!';
  print(message);

  // Booleans
  bool isRaining = true;
  bool isSunny = false;
  print('Is it raining? $isRaining');
  print('Is it sunny? $isSunny');

  // Dynamic (no fixed type, any value can be assigned)
  dynamic value = 42;
  print('Value: $value');
  value = 'hello';
  print('Value: $value');

  // Object
  Object obj = 'hello';
  print(obj.runtimeType);
  obj = 42;
  print(obj.runtimeType);

  // Final (value can change during runtime)
  final DateTime now = DateTime.now();

  // Const (compile time constant)
  const String homePlanet = 'Earth';
  const int daysInWeek = 7;
  print('Home planet: $homePlanet');
  print('Days in week: $daysInWeek');

  // Var (automatically type inferred)
  var isWindy = true;
  var temperature = 25.0;
  print('Is it windy? $isWindy');
  print('Temperature: $temperature');
}
```

## Collections

### Map

- Stores data as key-value pairs
- Each key in the map must be unique
- Can be created with or without type annotations
- Accessed using keys
- Supports operations such as adding and removing key-value pairs

```dart
  // Map: The Map object is a key and value pair.
  // Keys and values on a map may be of any type. It is a dynamic collection.
  // Creating a map with type annotations
  Map<String, int> ages = {
    'Alice': 30,
    'Bob': 25,
    'Charlie': 35,
  };
  print(ages); // {"Alice": 30, "Bob": 25, "Charlie": 35}

  // Creating a map without type annotations
  var salaries = {
    'Alice': 50000,
    'Bob': 60000,
    'Charlie': 70000,
  };
  print(salaries); // {"Alice": 50000, "Bob": 60000, "Charlie": 70000}

  // Accessing a value by key
  print(ages['Alice']); // 30
  print(salaries['Bob']); // 60000

  // Adding a key-value pair to the map
  ages['David'] = 40;
  print(ages); // {"Alice": 30, "Bob": 25, "Charlie": 35, "David": 40}

  // Removing a key-value pair from the map
  salaries.remove('Charlie');
  print(salaries); // {"Alice": 50000, "Bob": 60000}

  // Checking if a map contains a key
  print(ages.containsKey('Alice')); // true
  print(salaries.containsKey('Charlie')); // false

  // Iterating over a map using a for loop
  for (var entry in ages.entries) {
    print('${entry.key}: ${entry.value}');
  }
  // Output:
  // "Alice: 30"
  // "Bob: 25"
  // "Charlie: 35"
  // "David: 40"

  // Iterating over a map using forEach() method
  salaries.forEach((key, value) => print('$key earns $value dollars'));
  // Output:
  // "Alice earns 50000 dollars"
  // "Bob earns 60000 dollars"
```

### List

- Stores data as an ordered collection of elements
- Elements can be of any data type
- Can be created with or without type annotations
- Accessed using index values
- Supports operations such as adding and removing elements

```dart
  // List: List data type is similar to arrays.
  // A list is used to represent a collection of objects.
  // It is an ordered group of objects.
  // Creating a list with type annotations
  List<String> fruits = ["apple", "banana", "orange"];
  print(fruits); // ["apple", "banana", "orange"]

  // Creating a list without type annotations
  var vegetables = ["carrot", "spinach", "broccoli"];
  print(vegetables); // ["carrot", "spinach", "broccoli"]

  // Adding an element to the list
  fruits.add("grape");
  print(fruits); // ["apple", "banana", "orange", "grape"]

  // Removing an element from the list
  vegetables.remove("spinach");
  print(vegetables); // ["carrot", "broccoli"]

  // Accessing an element in the list
  print(fruits[0]); // "apple"
  print(vegetables[1]); // "broccoli"

  // Checking if a list contains a value
  print(fruits.contains("apple")); // true
  print(vegetables.contains("spinach")); // false

  // Iterating over a list using a for loop
  for (var fruit in fruits) {
    print(fruit);
  }
  // Output:
  // "apple"
  // "banana"
  // "orange"
  // "grape"

  // Iterating over a list using forEach() method
  vegetables.forEach((vegetable) => print(vegetable));
  // Output:
  // "carrot"
  // "broccoli"
```

### Set

- Stores data as an unordered collection of unique elements
- Elements can be of any data type
- Cannot contain duplicate elements
- Can be created with or without type annotations
- Supports operations such as adding and removing elements

```dart
  // Using a Set with type annotations
  Set<int> mySet1 = {1, 2, 3};
  print(mySet1); // Output: {1, 2, 3}

  // Using a Set without type annotations
  var mySet2 = {'apple', 'banana', 'cherry'};
  print(mySet2); // Output: {apple, banana, cherry}

  // Adding an element to a Set
  mySet1.add(4);
  print(mySet1); // Output: {1, 2, 3, 4}

  // Removing an element from a Set
  mySet2.remove('banana');
  print(mySet2); // Output: {apple, cherry}

  // Iterating over a Set
  for (var element in mySet1) {
    print(element);
  }
  // Output:
  // 1
  // 2
  // 3
  // 4
```

> Overall, the choice between Map, List, and Set depends on the specific requirements of your application. Use a Map when you need to store data as key-value pairs and quickly look up values by their keys. Use a List when you need to store data in a specific order and access it by index values. Use a Set when you need to store a collection of unique elements and don't care about their order.

## String Interpolation

String interpolation is similar to python formatted strings

```dart
  // can use single or double qoutes for String type
  var firstName = 'Nicola';
  var lastName = "Tesla";
  print("My name is ${firstName} ${lastName}")

  //can embed variables in string with $
  String fullName = "$firstName $lastName";

  String upperCase = '${firstName.toUpperCase()}';
  print(upperCase); //Print: NICOLA

```

## Comments

```dart
  // This is a normal, one-line comment.

  /// This is a documentation comment, used to document libraries,
  /// classes, and their members. Tools like IDEs and dartdoc treat
  /// doc comments specially.

  /* Comments like these are also supported. */
```

## Imports

```dart
  // Importing a library from a specific file
  import 'my_library.dart';

  // Importing a library with a prefix
  import 'my_library.dart' as mylib;

  // Importing only specific parts of a library
  import 'my_library.dart' show MyClass, myFunction;

  // Importing everything except specific parts of a library
  import 'my_library.dart' hide MyClass;

  // Importing built-in Dart libraries
  import 'dart:math';
  import 'dart:convert';

  // Importing libraries from external packages
  import 'package:test/test.dart';
```

## Operators

```dart
  // Arithmetic Operators
  int a = 10;
  int b = 3;
  print('a + b = ${a + b}');  // Addition
  print('a - b = ${a - b}');  // Subtraction
  print('a * b = ${a * b}');  // Multiplication
  print('a / b = ${a / b}');  // Division
  print('a ~/ b = ${a ~/ b}');  // Integer Division
  print('a % b = ${a % b}');  // Modulo
  print('a++ = ${a++}');  // Post-Increment
  print('++b = ${++b}');  // Pre-Increment
  print('a-- = ${a--}');  // Post-Decrement
  print('--b = ${--b}');  // Pre-Decrement

  // Equality Operators
  int x = 5;
  int y = 5;
  int z = 6;
  print('x == y is ${x == y}');  // Equal to
  print('x != z is ${x != z}');  // Not equal to

  // Relational Operators
  int p = 10;
  int q = 5;
  print('p > q is ${p > q}');  // Greater than
  print('p < q is ${p < q}');  // Less than
  print('p >= q is ${p >= q}');  // Greater than or equal to
  print('p <= q is ${p <= q}');  // Less than or equal to

  // Logical Operators
  bool r = true;
  bool s = false;
  print('r && s is ${r && s}');  // Logical AND
  print('r || s is ${r || s}');  // Logical OR
  print('!r is ${!r}');  // Logical NOT

```

## Control Flows

### Conditionals

**If Statement**
This is used to execute a block of code only if a certain condition is met.

**If-Else Ladder**
This is used to check multiple conditions in a series of if-else blocks.

**Switch Statement**
This is used to evaluate a single expression against multiple possible values.

```dart
  // If Statement
  int a = 10;
  if (a > 0) {
    print('$a is positive');
  }

  // If-Else Ladder
  int b = 0;
  if (b > 0) {
    print('$b is positive');
  } else if (b < 0) {
    print('$b is negative');
  } else {
    print('$b is neither positive nor negative');
  }

  // Switch Statement
  String color = 'red';
  switch (color) {
    case 'red':
      print('The color is red');
      break;
    case 'green':
      print('The color is green');
      break;
    case 'blue':
      print('The color is blue');
      break;
    default:
      print('The color is not recognized');
  }
}
```

---

### Loops

1. **for loop**

   - A for loop is used to iterate over a range of values or a collection of elements. It has three parts: an initializer, a condition, and an updater. The loop runs as long as the condition is true, and the updater is executed after each iteration.

2. **while loop**

   - A while loop is used to repeatedly execute a block of code as long as a given condition is true. The condition is checked at the beginning of each iteration, and if it is false, the loop terminates.

3. **do-while loop**

   - A do-while loop is similar to a while loop, but the condition is checked at the end of each iteration instead of at the beginning. This means that the loop always executes at least once.

4. **forEach loop**

   - A forEach loop is used to iterate over the elements of a collection. It takes a function as an argument, and the function is executed for each element in the collection.

5. **for-in loop**
   - A for-in loop is used to iterate over the elements of a collection. It automatically iterates over each element in the collection, without the need for an index variable.

```dart
  // for loop (prints upto 5, including 5)
  for (int i = 1; i <= 5; i++) {
    print('for loop iteration $i');
  }

  // while loop (prints upto 5, including 5)
  int j = 1;
  while (j <= 5) {
    print('while loop iteration $j');
    j++;
  }

  // do-while loop (prints upto 6, including 6)
  int k = 1;
  do {
    print('do-while loop iteration $k');
    k++;
  } while (k <= 5);

  // forEach loop
  List<String> fruits = ['apple', 'banana', 'orange', 'kiwi'];
  fruits.forEach((fruit) => print('I like $fruit'));

  // for-in loop
  List<String> fruits = ['apple', 'banana', 'orange', 'kiwi'];
  for (String fruit in fruits) {
    print('I like $fruit');
  }
```

## Functions

In Dart, functions are first-class objects, which means that they can be treated like any other object, such as numbers or strings. This allows functions to be assigned to variables, passed as arguments to other functions, and returned as values from functions.

#### Assign function to a variable

> In this example, we define a function add that takes two integer arguments and returns their sum. We then assign the function to a variable myFunc using the function name, without calling it with parentheses. We can then call the function using the variable myFunc, passing in the arguments as usual.

```dart
  // Define a function
  int add(int a, int b) {
    return a + b;
  }

  // Assign the function to a variable
  var myFunc = add;

  // Call the function through the variable
  print(myFunc(2, 3)); // Output: 5
```

#### Passing a function as an argument to another function

> In this example, we define a function applyFunction that takes another function as its first argument, along with two integer arguments. The function simply calls the passed function with the integer arguments and prints the result. We also define another function subtract that takes two integer arguments and returns their difference. We then call applyFunction with subtract as the first argument, along with 5 and 3 as the other two arguments.

```dart
  // Define a function that takes another function as an argument
  void applyFunction(int Function(int, int) func, int a, int b) {
    print(func(a, b));
  }

  // Define a function to pass as an argument
  int subtract(int a, int b) {
    return a - b;
  }

  // Call applyFunction with the subtract function
  applyFunction(subtract, 5, 3); // Output: 2
```

#### Returning a function from another function

> In this example, we define a function makeGreeter that takes a String argument name and returns another function that takes no arguments and prints a greeting message using the name. We then call makeGreeter with "Bob" as the argument, which returns a function that greets "Bob". We store this function in a variable greetBob and then call it, which prints "Hello, Bob!" to the console.

```dart
  // Define a function that returns a function
  Function makeGreeter(String name) {
    return () => print("Hello, $name!");
  }

  // Call makeGreeter to get a function and store it in a variable
  var greetBob = makeGreeter("Bob");

  // Call the returned function
  greetBob(); // Output: Hello, Bob!
```

---

### Arrow Function

The arrow function syntax is a shorthand way of defining a function in Dart. Instead of using the function keyword, we can define a function using an arrow (=>) after the function signature. The arrow function syntax is useful for defining short, one-line functions, and can make our code more concise and easier to read.

```dart
  // Arrow function syntax
  int multiply(int a, int b) => a * b;

  // Usage
  print(multiply(2, 3)); // Output: 6
```

### Anonymous Function (lamda functions)

In Dart, an anonymous function is a function that is defined without a name. Instead of giving the function a name, we can assign it to a variable, which allows us to call the function by invoking the variable.

```dart
  // Define an anonymous function and assign it to a variable
  var square = (int x) {
    return x * x;
  };

  // Usage
  print(square(2)); // Output: 4
```

Anonymous functions are useful for defining functions that are only used once, or for passing functions as arguments to other functions. For example, we can pass an anonymous function as an argument to the map method of a List object to apply the function to each element of the list.

```dart
  var numbers = [1, 2, 3, 4, 5];

  // Use an anonymous function with the map method to square each element
  var squaredNumbers = numbers.map((int x) => x * x);

  // Output the squared numbers
  print(squaredNumbers); // Output: (1, 4, 9, 16, 25)
```

## Class

In Dart, a class is a blueprint for creating objects that encapsulate data and behavior. It defines the attributes (or properties) and methods that are common to all objects created from that class.

You can mark some of the object's data as private by prefixing it with an underscore ("\_"). This means that it should only be accessed or changed from within the class, not from outside.

```dart
  class Person {
    // Attributes (or properties) that each object will have.
    String name;
    int age;

    // Private property, which can be only accessed through getters and setters
    String _address;

    // A constructor initializes the attributes when an object is created.
    Person(this.name, this.age);

    // Methods define the behavior that each object will have.
    void sayHello() {
      print("Hello, my name is $name and I'm $age years old.");
    }
  }

  // You can create a new object from a class using the constructor
  // Creating objects in Dart 2 no longer requires the use of the "new" keyword.
  var john = new Person("John", 25);

  // You can access the object's attributes and call its methods
  // using the dot notation.
  print(john.name); // Output: John
  john.sayHello(); // Output: Hello, my name is John and I'm 25 years old.
```

### Abstract Class

An abstract class in Dart is a class that cannot be directly instantiated, but can be used as a base class for other classes. It can contain abstract methods, which are methods without an implementation, and concrete methods, which have a body.

An abstract class can define a common interface for a group of related classes, and can be used to enforce consistency among its subclasses. It can also be used to define a type that can be used to restrict function arguments.

```dart
  // An abstract class is a class that cannot be instantiated directly,
  // but can be used as a superclass.
  abstract class Animal {
    // Abstract methods define method signatures without implementation.
    void makeSound();
    void eat() { // Concrete methods can also be defined in an abstract class.
      print("The animal is eating.");
    }
  }

  // Subclasses of an abstract class must implement all abstract methods.
  class Cat extends Animal {
    void makeSound() => print("Meow!");
  }

  class Dog extends Animal {
    void makeSound() => print("Woof!");
  }

  // You can also use an abstract class to define a type that can be used
  // to restrict function arguments.
  void soundOff(Animal animal) => animal.makeSound();

  // You cannot create an instance of an abstract class directly,
  // but you can create instances of its subclasses.
  var cat = Cat();
  var dog = Dog();

  // You can call methods on the objects as usual, and pass them to functions
  // that take the abstract class as a parameter.
  cat.makeSound(); // Output: Meow!
  dog.makeSound(); // Output: Woof!

  soundOff(cat); // Output: Meow!
  soundOff(dog); // Output: Woof!
```

### Getters and Setters

In Dart, getters and setters are special methods that allow you to control access to an object's properties. Getters retrieve the current value of a property, while setters change the value of a property.

```dart
  class Person {
    // Private attributes can only be accessed through getters and setters.
    String _name;
    int _age;

    // Getters retrieve the current value of an attribute.
    String get name => _name;
    int get age => _age;

    // Setters change the value of an attribute, with optional validation logic.
    set name(String value) => _name = value;
    set age(int value) => _age = value > 0 ? value : 0;

    Person(this._name, this._age);
  }

  // You can use getters and setters to control access to an object's properties.
  var john = new Person("John", 25);
  print(john.name); // Output: John
  john.age = -10;
  print(john.age); // Output: 0
```

## Interfaces

In Dart, an interface is a contract that defines a set of method signatures that a class must implement. An interface only specifies the method signatures, not their implementation. A class that implements an interface must provide an implementation for all the methods declared in the interface.

> In below example, Vehicle, defines two methods that a class must implement: start() and stop(). Any class that implements this interface must provide its own implementation for these methods.

```dart
  // Define an interface
  abstract class Vehicle {
    void start();
    void stop();
  }

  // Implement the interface
  class Car implements Vehicle {
    @override
    void start() {
      print('Starting the car');
    }

    @override
    void stop() {
      print('Stopping the car');
    }
  }
```

## Exceptions

In Dart, exceptions are a way to handle runtime errors and abnormal conditions that occur during program execution. An exception is an object that represents an error that occurred during the execution of a program.

> The try block is used to enclose the code that might throw an exception. The catch block is used to handle the exception if it is thrown. The on keyword is used to specify the type of exception to catch. The finally block is used to specify code that should be executed regardless of whether an exception was thrown or not.

```dart
  try {
    var result = 12 ~/ 0; // This line will throw an exception
  } on IntegerDivisionByZeroException {
    print('Cannot divide by zero');
  } catch (e) {
    print('An exception occurred: $e');
  } finally {
    print('This code will always be executed');
  }
```

### Throw Exceptions

In Dart, you can throw or raise an exception using the throw keyword. You can throw any object as an exception. Typically, you would throw an object that extends the Exception class or one of its subclasses, such as FormatException, ArgumentError, or RangeError.

```dart
  void validateAge(int age) {
    if (age < 0 || age > 120) {
      throw ArgumentError('Invalid age: $age');
    }
  }

  void main() {
    try {
      validateAge(150);
    } catch (e) {
      print('An exception occurred: $e'); // An exception occurred: Invalid age: 150
    }
  }
```

## Futures

Futures represent a value that may not be available yet but will be available at some point in the future. They are often used in asynchronous programming, where the application needs to perform some long-running task, such as a network request or file I/O, without blocking the main thread. Instead of waiting for the task to complete before moving on, the application can create a Future object to represent the task and continue executing other code. When the task is finished, the Future object is updated with the result.

> In this example, we use the http package to send a GET request to the API endpoint and fetch the username. We then use async/await to wait for the username to be fetched before printing it. If an error occurs while fetching the username, we handle it using a try/catch block and print an error message.

```dart
  import 'dart:async';
  import 'dart:convert';
  import 'package:http/http.dart' as http;

  // Define the URL for the API endpoint
  const String apiEndpoint = 'https://example.com/api/user';

  // Define a function to fetch the username from the API and return a Future
  Future<String> fetchUsername() async {
    // Send a GET request to the API endpoint
    final response = await http.get(Uri.parse(apiEndpoint));
    if (response.statusCode == 200) { // If the response is successful
      final json = jsonDecode(response.body); // Parse the JSON response
      return json['username']; // Return the username
    } else { // If the response is not successful
      throw Exception('Failed to fetch username'); // Throw an exception
    }
  }

  // Define a function to print the username using async/await
  Future<void> printUsername() async {
    try {
      final username = await fetchUsername(); // Wait for the username to be fetched
      print('The username is $username'); // Print the username
    } catch (e) { // Handle any errors that occur while fetching the username
      print('Error fetching username: $e');
    }
  }

  void main() {
    print('Fetching username...');
    printUsername(); // Call the printUsername() function
    // Print a message indicating that we're waiting for the username
    print('Waiting for the username to be fetched...');
  }
```

## Miscellaneous

### Null and Null aware

In Dart, null is a special value that represents the absence of a value. When a variable is assigned null, it means that the variable doesn't currently have a value.

The null-aware operator in Dart is a shorthand way of checking whether an object is null before accessing its properties or calling its methods. There are two types of null-aware operators

#### **1. The ?. operator**

This operator is used to safely access properties and methods of an object that might be null. If the object is null, the expression will short-circuit and return null, without attempting to access the property or call the method.

> If the person object is null, the name variable will be assigned null without throwing a NullPointerException.

```dart
  class Person {
    String name;
  }

  Person person;
  // name variable will be null instead of raising a NullPointerException
  String name = person?.name;
```

#### **2. The ?? operator**

This operator is used to provide a default value if the expression to the left of the operator is null. If the expression is not null, the operator returns its value.

```dart
  int getUserAge() {
    // some logic to retrieve the user's age
    return null;
  }

  void main() {
    int age = getUserAge() ?? 18;
    print('The user is ${age} years old.');
  }
```

---

### Ternary Operator

The ternary operator is a shorthand way of writing an if-else statement in Dart.

```dart
  void main() {
    // traditional approach using if-else
    int age = 25;
    String status;

    if (age >= 18) {
      status = 'Adult';
    } else {
      status = 'Minor';
    }

    // Ternary Operator
    // Syntaxt : condition ? true : false
    String result = age >= 18 ? 'Adult' : 'Minor';  // Adult

    print(status);
    print(result);
  }
```

---

### Spread Operator

In Dart, the spread operator (...) is used to "spread" the elements of a collection into another collection. The spread operator can be used with lists, sets, and maps.

```dart
  void main() {
    var list1 = [1, 2, 3];
    var list2 = [4, 5, 6];

    var combinedList = [...list1, ...list2];
    print(combinedList); // Output: [1, 2, 3, 4, 5, 6]

    // Can also combine with other values
    var combinedList = [0, ...list1, ...list2, 7, 8, 9];
    print(combinedList); // Output: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
  }
```

---

### Cascade Notation

Cascade notation is a feature in Dart that allows you to perform a sequence of operations on an object by chaining them together using the ".." operator. This operator is also known as the cascade operator.

The cascade notation is particularly useful when you want to perform multiple operations on the same object. Instead of creating temporary variables or repeatedly referencing the same object, you can use cascade notation to chain the operations together.

```dart
  class Person {
    String name;
    int age;

    void introduce() {
      print("Hello, my name is $name and I am $age years old.");
    }
  }

  void main() {
    var person = Person()
      ..name = "John"
      ..age = 30
      ..introduce();
  }
```

---

### References

- **https://quickref.me/dart**
