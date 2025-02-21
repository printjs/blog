#  Avoid Object Wrapper Types (String, Number, Boolean, Symbol, BigInt)

TypeScript models this distinction by having distinct types for the primitives and their object wrappers:
* string and String
* number and Number
* boolean and Boolean
* symbol and Symbol
* bigint and BigInt

## Things to Remember
* Avoid TypeScript object wrapper types. Use the primitive types instead: string instead of String, number instead of Number, boolean instead of Boolean, symbol instead of Symbol, and bigint instead of BigInt.
* Understand how object wrapper types are used to provide methods on primitive values. Avoid instantiating them or using them directly, with the exception of Symbol and BigInt.
