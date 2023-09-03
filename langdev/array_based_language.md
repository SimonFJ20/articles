
# Array based language

**A small programming language, based on arrays, inspired by Typescript's type system.**

Required knowledge:
- Programming
- A little bit of Typescript.
- A little bit of functional programmaing. Mostly just the fundamentals of recursion.

As a start to this series of articles, I'd like to show an idea I got for a programming language.

It started with me experiementing with the type system in Typescript.

To make this make sense, I'll have to explain a bit about typescript.

This is Javascript, we all know and hate it.
```js
let myVar = 123;

function foo(bar) {
    return bar + "baz";
}
```
And as you'd probably expect, Typescript is the same, but with types.
```ts
let myVar: number = 123;

function foo(bar: string): string {
    return bar + "baz";
}

// we'll use 'const' rather than 'let' from here on out
// apart from assignabiliy, they are semantically identical
const myVar: number = 123;

// we'll also use arrow notation for functions
// the 2 function definitions are identical apart from syntax
const foo = (bar: string): string => bar + "baz";
```
Typescript's type system contains some quite advanced features.
One such feature is it's generic type parameters (called "generics").[1]
```ts
const foo = <T>(bar: T): T => bar;

const a: number = 123;
const b: number = foo<number>(a);

const c = "baz";
const d = foo(c);
```
These generics can be constrained, such that we only allow a specific subset of types.
Typescript calls this narrowing.[2]
```ts
const unconstrained = <T>(a: T): T => a;
const constrained = <T extends number>(a: T): T => a;

const a = unconstrained(123);
const b = unconstrained("abc");
const c = constrained(123);
const d = constrained("abc"); // Argument of type 'string' is not assignable to parameter of type 'number'.
```
Another feature, is the variety of primitive types in Typescript.
```ts
const a: number = 123;
const b: 12 = 12;
const c: string = "abc"
const d: "foo" = "bar"; // Type '"bar"' is not assignable to type '"foo"'.
const e: number[] = [1, 2, 3];
const f: [string, 123] = ["foo", 123];
const g: [string, 123] = ["", 123];
const h: [string, 123] = ["foo", 456]; // Type '456' is not assignable to type '123'.
```
Not represented here are the `any`, `unknown`, `never` types, which are handy, but not necessary.
All types extend `any`, no types extend `unknown` and a values of type `never` can never exist (hence the name).
The locals (names referring to variable or constant values) `b` and `d` are what Typescript calls literal types.[3]
Literal types represent a specific value, eg. the `b` has the type `12`, meaning it'll only accept the value `12` specifically, not other numbers like `7`, `-123` or `3.14`; 
The locals `a` and `c` are what Typescript calls primitive types.[4]
Primitives types are the set of all literal types of a class of values. For example, the type `number` is a type of the set of all numbers, meaning the local `a` can accept all number values such as `7`, `-123` or `3.14`.
The local `e` has an array type, which is what you'd expect.[5]
the local `f` has what Typescript calls Tuple Types.[6]

Now these tuple types are quite interesting, as Typescript lets us do array operations on them.
To see what I mean, lets first look at array operations on array values.[8]
```ts
type A = number[];

const a: A = [1, 2, 3];
const b: A = [...a, 4]; // [1, 2, 3, 4]
const c: A = [5, 6, 7];
const d: A = [...a, ...c]; // [1, 2, 3, 4, 5, 6]
```
Here we see a value being appended to an array, and arrays being contatonated into a new array containing the elements of both.

The type statement defining `A`, makes `A` a type alias for `number[]`.[7]

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



Take a look at the following code.

```ts
type Add<Left extends any[], Right extends any[]> = [...Left, ...Right];
```

[1]: https://www.typescriptlang.org/docs/handbook/2/generics.html
[2]: https://www.typescriptlang.org/docs/handbook/2/narrowing.html
[3]: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#literal-types
[4]: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#the-primitives-string-number-and-boolean
[5]: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#arrays
[6]: https://www.typescriptlang.org/docs/handbook/2/objects.html#tuple-types
[7]: https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#type-aliases
[8]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax#spread_in_array_literals
[9]: https://www.typescriptlang.org/docs/handbook/2/conditional-types.html
[10]: https://github.com/microsoft/TypeScript/issues/14833






