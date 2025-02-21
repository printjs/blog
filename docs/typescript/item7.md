[首页](https://printjs.github.io/blog) / [typescript](https://printjs.github.io/blog/docs/typescript) / Think of Types as Sets of Values

# Think of Types as Sets of Values (!important)

At runtime, every variable has a single value chosen from JavaScript’s universe of values. There are many possible values, including:
* 42
* null
* undefined
* 'Canada'
* {animal: 'Whale', weight_lbs: 40_000}
* /regex/
* new HTMLButtonElement
* (x, y) => x + y

But before your code runs, when TypeScript is checking it for errors, a variable just has a type. This is best thought of as a set of possible values. This set is known as the domain of the type. For instance, you can think of the number type as the set of all number values. 42 and -37.25 are in it, but 'Canada' is not. Depending on strict NullChecks, null and undefined may or may not be part of the set.

```TypeScript
interface Person {
 name: string;
 age: number;
}
interface Lifespan {
 birth: Date;
 death?: Date;
}
type PersonAndLifespan = Person & Lifespan
type PersonOrLifespan = Person | Lifespan

const ps: PersonAndLifespan = {
 name: 'Alan Turing',
 birth: new Date('1912/06/23'),
 death: new Date('1954/06/07'),
}; // ERROR 'age' is declared here.

const ps: PersonOrLifespan = {
 name: 'Alan Turing',
 birth: new Date('1912/06/23'),
 death: new Date('1954/06/07'),
}; // OK
```

```TypeScript
// Disclaimer: these are relationships, not TypeScript code!
keyof (A&B) = (keyof A) | (keyof B)
keyof (A|B) = (keyof A) & (keyof B)
```

## Things to Remember
* Think of types as sets of values (the type’s domain). These sets can either be finite (e.g., boolean or literal types) or infinite (e.g., number or string).
* TypeScript types form intersecting sets (a Venn diagram) rather than a strict hierarchy. Two types can overlap without either being a subtype of the other.
* Remember that an object can still belong to a type even if it has additional properties that were not mentioned in the type declaration.
* Type operations apply to a set’s domain. The domain of A | B is the union of the domains of A and B.
* Think of “extends,” “assignable to,” and “subtype of ” as synonyms for “subset of.”