# `setState()`

## Use functional `setState()` for concurrent state updates

```javascript
iterateAndSetState() {
  someArray.forEach(item => this.setState({
    itemCount: this.state.itemCount + 1,
  }));
}
```

The following function will only increment `this.state.itemCount` by 1 regardless of the size of `someArray`. This is because when the first argument to `setState()` is an object and `setState()` is called multiple times, the updates are batched and then merged.

This most commonly occurs when setting state via iteration.

**Fix**:

```javascript
iterateAndSetState() {
  someArray.forEach(item => this.setState(state => ({
    itemCount: state.itemCount + 1,
  })));
}
```

Here, `state` passed to the callback in `setState()` is guaranteed to be the most up-to-date `state`, regardless of batching.
