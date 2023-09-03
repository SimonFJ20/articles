
# Improve Typescript's type recursion limit

![image](https://github.com/SimonFJ20/articles/assets/28040410/35b641ac-664a-4326-af0b-232d6d730069)

## Lazy constraints

Rewrite
```ts
type A<B extends number> = ...; 
```
into
```ts
type A<B> ?
    [B] extends [infer B extends number] 
        ? ...
        : never;
```
because for some reason, this increases the max depth.

