# `componentWillReceiveProps()`

## Consider using `componentDidUpdate()`

If you have do something with props based on a prop update, it can be awkward to use `nextProps` passed by `componentWillReceiveProps()` if you want to call other class methods, which may access `this.props`. If you want to react on next props rather than current props, calling class methods will result in using "outdated" props.

Using `componentDidUpdate()` means that class methods will have the correct props. The caveat is that extra renders may occur, because you are responding to the prop change after the render caused by the prop change completes. This should be weighed against the elegance of not having to make class methods accept `nextProps` as arguments.

## Consider using helper functions that take `(currProps, nextProps)`

Other than `render()`, `componentWillReceiveProps()` often become the longest functions in a component, and usually, various different responsibilities are being handled, making the function exceptionally difficult to read and refactor. For example, a `componentWillReceiveProps()` may be responsible for flushing outdated state, handling fetches, updating dependent form fields, etc.

Consider writing functions that that take `(currProps, nextProps)` which each handle one of these responsibilities. This can make future refactoring much more pleasant, because now responsibilities are distributed across various functions, rather than living in one gigantic method.