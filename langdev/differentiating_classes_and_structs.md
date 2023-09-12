
# [WIP] Differentiating classes and structs

_Simon F. Jakobsen_

## Abstract

In modern programming, the term "class" has multiple meanings, and is often confused with the concept of a struct.
In many programming languages, such as Java, there are little to none language level distinctions between concept of structs and the concept of classes.
As the class construct is taught to be used only in the way OOP dictates them to be,
it often leads to cases where class constructs are applied disadvantageous.
This is most often the case for beginners who aren't skilled in tackling edge cases using design patterns and discipline.
In this article I will try to argue for the distinctness of classes and structs,
I'll then propose some language features tackling these issues in an imaginary languages,
and compare them to modern languages.

## Terminology

### Struct

In computer science, a struct (also called a structure, records, compound data) is a language concept and construct used to describe a piece of data consisting of multiple values.<sup>[1]</sup>
Structs consists of a list of field specifiers, and a name of the struct.
A field specifier specifies the name and data type fields (also called data fields, members, data members, attributes, properties, rows, instance variables) of the struct instances.
Some languages also support methods (also called associated methods, member functions), which provide ways of manipulating and retrieving the data of struct instances. A struct is a description of a composite data type with optional associated behaviour.

Some languages provide functionality to specify mutability of the struct, its fields or of struct instances.
Mutability meaning the ability to change the value of fields post initialization, implying that all fields are initialized at some point during instanciation.
Most often either complete mutability or complete immutability is desired, as opposed to varying mutability throughout the fields.

Some languages support access modifiers as a facility to hide certain fields from the consumer, these fields are only available through methods.
Although this is a feature in many languages, anything else than completely transparent visibility is often undesired, as we usually care about the underlying data.

A common design pattern supported by the struct construct is the Data Transfor Object (DTO) pattern.
Structs also support being used as passive data structures.<sup>[2]</sup>

An example of the struct concept is the `struct` construct in C.

```c
// define a struct with the name 'Car'
struct Car {
    // define a field 'top_speed' with the type 'float'
    float top_speed;
    int passenger_capacity;
};

// declare and instantiate a struct instance called 'sedan' of the struct 'Car'
struct Car sedan;
// afterwards, define the value of the fields
sedan.top_speed = 200.0;
sedan.passenger_capacity = 5;

// initialize a struct instance 'sports_car'
struct Car sports_car = {
    .top_speed = 300.0,
    .passenger_capacity = 2,
};

// use struct values
if (sedan.top_speed < sports_car.top_speed) { /* ... */ }
```

C doesn't strongly support methods, visibility modifiers or mutability specifiers on individual fields, but does support specifying mutability on entire struct instances. 

```c
// instantiate a non-const, ie. mutable instance
Car suv = {
    .top_speed = 170,
    .passenger_capacity = 5,
};

// instantiate a const, ie immutable instance
const Car minivan = {
    .top_speed = 160,
    .passenger_capacity = 7,
};

// allowed, since 'suv' is non-const/mutable
suv.top_speed = 180;
// error, not allowed, since 'minivan' is const/immutable
minivan.top_speed = 150;
```

The goal of a struct can trivially be achieved by the use of multiple variables defined seperately, the struct construct just provides an easy to read, less error prone, clear and concise way of grouping data together.

Ultimately, when using a struct, we care about the data.

### Class

In OOP, a class is a concept used to describe objects (also called entities) in an object model representing a problem space.
Classes are the same as types from type theory.
In OOP, an object is a 'thing' with some associated behaviour. Associated behaviour is often achieved using methods (also called associated methods, member functions). An object may contain data, but objects are opaque, meaning the consumer may not access the data directly, but have to rely on methods, which are in turn allowed to access the internal data. These restrictions are in place in order to encapsulate the data, allowing for data abstraction.<sup>[4]</sup>

Notice that classes are descriptions of objects in our problem space, and not just descriptions of composite data types; 

In many class-based languages (most often called OO-languages, although I dislike the term) the class concept is conveyed using a `class` construct.
Generalized, the class construct consists of a name of the class, and a list of fields (also called data fields, members, data members, attributes, properties, rows, instance variables) as though it was a struct.
The list also contains methods.
Most often access modifiers can be used on both fields and methods to make them public, private and others, ie. accessible or inaccesible to the consumer.

An example of the class concept is the `class` construct in Java.

```java
// define a class with the name 'User'
class User {
    // define private field 'username' with type 'String'
    private String username;
    private Hash passwordHash;

    // define constructor that initialized a class instance
    // using the specified parameters 'username' and 'password'
    public User(String username, String password) {
        this.username = username;
        this.passwordHash = password.toHash();
    }

    // define a some behaviour, ie. public method, with access to the internal fields
    public void changePassword(String oldPassword, String newPassword) throws AuthException {
        if (!passwordHash.isEqualTo(oldPassword.toHash()))
            throw WrongPasswordException();
        this.passwordHash = newPassword.toHash();
    }
}

// initialize an instance of 'User'
User terry = new User("terry", "1234");

// use public behaviour specified in the class definition
terry.changePassword("1234", "abcd");

// error, not allowed, since 'passwordHash' is marked 'private' inside the class definition
terry.passwordHash = "foo".toHash();
```

In this example we can see the `passwordHash` as an example of encapsulation.
The consumer of the interface has no idea nor influence on how the password is stored.
In order to write code adhearing to object oriented design, you have to follow a set of principles.
One of the most important principles, is that of encapsulation (also called information hiding).
The principle of encapsulation 

Ultimately, when using a class, we care about the behaviour.

## Problem

There are cases where OOP constrained classes aren't suited the problem.
In these cases using the class constructs in these languages, the way OO is taught, ie. encapsulation, ie. private fields, accessible through methods,
often leads to larger amounts of code than neccessary.
This also often leads to heaps of getters and setters, which is seen as a code smell.<sup>[11] [12]</sup>

### PODs

### Many parameters

```
class 
```

## Status quo

This isn't a new idea, many languages already have distinct concepts of classes and structs. Java for example has records.<sup>[6]</sup>
C++ has both structs and classes, although are basically the same construct, except for the default accessability semantics.<sup>[7]</sup>
C# has classes, structs and records, these all have different semantics and use cases.<sup>[8]</sup>
Typescript has both classes and strongly typed objects.<sup>[9][10]</sup>

While the language facilities are in place in many languages, they are often neither intuitive nor 

## Language design proposal

[1]: https://en.wikipedia.org/wiki/Record_(computer_science)
[2]: https://en.wikipedia.org/wiki/Passive_data_structure
[3]: https://en.wikipedia.org/wiki/Class_(computer_programming)
[4]: https://en.wikipedia.org/wiki/Object-oriented_programming
[5]: https://en.wikipedia.org/wiki/Data_transfer_object
[6]: https://docs.oracle.com/en/java/javase/14/language/records.html
[7]: https://en.cppreference.com/w/cpp/language/class
[8]: https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/value-types
[9]: https://www.typescriptlang.org/docs/handbook/2/objects.html
[10]: https://www.typescriptlang.org/docs/handbook/2/classes.html
[11]: https://www.infoworld.com/article/2073723/why-getter-and-setter-methods-are-evil.html
[12]: https://wiki.c2.com/?AccessorsAreEvil
[13]: https://en.wikipedia.org/wiki/SOLID

