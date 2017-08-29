# Class methods

## Consider destructuring props you need

Compare the following:

```
doSomething() {
  const processedFoo = this.props.foo.filter(foo => foo === this.props.bar);

  this.props.onChange(processedFoo.concat(this.props.baz));
}
```

```
doSomething() {
  const { foo, bar, baz, onChange } = this.props;

  const processedFoo = foo.filter(foo => foo === bar);

  onChange(processedFoo.concat(baz));
}
```

React component props are basically instance variables, and instance variables that are used in a class method are basically arguments to that method. The purpose of a function signature is to give a quick overview of a function's dependencies. By destructuring props at the start of a method, the glance value of the method is dramatically increased -- it becomes immediately clear which props are used in the course of the function. This is extremely beneficial in refactoring.

Also, more of a personal opinion, but I think the latter reads much clearer.
```

## Consistent naming

Glance value of functions are important to allow quick grokking of code. Try to adhere to the following standards:

`getFoo()` - returns some computed value
`renderFoo()` - returns some JSX
`handleFoo()` - class method to be passed to child as event handler
`props.onFoo()` - prop that expects to receive an event handler
`fetchFoo()` - thunks that handle network requests