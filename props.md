# Props

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
* "Namespaced" props (e.g. `footerProps`)

## Use helper components to avoid arrow functions in `render()` as props when creating component instances interatively

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

## Coerce boolean props to booleans

Because of how Javascript handles truthiness or falsiness, it can be less verbose to pass in props that will be used as booleans without coercing them.

```javascript
<Component hasValues={values.length}/>
```

There are two main downsides of not using `!!values.length` instead.

1. If `<Component/>` uses `propTypes`, it can throw an error when it gets a number instead of a boolean for `hasValues`.
2. The component may use boolean rendering logic such as `return props.hasValues && <ValueComponent/>`. Javascript boolean operators do not strictly return booleans, so if `props.hasValues === 0`, the string `"0"` will be rendered. This is because `falseyValue && truthyValue` returns `falseyValue`, not `false`.