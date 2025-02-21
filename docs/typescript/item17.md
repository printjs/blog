[首页](https://printjs.github.io/blog) / [typescript](https://printjs.github.io/blog/docs/typescript) / Avoid Numeric Index Signatures

# Avoid Numeric Index Signatures

## Things to Remember
* Understand that arrays are objects, so their keys are strings, not numbers. number as an index signature is a purely TypeScript construct designed to help catch bugs.
* Prefer Array, tuple, ArrayLike, or Iterable types to using number in an index signature yourself.
