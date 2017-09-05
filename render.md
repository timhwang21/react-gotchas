# `render()`

## Avoid declaring functions as props in `render()`

```jsx
return (
  <Foo onClick={() => { /* do something */ }}/>
);
```

From `Foo`'s perspective, every single render will result in a new `onClick` prop. Thus, the component will continually rerender. If possible, make the callback a class method if it needs access to `this`, or free-standing functions if it doesn't.

It is sometimes recommended to use arrow functions instead of class methods in components, to avoid the need to `bind()` in the constructor. While this is easier to write, it can be bad for performance, as arrow functions are treated as class properties and not class methods, and are not attached to the prototype. Thus, every new instance of the class will redeclare the function when created.

Besides in event handlers, a very common place this occurs is in refs. Most online examples demonstrate callback refs as follows: `ref={node => this.node = node}` This carries the same performance penalty.

**Note**: In general, consider how many children a component will have in the DOM tree. The closer to being a leaf a component is, and the less expensive rendering is, the less important it is to optimize this rerender.

## Avoid 'naked' arrays and objects as props in `render()`

```jsx
return (
  <Foo foo={[]} bar={{}}/>
);

// in Foo componentWillReceiveProps()
this.props.foo === nextProps.foo // false
this.props.bar === nextProps.bar // false
```

New identities will be recognized as prop updates. Common cases where these arise:

* Redux form inline validation function arrays
* Inline styles

## Avoiding arrow functions in `render()` when mapping

```jsx
renderCheckboxes() {
  const { options } = this.props;

  return options.map(opt => <Checkbox onClick={() => this.handleClick(opt)}/>);
  // ...or something like...
  return options.map(opt => <Checkbox onClick={this.handleClick.bind(opt)}/>);
}
```

Compared to the above example, this may seem harder to deal with. This can be resolved by declaring a helper component class, passing the event handler as a props, and defining a class method on the helper:

```jsx
renderCheckboxes() {
  const { options } = this.props;

  return options.map(opt => <HelperCheckbox opt={opt} onClick={onClick});
}

// in <HelperCheckbox/>
handleClick() {
  const { onClick, opt } = this.props;

  onClick(opt);
}

render() {
  return <Checkbox onClick={this.handleClick}/>;
}
```

The same note from above applies. Because this case takes comparatively more work to optimize, consider if it is even worthwhile to do.

## Avoid expensive logic in `render()`

If you have expensive logic within `render()`, consider moving it to a helper function. While you can't avoid running the complex operation, you can at least avoid redefining the process multiple times.

## `renderFoo()` helpers

Oftentimes we see `renderFoo()` being called in `render()` as some intermediary value. Consider if it makes sense to break this out into a separate component. This can help debugging, and make extending and refactoring easier in the future by separating concerns into separate files.