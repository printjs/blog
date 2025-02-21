# Distinguish Excess Property Checking from Type Checking

```typescript
// case1
interface Room {
 numDoors: number;
 ceilingHeightFt: number;
}
const r: Room = {
 numDoors: 1,
 ceilingHeightFt: 10,
 elephant: 'present',
// ~~~~~~~ Object literal may only specify known properties,
// and 'elephant' does not exist in type 'Room'
};

// case2
const obj = {
 numDoors: 1,
 ceilingHeightFt: 10,
 elephant: 'present',
};
const r1: Room = obj; // OK
```

> So what’s different about these two examples? In the first you’ve triggered a process known as “excess property checking,” which helps catch an important class of errors that the structural type system would otherwise miss. But this process has its limits, and conflating it with regular assignability checks can make it harder to build an intuition for structural typing. Recognizing excess property checking as a distinct process will help you build a clearer mental model of TypeScript’s type system.

```typescript
const o: Options = { darkmode: true, title: 'Ski Free' };
// ~~~~~~~~ 'darkmode' does not exist in type 'Options'...

const intermediate = { darkmode: true, title: 'Ski Free' };
const o: Options = intermediate; // OK
```

> While the righthand side of the first line is an object literal, the righthand side of the second line (intermediate) is not, so excess property checking does not apply, and the error goes away.

Excess property checking does not happen when you use a type assertion:
```typescript
const o = { darkmode: true, title: 'MS Hearts' } as Options; // OK
```
This is a good reason to prefer type annotations to assertions (Item 9).

If you don’t want this sort of check, you can tell TypeScript to expect additional properties using an index signature:
```typescript
interface Options {
 darkMode?: boolean;
 [otherOptions: string]: unknown;
}
const o: Options = { darkmode: true }; // OK
```

A related check happens for so-called “weak” types, which have only optional properties:
```typescript
interface LineChartOptions {
 logscale?: boolean;
 invertedYAxis?: boolean;
 areaChart?: boolean;
}
function setOptions(options: LineChartOptions) { /* ... */ }
const opts = { logScale: true };
setOptions(opts);
// ~~~~ Type '{ logScale: boolean; }' has no properties in common
// with type 'LineChartOptions'
```


## Things to Remember
* When you assign an object literal to a variable with a known type or pass it as an argument to a function, it undergoes excess property checking.
* Excess property checking is an effective way to find errors, but it is distinct from the usual structural assignability checks done by the TypeScript type checker. Conflating these processes will make it harder for you to build a mental model of assignability. TypeScript types are not “closed” (Item 4).
* Be aware of the limits of excess property checking: introducing an intermediate variable will remove these checks.
* A “weak type” is an object type with only optional properties. For these types, assignability checks require at least one matching property.

