# `mapStateToProps()`

## Props should be connected in a pure way

Consider the following:

```javascript
const mapStateToProps = (state, props) => ({
  activeFoos: props.foos.filter(foo => foo.isActive),
});
```

`filter()` creates a new array every time it is called. `connect()` is called whenever an action dispatch occurs. Thus, every single time the Redux store receives an action, the component connected via the above function will rerender due to a prop change.

## NEVER pass state as a prop!

Sometimes it is tempting to do this:

```javascript
const mapStateToProps = (state, props) => ({
	// ...stuff...
	state,
});
```

This lets the component use the state object flexibly, which can *SEEM* to alleviate the boilerplate needed from selectors, etc. However, this also means the component will re-render on EVERY state change, even to parts of the state that are entirely unrelated to the component in question.

## `mapStateToProps` arity affects performance

The implementation of `connect` actually [checks the arity](https://github.com/reactjs/react-redux/blob/7bde889738b4e4716bcd5fa7acf75e2675798997/src/connect/wrapMapToProps.js#L21) of your `mapStateToProps` function, and does an optimization if only one argument is accepted:

```javascript
// A length of one signals that mapToProps does not depend on props from the parent component.
// A length of zero is assumed to mean mapToProps is getting args via arguments or ...args and
// therefore not reporting its length accurately..
export function getDependsOnOwnProps(mapToProps) {
  return (mapToProps.dependsOnOwnProps !== null && mapToProps.dependsOnOwnProps !== undefined)
    ? Boolean(mapToProps.dependsOnOwnProps)
    : mapToProps.length !== 1
}
```

This means that if your `mapStateToProps` is written to take arguments `(state, props)`, it will be recomputed when props change, **even if you do not use props in the function**. This also means that if you use `arguments` in your function to access props for whatever reason (please don't do this), your function will not recompute when props change, leading to errors.

Normally, this shouldn't be impactful, because `mapStateToProps` should be implemented to be cheap to compute. However, when coupled with incorrectly cached selectors or other expensive computations, this can degrade performance by causing unnecessary rerenders.