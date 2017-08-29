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