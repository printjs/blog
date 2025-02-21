[首页](https://printjs.github.io/blog) / [typescript](https://printjs.github.io/blog/docs/typescript) / Use Type Operations and Generic Types to Avoid Repeating Yourself

# Use Type Operations and Generic Types to Avoid Repeating Yourself

## case1
```typescript
interface Bird {
    wingspanCm: number;
    weightGrams: number;
    color: string;
    isNocturnal: boolean;
}
interface Mammal {
    weightGrams: number;
    color: string;
    isNocturnal: boolean;
    eatsGardenPlants: boolean;
}
```
to
```typescript
interface Vertebrate {
    weightGrams: number;
    color: string;
    isNocturnal: boolean;
}
interface Bird extends Vertebrate {
    wingspanCm: number;
}
interface Mammal extends Vertebrate {
    eatsGardenPlants: boolean;
}

```
## case2
```typescript
interface Person {
    firstName: string;
    lastName: string;
}
interface PersonWithBirthDate extends Person {
    birth: Date;
}

// or
type TopNavState = Pick<State, 'userId' | 'pageTitle' | 'recentFiles'>;
```
## case3
```typescript
interface State {
    userId: string;
    pageTitle: string;
    recentFiles: string[];
    pageContents: string;
}
interface TopNavState {
    userId: string;
    pageTitle: string;
    recentFiles: string[];
    // omits pageContents
}
```
to
```typescript
interface TopNavState {
    userId: State['userId'];
    pageTitle: State['pageTitle'];
    recentFiles: State['recentFiles'];
};
//or
type TopNavState = {
    [K in 'userId' | 'pageTitle' | 'recentFiles']: State[K]
};
```

Mapped types are the type system equivalent of looping over the fields in an array. This particular pattern is so common that it’s part of the standard library, where it’s called `Pick`:
```typescript
type Pick<T, K> = { [k in K]: T[k] };
type TopNavState = Pick<State, 'userId' | 'pageTitle' | 'recentFiles'>;
```

If you’re defining a class that can be initialized and later updated, the type for the parameter to the update method might optionally include most of the same parameters as the constructor:
```typescript
interface Options {
    width: number;
    height: number;
    color: string;
    label: string;
}
interface OptionsUpdate {
    width?: number;
    height?: number;
    color?: string;
    label?: string;
}
class UIWidget {
    constructor(init: Options) { /* ... */ }
    update(options: OptionsUpdate) { /* ... */ }
}
```
You can construct OptionsUpdate from Options using a mapped type and keyof:
```typescript
type OptionsUpdate = {[k in keyof Options]?: Options[k]};
```

The mapped type ([k in keyof Options]) iterates over these and looks up the corresponding value type in Options. The ? makes each property optional. This pattern is also very common and is included in the standard library as Partial:
```typescript
class UIWidget {
    constructor(init: Options) { /* ... */ }
    update(options: Partial<Options>) { /* ... */ }
}
```

Mapped types have a few other tricks up their sleeve. You can include an as clause in them to rename the keys. There are many uses for this, but one is to invert the keys and values in a mapping:
```typescript
interface ShortToLong {
 q: 'search';
 n: 'numberOfResults';
}
type LongToShort = { [k in keyof ShortToLong as ShortToLong[k]]: k };
// ^? type LongToShort = { search: "q"; numberOfResults: "n"; }
```

If the index clause in your mapped type is of the form K in keyof T or a few variants on it, then TypeScript treats it as a “homomorphic” mapped type. This means that modifiers (like readonly and ? for optional) and documentation are transferred over to the new type
```typescript
interface Customer {
    /** How the customer would like to be addressed. */
    title?: string;
    /** Complete name as entered in the system. */
    readonly name: string;
}
type PickTitle = Pick<Customer, 'title'>;
// ^? type PickTitle = { title?: string; }
type PickName = Pick<Customer, 'name'>;
// ^? type PickName = { readonly name: string; }
type ManualName = { [K in 'name']: Customer[K]; };
// ^? type ManualName = { name: string; }

```
In this case, Pick is a homomorphic mapped type and preserves the optional and readonly modifiers. The ManualName mapped type does not use a keyof expression, so it is not homomorphic and it does **not transfer modifiers**. If you define a value using one of the homomorphic types, you’ll see that the documentation has been transferred over as well.

You may also find yourself wanting to define a type that matches the shape of a value:
```typescript
const INIT_OPTIONS = {
    width: 640,
    height: 480,
    color: '#00FF00',
    label: 'VGA',
};
interface Options {
    width: number;
    height: number;
    color: string;
    label: string;
}
```

You can do so with typeof:
```typescript
type Options = typeof INIT_OPTIONS;
```

Similarly, you may want to create a named type for the inferred return value of a function or method:
```typescript
function getUserInfo(userId: string) {
 // ...
    return {
        userId,
        name,
        age,
        height,
        weight,
        favoriteColor,
    };
}
// Return type inferred as { userId: string; name: string; age: number, ... }
```
Doing this directly requires conditional types (see Item 52). But, as we’ve seen before, the standard library defines generic types for common patterns like this one. In this case the ReturnType generic does exactly what you want:
```typescript
type UserInfo = ReturnType<typeof getUserInfo>;
```
**Note that ReturnType operates on typeof getUserInfo, the function’s type, rather than getUserInfo**, the function’s value. As with typeof, use this technique judiciously. Don’t get mixed up about your source of truth.

## Things to Remember
* The DRY (don’t repeat yourself) principle applies to types as much as it applies to logic.
* Name types rather than repeating them. Use extends to avoid repeating fields in interfaces.
* Build an understanding of the tools provided by TypeScript to map between types. These include keyof, typeof, indexing, and mapped types.
* Generic types are the equivalent of functions for types. Use them to map between types instead of repeating type-level operations.
* Familiarize yourself with generic types defined in the standard library, such as Pick, Partial, and ReturnType.
* Avoid over-application of DRY: make sure the properties and types you’re sharing are really the same thing.



