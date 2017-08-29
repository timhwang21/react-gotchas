# `mapStateToProps()`

## Props should be connected in a pure way

Consider the following:

```javascript
const mapStateToProps = (state, props) => ({
  activeFoos: props.foos.filter(foo => foo.isActive),
});
```

`filter()` creates a new array every time it is called. `connect()` is called whenever an action dispatch occurs. Thus, every single time the Redux store receives an action, the component connected via the above function will rerender due to a prop change.

