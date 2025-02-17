# Get Comfortable with Structural Typing

JavaScript encourages “duck typing”: if you pass a function a value with all the right properties, it won’t care how you made the value. It will just use it. (This term refers to the saying, “If it walks like a duck and talks like a duck, then it probably is a duck.”)

## Things to Remember
* Understand that JavaScript is duck typed and TypeScript uses structural typing to model this: values assignable to your interfaces might have properties beyond those explicitly listed in your type declarations. Types are not “sealed.”
* Be aware that classes also follow structural typing rules. You may not have an instance of the class you expect!
* Use structural typing to facilitate unit testing.