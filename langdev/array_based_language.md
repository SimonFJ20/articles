
# Array based language

**A small programming language, based on arrays, inspired by Typescript's type system.**

*Simon F. Jakobsen*

I'd like to show an idea I got for a programming language.

It started with me experimenting with the type system in Typescript.

## Typescript's type system

To make this make sense, I'll have to explain a bit about typescript.

Typescript has a type system (WOW :exploding_head:!).
Typescript's type system contains some quite advanced features.
One such feature is it's generic type parameters (called "generics").[1]
```ts
const foo = <T>(bar: T): T => bar;

foo<number>(123); // T = number
foo("foo"); // T = string
```
These generics can be constrained, such that we only allow a specific subset of types.
Typescript calls this narrowing.[2]
```ts
const constrained = <T extends number>(a: T): T => a;

const c = constrained(123);
const d = constrained("abc"); // Argument of type 'string' is not assignable to parameter of type 'number'.
```
Another feature, is the variety of primitive types in Typescript.
```ts
const a: number = 123;
const b: 12 = 12;

const c: number[] = [1, 2, 3];
const d: [string, 123] = ["foo", 123];
```
Not represented here are the `any`, `void`, `unknown`, `never`, `null`, `undefined` types and object literal types, which are handy, but not necessary.
The local (name referring to variable or constant value) `b` is what Typescript calls literal types.[3]
Literal types represent a specific value, eg. the `b` has the type `12`, meaning it'll only accept the value `12` specifically, not other numbers like `7`, `-123` or `3.14`; 
The local `a` is what Typescript calls primitive types.[4]
Primitives types are the set of all literal types of a class of values. For example, the type `number` is a type of the set of all numbers, meaning the local `a` can accept all number values such as `7`, `-123` or `3.14`.
The local `c` has an array type, which is what you'd expect.[5]
the local `d` has what Typescript calls Tuple Types.[6]

Now these tuple types are quite interesting, as Typescript lets us do array operations on them.
To see what I mean, lets first look at array operations on array values.[8]
```ts
const a = [1, 2, 3];
const b = [...a, 4]; // [1, 2, 3, 4]
const c = [5, 6, 7];
const d = [...a, ...c]; // [1, 2, 3, 4, 5, 6]
```
Here we see a value being appended to an array, and arrays being contatonated into a new array containing the elements of both.

Now with tuple types, we can do the same on types.
```ts
type A = [any, any];
type B = [any, any, any];
type C = [...A, ...B]; // [any, any, any, any, any]
```
Now these types, we retrieve using generics too.
```ts
type C<A extends any[], B exends any[]> = [...A, ...b];
```
Now what if we represent integers N in terms of arrays with a of length N.
And what if we rename `C` to `Add`.
```ts
type Add<Left extends any[], Right exends any[]> = [...Left, ...Right];

type A = [any, any, any]; // 3
type B = [any, any]; // 2
type Sym = Add<A, B>; // [any, any, any, any, any], ie. 2 + 3 = 5
```
Now wouldn't it also be nice if we had some way to do conditionals.
Consider the ternary operator on values.
```ts
const a = (b: number): string =>
    b === 5
        ? "b is five"
        : "b is not five";

const c = a(5); // "b is five";
const c = a(3); // "b is not five";
```
Typescript provides the same construct for types, in what they call Conditional Types.[9]
```ts
type A<B extends number> =
    B extends 5
        ? string
        : boolean;

let a: A<5> = "hello";
let b: A<4> = true;
let c: A<5> = false; // Type 'boolean' is not assignable to type 'string'.
```
Now we just need some way of doing iteration, luckily for us Typescript allows for recursive type definitions.
```ts
type Contains<V, VS extends any[]> =
    VS extends [infer C, ...infer Rest extends any[]]
        ? C extends V
            ? true
            : Contains<V, Rest>
        : false

type A = Contains<5, [1, 2, 3]>; // false
type B = Contains<"bar", ["foo", "bar", "baz"]>; // true
```
The quick ones among you would already have realised, that Typescript type system is Turing complete as fuck.[10]
(Except for the recursion depth for type instantiations, which have methods of partial mitigation.[11])
This is because we conform to the 3 rules of a turing complete programming language, consisting of:
1. Sequence (we have recursion)
2. Selection (we have conditional)
3. Iteration (we have recursion)

Also notice the lack of ability to do anything, meaning no side effects, and the way types definitions are essentially functions, meaning the what we have is a purely functional programming language.

Now with this newly discovered Turing complete purely functional programming language of ours, that being Typescript's type system,
we could, for example, make a PEMDAS complient calculator, including a full set of signed integer arithmetic operations add/subtract/multiply/divide/remainder/exponentiation, including comparison operators lt/lte/gt/gte/equal/not equal,
and of course a X86-64 generator for good measures. And this is exactly what I did.[12]

