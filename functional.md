# Functional style

## Prefer pure functions

In general, pure functions are easier to test, easier to reason about, and tend to be less imperative and less declarative. They can be harder or more verbose to write, but in my opinion, the benefits drastically outweigh the consequences.

In addition, both React and Redux espouse functional principles. When working with a library, adopting conflicting paradigms is generally asking for trouble.

This means `concat()` over `push()`, `map()` and `reduce()` over for-loops, spread over `Object.assign`, etc.

## ...but not always

Consider the following:

```javascript
const things = ['foo', 'bar', 'baz'];

const loudThings = things.reduce((acc, el) => ({ ...acc, [el]: el.toUpperCase() }), {});
```

Looks sexy, but the accumulator is being reallocated and garbage collected for every item in `things`. If `things` is a million item array you'll have thrown away that many "intermediary" objects, which is `O(n^2)`.

Mutation can drastically speed this up:

```javascript
const things = ['foo', 'bar', 'baz'];

const loudThings = things.reduce((acc, el) => { acc[el] = el.toUpperCase(); return acc; }, {});
```

Linear time. Mutating here is fine because `acc` is created only when the function is evaluated. In general, objects that are allocated within a function are free to be mutated. This includes the accumulator in `reduce()` (second argument). Specifically, *observable* mutations should be avoided, while *nonobservable* mutations (mutations to objects internal to a function) are fine (though should still be avoided unless they result in a nontrivial performance gain).

## Avoid side effects in functions generally viewed as pure

Yes, you can mutate an array in `map()`. Yes, you can use `every()` or `some()` as a `forEach()` that can be `break`ed out of, and is more elegant than a for loop. But the semantics of these functions are that they are pure. Someone extending your code may assume that your usage of `some()` does not cause a mutation somewhere when making changes, and this assumption may then lead to an error down the line.

## Point-free programming

Oftentimes, callbacks are unnecessarily wrapped in arrow functions. While the performance penalty is minimal, it's not idiomatic: it basically treats arrow functions as akin to Ruby blocks, and masks the fact that higher order functions take functions as arguments.

The following two are identical:

```javascript
const isActive = el => el.active;

flights.filter(flight => isActive(flight));
flights.filter(isActive);
```

**Note**: that this is NOT the case when the callback takes multiple arguments:

```javascript
const numberStrings = ['1', '2', '3'];

numberStrings.map(num => parseInt(num));
numberStrings.map(parseInt);
// second argument passed to parseInt() will be the element index
```
