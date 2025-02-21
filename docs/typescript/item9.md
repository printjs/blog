[首页](https://printjs.github.io/blog) / [typescript](https://printjs.github.io/blog/docs/typescript) / Prefer Type Annotations to Type Assertions

# Prefer Type Annotations to Type Assertions

TypeScript seems to have two ways of assigning a value to a variable and giving it a type:
```typescript
interface Person { name: string };
const alice: Person = { name: 'Alice' };
// ^? const alice: Person
const bob = { name: 'Bob' } as Person;
// ^? const bob: Person
```

> You may also see code that looks like `const bob = <Person>{}`. This was the original syntax for assertions and is equivalent to {} as Person. It is less common now because <Person> is interpreted as a start tag in .tsx files (TypeScript + React).

***Type assertions have their limits: they don’t let you convert between arbitrary types. The general rule is that you can use a type assertion to convert between A and B if they are “comparable” to one another. Using the set terminology from Item 7, this means that A and B must have a non-empty intersection. In particular, subtypes are allowed. HTMLElement is a subtype of HTMLElement | null, so this type assertion is OK. (The intersection of these types is HTMLElement.) HTMLButtonElement is a subtype of EventTarget, so that is OK, too. And Person is a subtype of {}, so that assertion is also fine.***

But you can’t convert between a Person and an HTMLElement since their intersection is empty (i.e., the never type):
```typescript
interface Person { name: string; }
const body = document.body;
const el = body as Person;
// ~~~~~~~~~~~~~~
// Conversion of type 'HTMLElement' to type 'Person' may be a mistake because
// neither type sufficiently overlaps with the other. If this was intentional,
// convert the expression to 'unknown' first.
```
The error suggests an escape hatch, namely, using the unknown type (Item 46). Every type is a subtype of unknown, so assertions involving unknown are always OK. This lets you convert between arbitrary types, but at least you’re being explicit that you’re doing something suspicious!
```typescript
const el = document.body as unknown as Person; // OK
```

## Things to Remember
* Prefer type annotations (: Type) to type assertions (as Type).
* Know how to annotate the return type of an arrow function.
* Use type assertions and non-null assertions only when you know something about types that TypeScript does not.
* When you use a type assertion, include a comment explaining why it’s valid.