![image](https://github.com/SimonFJ20/articles/assets/28040410/baf907d9-f535-4091-b8d2-4e5ccaaf9223)
![image](https://github.com/SimonFJ20/articles/assets/28040410/34bca835-96b6-4ac3-8618-5c292e7332c5)
![image](https://github.com/SimonFJ20/articles/assets/28040410/c1f3e2e2-6d7d-4986-9be7-98abeb3dcadb)

## Reflecting on Typescript

From now on I'll refer to Typescript's type system used as a programming language as just TS for simplicity, and use *Typescript* to refer to the original definition. 

### The good

What do I like about Typescript:
- Simple
- Ergonomic syntax
- Small programs
- Composable

#### Simple

```ebnf
program ::= typeDefinitionStatement*
typeDefinitionStatement ::= "type" ID paramList?  "=" type
paramList ::= "<" seperatedList(param, ",") ">"
param ::= ID ("extends" pattern)?

pattern ::= objectPattern | tuplePattern | spreadPattern | literalType | ID | "infer" ID
objectPattern ::= uninteresting
tuplePattern ::= [pattern*]
spreadPattern ::= "..." pattern

type ::= optionalType | objectType | tupleType | arrayType | spreadType | literalType | ID
optionalType ::= param "?" type ":" type
objectType ::= uninteresting 
tupleType ::= [seperatedList(type, ",")]
arrayType ::= type[]
spreadType ::= "..." type
literalType ::= INT_LITERAL | STRING_LITERAL | "true" | "false" | "null" | "undefined"
```
The above is the entire grammar of the parts of Typescript I'm interested in expressed in EBNF.[13][14]
This evidently represents a very simple language, but we've just seen that just this is Turing complete and, in my opinion, totally usable.

#### Ergonomic syntax

Look at the following code.

```ts
type A = [any, any, any];

type B<C extends any[]> =
    C extends [any, any, any]
        ? true
        : false

type D<A extends any[], E extends any[]> =
    B<A> extends true
        ? [...A, ...E]
        : E;

type F<A> = ...;
type MapF<List extends any[], Acc extends any[] = []> =
    List extends [infer Element, ...infer Rest extends any[]]
        ? MapF<Rest, [...Acc, F<Element>]>
        : Acc;
type Applied = MapF<[2, 3]>; // [F<2>, F<3>];
```
It's trivial to infer that `[any, any, any]` is a container with 3 elements, the elements all being `any` in this case.
The spread syntax is even simpler to deduce the behavior of.
I really like the tenary operator, as it's clear and concise.

#### Small programs

This is mostly anecdotal, as I haven't done much formal research on it.
But in my experience, programs written in this functional style tend to be a lot shorter,
than their imperitive equivalent.

#### Composable

Everything is either a value or a function.
There isn't a seperation between statements and expressions.
All subexpressions in an expression can be extracted out into it's own function.
This is generally a trait of functional programming languages.

### The bad

What do I not like about Typescript:
- The implementation
- No way of doing side effects
- No higher kinded types
- The rest of *Typescript*

#### The implementation

Typescript isn't inherently slow, just the fact that it's evaluated through *Typescript*'s type checker.

Without having done any performance testing myself, I can say from experience, that even simple program using a bit of recursion can take several 10's of seconds to evaluate.
Compare this to modern day Javascript in the browser and it's abysmally slow.

There's also the problem of recursion depth.

![image](https://github.com/SimonFJ20/articles/assets/28040410/26fec0ae-5376-4d9e-a25c-d4f981a2116b)

This limits the size of programs.
There are ways to improve this, but only slightly.[11]

#### Side effects

Input is typed into the source code, output is retrieved by asking the language server, this isn't particularly practical.

The lack of side effects in general isn't necessarily bad, but the lack of any side effects limits the usecases a lot.
For example, there are no way to
- read a file
- write to a file
- host a web server
- send a web request
- print to the console
- get input from the console
- interact with a database

#### No higher kinded types

*Typescript* does not suppert higher kinded types.[15]
We could've used them as lambda functions, implying polymorphism.
But in my experience, i haven't found the lack of parametric polymorphism to be crippling. It's just a nice to have.

#### The rest of Typescript

Having all the other features of *Typescript* in the languages, is obviously undesireable.

## A new language

Wouldn't it be nice to have all the good parts without the bad?
Yes, it would. Therefore let's make our own language with all the right ideas and none of the bad.

### Spec

This isn't a complete specification, just a slightly technical description of the language.

#### Expressions

##### Symbol

A symbol is an identifiable literal value.

```rs
'a
'123
'foo_bar
```

Identifiable meaing two identical symbols are equal, ie. `'a = 'a` and `'a != 'b`.

##### Array

None, one or multiple values in a container.

```rs
[]
['a 'b 'c]
[[] 'a ['a 'b]]
```
No comma seperation.

##### Integer literal

Integers are syntactic suger for arrays of a specific length.

```rs
5 // is equivalent to [_ _ _ _ _]
```

##### Character literal

Characters are syntactic suger for integers with the ascii/unicode representation.

```rs
'a' // is equivalent to 96
```

##### String literal

Strings are syntactic suger for arrays with characters represented as integers.

```rs
"hello" // is equivalent to ['h' 'e' 'l' 'l' 'o']
```

##### Spread

```rs
..v
[a b] // [[..] [..]]
[a ..b] // [[..] ..]
[..a b] // [.. [..]]
[..a ..b] // [.. ..]
```

##### Application/Call

```rs
(operator arg1 arg2)
(a)
(a b)
```

##### Conditional

```rs
if a = b
    ? c
    : d
```

`a` is an expression, so are `c` and `d` although they're lazilier evaluated. `b` is a pattern optionally containing bound values.

##### Let

```rs
let a = 5;
let b x = [x x];

a // 5
b // fn x = [x x]
(b 5) // [5 5]
```

Uses the `let` keyword.

##### Lambda

```rs
let my_lambda = fn a b = (add a b)

(my_lambda 2 3)
```

Uses the `fn` keyword. 

##### Curring

Some some reason i chose to support currying.[16]

```rs
let a v = (add v 2)

(a 5) // 7
(a 3) // 5
```

#### Grammar

```ebnf
program ::= expression

expression ::= let | fn | if | spreadExpr | arrayExpr | callExpr | groupExpr | IDENTIFIER | SYMBOL | INTEGER

let ::= "let" IDENTIFIER+ "=" expression ";" expression

fn ::= "fn" IDENTIFIER* "=" expression

if ::= "if" expression "=" pattern "?" expression ":" expression

spreadExpr ::= ..expression

arrayExpr ::= [expression*]

callExpr ::= (expression+)

groupExpr ::= (expression)

pattern ::=  bind | spreadPattern | arrayPattern | groupPattern | IDENTIFIER | SYMBOL | INTEGER

bind ::= "@" IDENTIFIER ("=" pattern)?

spreadPattern ::= ..pattern

arrayPattern ::= [pattern*]

groupPattern ::= (pattern)

SYMBOL ::= /('[a-zA-Z0-9_]+)|([0-9]+)/
IDENTIFIER ::= /[a-zA-Z_][a-zA-Z0-9_]*/
INTEGER ::= /[0-9]+/
```

This is an EBNF grammar describing the language.
All whitespace is ignored, except for it's delimiting capabilities, eg. `a b` and `ab` mean different things.

### Example programs

#### Hello world

```rs
"Hello, world!"
```

#### FizzBuzz

```rs
let fizzbuzz n =
    if (mod n 15) = 'true
        ? "fizzbuzz"
    : if (mod n 3) = 'true
        ? "fizz"
    : if (mod n 5) = 'true
        ? "buzz"
        : (int_to_string(n));

(map fizzbuzz (range 1 100))
```

#### Calculator

```rs
let contains v vs =
    if vs = [@m ..@rest]
        ? if v = m
            ? true
            : (contains v rest)
        : false;

let tokenize_int text acc =
    if text = [@char ..@rest]
        ? if (contains char "0123456789") = 'true
            ? (tokenize_int rest [..acc char])
            : [text acc]
        : [text acc];

let tokenize text acc =
    if text = [@char ..@rest]
        ? if (contains char "0123456789") = 'true
            ? if tokenize_int text [] = [@rest @value]
                ? (tokenize rest [..acc ['int value]])
                : 'never
            : if (contains char "+-*/()") = 'true
                ? (tokenize rest [..acc ['operator char]])
                : (tokenize rest [..acc ['invalid char]])
        : acc;

let parse_group tokens =
    if tokens = [..@rest]
        ? if (parse tokens) = [@expr ..@rest]
            ? 
        : ['error "expected expression"]


let parse_operand tokens =
    if tokens = [['int value] ..@rest]
        ? ['int value]
        : if tokens = [['operator '('] ..@rest]
            ? (parse_group rest)
            : ['error "expected operand"]
```

## Implementation

I'm working on a quite naive interpreter written in Typescript.

[1]: https://www.typescriptlang.org/docs/handbook/2/generics.html
[2]: https://www.typescriptlang.org/docs/handbook/2/narrowing.html
[3]: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#literal-types
[4]: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#the-primitives-string-number-and-boolean
[5]: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#arrays
[6]: https://www.typescriptlang.org/docs/handbook/2/objects.html#tuple-types
<!-- [7]: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#type-aliases -->
[8]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax#spread_in_array_literals
[9]: https://www.typescriptlang.org/docs/handbook/2/conditional-types.html
[10]: https://github.com/microsoft/TypeScript/issues/14833
[11]: /techniques/improve_typescript_type_recursion_depth.md
[12]: https://gist.github.com/SimonFJ20/1bbfd17c323acb78ad46fef2af12d968
[13]: https://en.wikipedia.org/wiki/Extended_Backus%E2%80%93Naur_form
[14]: https://stackoverflow.com/questions/12720955/is-there-a-formal-ideally-bnf-typescript-js-language-grammar-or-only-typescri
[15]: https://github.com/microsoft/TypeScript/issues/1213
[16]: https://en.wikipedia.org/wiki/Currying






