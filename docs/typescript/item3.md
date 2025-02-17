# Understand That Code Generation Is Independent of Types

> ***You Cannot Check TypeScript Types at Runtime***

```typescript
interface Square {
 width: number;
}
interface Rectangle extends Square {
 height: number;
}
type Shape = Square | Rectangle;
function calculateArea(shape: Shape) {
    if (shape instanceof Rectangle) {
    // ~~~~~~~~~ 'Rectangle' only refers to a type,
    // but is being used as a value here
    return shape.height * shape.width;
    // ~~~~~~ Property 'height' does not exist on type 'Shape'
     } else {
    return shape.width * shape.width;
    }
}
```

**The instanceof check happens at runtime**

## Things to Remember
* Code generation is independent of the type system. This means that TypeScript types cannot affect the runtime behavior of your code.
* It is possible for a program with type errors to produce code (“compile”).
* TypeScript types are not available at runtime. To query a type at runtime, you need some way to reconstruct it. Tagged unions and property checking are common ways to do this.
* Some constructs, such as class, introduce both a TypeScript type and a value that is available at runtime.
* Because they are erased as part of compilation, TypeScript types cannot affect the runtime performance of your code