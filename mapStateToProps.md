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