# `mergeProps()`

## `connect`ed props should have priority

Very often you see `mergeProps()` implemented as such:

```javascript
const mergeProps = (stateProps, dispatchProps, ownProps) => ({
	...stateProps,
	...dispatchProps,
	...ownProps,
});
```

The default implementation of `mergeProps()` (used when the user does not define it) has `ownProps` spread in first, rather than last. The above implementation, if added to an existing connected component, may subtly alter behavior by causing the "wrong" prop to be passed.

## `mergeProps` recomputes a lot

From the Redux documentation, `mapStateToProps()` recomputes whenever (any) state or props change, and `mapDispatchToProps()` recomputes whenever any props change. This generally prevents bound action creators from `mapDispatchToProps()` from causing rerenders, because they tend to only be recreated when a rerender would happen anyways.

`mergeProps()` is sometimes used to bind action creators that need props from `mapStateToProps()`. This means that if uncached, the resultant function is recreated on every state change, often leading to unwanted rerenders.