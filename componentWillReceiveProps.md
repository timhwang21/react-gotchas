# `componentWillReceiveProps()`

## Use `componentDidUpdate()` to respond to changes instead

> **Update**: React 16.3 onwards began the process of deprecating `componentWillReceiveProps()`. You almost certainly want to use `componentDidUpdate()` instead. However, you most likely want to use `componentDidUpdate()` even if you're using an older version of React.

If you have do something with props based on a prop update, it can be awkward to use `nextProps` passed by `componentWillReceiveProps()` if you want to call other class methods, which may access `this.props`. If you want to react on next props rather than current props, calling class methods will result in using "outdated" props.

Using `componentDidUpdate()` means that class methods will have the correct props. The caveat is that extra renders may occur, because you are responding to the prop change after the render caused by the prop change completes. This should be weighed against the elegance of not having to make class methods accept `nextProps` as arguments.

Finally, `componentDidUpdate()` prevents the common mistake described in the previous section, where `this.props.foo` is used for something where `nextProps.foo` was intended.

## Use a memoized function or `static getDerivedStateFromProps()` to compute values based on props instead

If you are using React 16.3 and onwards, `getDerivedStateFromProps()` replaces `componentWillReceiveProps()` for the use case of computing some value dependent on props. Note that API requests, etc. should still be handled in `componentDidUpdate()` instead.

Note that `getDerivedStateFromProps()` has a verbose API, and that simpler alternatives exist. Specifically, [the React docs](https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html) recommend using a memoized function that eagerly computes the derived value instead. Because of the caching, recomputations won't create new object references that cause unwanted rerenders.

## Be wary of whether you need the current or next prop

A common use case is using callbacks passed as props to react to prop changes. Keep in mind that these callbacks can also change: for example, they may use the parent's own props. Thus, if you are reacting to something in `nextProps`, you probably want to call `nextProps.callback()` instead of `this.props.callback()`. Even if the functions are identical, you almost always want `nextProps.callback()` just on the off chance that the function DID change.

## Consider using helper functions that take `(currProps, nextProps)`

Other than `render()`, `componentWillReceiveProps()` often become the longest functions in a component, and usually, various different responsibilities are being handled, making the function exceptionally difficult to read and refactor. For example, a `componentWillReceiveProps()` may be responsible for flushing outdated state, handling fetches, updating dependent form fields, etc.

Consider writing functions that that take `(currProps, nextProps)` which each handle one of these responsibilities. This can make future refactoring much more pleasant, because now responsibilities are distributed across various functions, rather than living in one gigantic method.
