[首页](https://printjs.github.io/blog) / [typescript](https://printjs.github.io/blog/docs/typescript) / Know How to Tell Whether a Symbol Is in the Type Space or Value Space

# Know How to Tell Whether a Symbol Is in the Type Space or Value Space

A symbol in TypeScript exists in one of two spaces:
* Type space
* Value space

```typescript
function email(to: Person, subject: string, body: string): Response {
 //      ――――― ―――――        ―――――――          ――――                     Values
 //               ――――――            ――――――        ――――――   ――――――――   Types
}
```

There are many other constructs that have different meanings in the two spaces:
* this in value space is JavaScript’s this keyword (Item 69). As a type, this is the TypeScript type of this, aka “polymorphic this.” It’s helpful for implementing method chains with subclasses.
* In value space, & and | are bitwise AND and OR. In type space they are the intersection and union operators.
* In value space, const introduces a new variable, but in type space, as const changes the inferred type of a literal or literal expression (Item 20).
* In value space, extends defines a subclass (class A extends B), but in type space it defines a subtype (interface A extends B) or a constraint on a generic type (Generic<T extends number>).
* In value space, "in" is used in for loops (for (key in object)), while in type space it’s used in mapped types (Item 15).
* In value space, ! is JavaScript’s logical not operator (!x), but in type space it’s a non-null type assertion (x!; see Item 9).


Correct
```typescript
function email(options: {to: Person, subject: string, body: string}) {
 // ...
}
function email(
 {to, subject, body}: {to: Person, subject: string, body: string}
) {
 // ...
}
```

ERROR
```typescript
function email({
 to: Person,
 // ~~~~~~ Binding element 'Person' implicitly has an 'any' type
 subject: string,
 // ~~~~~~ Binding element 'string' implicitly has an 'any' type
 body: string
 // ~~~~~~ Binding element 'string' implicitly has an 'any' type
}) { /* ... */ }
```

# Things to Remember
* Know how to tell whether you’re in type space or value space while reading a TypeScript expression. Use the TypeScript playground to build an intuition for this.
* Every value has a static type, but this is only accessible in type space. Type space constructs such as type and interface are erased and are not accessible in value space.
* Some constructs, such as class or enum, introduce both a type and a value.
* typeof, this, and many other operators and keywords have different meanings in type space and value space.
