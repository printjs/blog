# Apply Types to Entire Function Expressions When Possible

JavaScript (and TypeScript) distinguishes between a function statement and a function expression:
```typescript
function rollDice1(sides: number): number { /* ... */ } // Statement
const rollDice2 = function(sides: number): number { /* ... */ }; // Expression
const rollDice3 = (sides: number): number => { /* ... */ }; // Also expression
```

## Things to Remember
* Consider applying type annotations to entire function expressions, rather than to their parameters and return type.
* If you’re writing the same type signature repeatedly, factor out a function type or look for an existing one.
* If you’re a library author, provide types for common callbacks.
* Use typeof fn to match the signature of another function, or Parameters and a rest parameter if you need to change the return type.

