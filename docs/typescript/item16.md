# Prefer More Precise Alternatives to Index Signatures

Objects in JavaScript map string (or symbol) keys to values of any type. TypeScript lets you represent flexible mappings like this by specifying an index signature on the type:
```typescript
type Rocket = {[property: string]: string};
const rocket: Rocket = {
    name: 'Falcon 9',
    variant: 'v1.0',
    thrust: '4,940 kN',
}; // OK
```
> The [property: string]: string is the index signature. It specifies three things:
* **A name for the keys** This is purely for documentation; it is not used by the type checker in any way.
* **A type for the key** This needs to be a subtype of string | number | symbol (aka PropertyKey).Typically it’s string or a subtype of string such as a union of string literals.number indexes are best avoided, as you’ll see in Item 17. symbol is rare in application code.
* **A type for the values** This can be anything.

> While this code does type check, it has a few downsides:
* It allows any keys, including incorrect ones. Had you written Name instead of name, it would have still been a valid Rocket type.
* It doesn’t require any specific keys to be present. {} is also a valid Rocket.
* It cannot have distinct types for different keys. For example, we might want thrust to be a number rather than a string.
* TypeScript’s language services can’t help you with types like this. As you’re typing name:, there’s no autocomplete because the key could be anything.

## Things to Remember
* Understand the drawbacks of index signatures: much like any, they erode type safety and reduce the value of language services.
* Prefer more precise types to index signatures when possible: interfaces, Map, Records, mapped types, or index signatures with a constrained key space.

