[首页](https://printjs.github.io/blog) / [typescript](https://printjs.github.io/blog/docs/typescript) / Use readonly to Avoid Errors Associated with Mutation

# Use readonly to Avoid Errors Associated with Mutation

Placed on a property in an object type, readonly prevents assignments to that property:
```typescript
interface PartlyMutableName {
 readonly first: string;
 last: string;
}
const jackie: PartlyMutableName = { first: 'Jacqueline', last: 'Kennedy' };
jackie.last = 'Onassis'; // OK
jackie.first = 'Jacky';
// ~~~~~ Cannot assign to 'first' because it is a read-only property
```

Typically, you’ll want to prevent assignments to all properties on an object. TypeScript provides a generic utility type, Readonly<T>, that does just that:
```typescript
interface FullyMutableName {
 first: string;
 last: string;
}
type FullyImmutableName = Readonly<FullyMutableName>;
// ^? type FullyImmutableName = {
// readonly first: string;
// readonly last: string;
// }
```

**you can assign a mutable array to a readonly array, but not vice versa:**
```typescript
const a: number[] = [1, 2, 3];
const b: readonly number[] = a;
const c: number[] = b;
// ~ Type 'readonly number[]' is 'readonly' and cannot be
// assigned to the mutable type 'number[]'
```

**When you give a parameter a read-only type (either readonly for an array or Readonly for an object type), a few things happen:**
* TypeScript checks that the parameter isn’t mutated in the function body.
* You advertise to callers that your function doesn’t mutate the parameter.
* Callers may pass your function a readonly array or Readonly object.

## Things to Remember
* If your function does not modify its parameters, declare them readonly (arrays) or Readonly (object types). This makes the function’s contract clearer and prevents inadvertent mutations in its implementation.
* Understand that readonly and Readonly are shallow, and that Readonly only affects properties, not methods.
* Use readonly to prevent errors with mutation and to find the places in your code where mutations occur.
* Understand the difference between const and readonly: the former prevents reassignment, the latter prevents mutation.
