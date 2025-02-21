# Know the Differences Between type and interface
If you want to define a named type in TypeScript, you have two options. You can use a type alias, as shown here:
```typescript
type TState = {
 name: string;
 capital: string;
};
```
or define an interface:
```typescript
interface IState {
 name: string;
 capital: string;
}
```

> An interface does have some abilities that a type doesn’t, however. One of these is that an interface can be augmented. Going back to the State example, you could have added a population field in another way:
```typescript
interface IState {
    name: string;
    capital: string;
}
interface IState {
    population: number;
}
const wyoming: IState = {
    name: 'Wyoming',
    capital: 'Cheyenne',
    population: 578_000
}; // OK
```

To understand why this unusual feature is useful, it’s instructive to look at how TypeScript itself uses declaration merging to model the different versions of JavaScript’s standard library. The Array interface, for example, is defined in lib.es5.d.ts:
```typescript
// lib.es5.d.ts
interface Array<T> {
    /** Gets or sets the length of the array. */
    length: number;
    // ...
    [n: number]: T;
}
```
If you set target to ES5 in your tscong.json (Item 2), then this is all you get. This definition includes all the properties and methods that were available on arrays when ES5 was published in 2009. But if you target ES2015, TypeScript will also include lib.es2015.core.d.ts. This includes another declaration of the Array interface:
```typescript
// lib.es2015.core.d.ts
interface Array<T> {
    /** Returns the index of the first element in the array where predicate... */
    findIndex(
    predicate: (value: T, index: number, obj: T[]) => unknown,
    thisArg?: any
    ): number;
    // ... also find, fill, copyWithin
}
```

**You can enforce consistent use of type or interface using typescript-eslint’s consistent-type-definitions rule, which is part of the stylistic preset (it prefers interface by default).**

**In other words, use interface when you can and type when you must, or when it’s more ergonomic. But don’t sweat it too much either way.**

**You can enforce consistent use of type or interface using typescript-eslint’s consistent-type-definitions rule, which is part of the stylistic preset (it prefers interface by default).**


## Things to Remember
* Understand the differences and similarities between type and interface.
* Know how to write the same types using either syntax.
* Be aware of declaration merging for interface and type inlining for type.
* For projects without an established style, prefer interface to type for object types.