# Limit Use of the any Type

## There’s No Type Safety with any Types
```typescript
let ageInYears: number;
ageInYears = '12';
// ~~~~~~~ Type 'string' is not assignable to type 'number'.
ageInYears = '12' as any; // OK
ageInYears += 1; // OK; at runtime, ageInYears is now "121"
```

## any Lets You Break Contracts
```typescript
function calculateAge(birthDate: Date): number {
 // ...
}
let birthDate: any = '1990-01-19';
calculateAge(birthDate); // OK
```

## There Are No Language Services for any Types
## any Types Mask Bugs When You Refactor Code
## any Hides Your Type Design
## any Undermines Condence in the Type System

## Things to Remember
* TypeScript’s any type allows you to disable most forms of type checking for a symbol.
* The any type eliminates type safety, lets you break contracts, harms developer experience, makes refactoring error prone, hides your type design, and undermines confidence in the type system.
* Avoid using any when you can!