# Components

## Respect modular components!

If a component is built to be reusable and agnostic to where it is used, try to avoid shunting in logic to handle special cases. Keep reusable components as flexible as possible.

On the other hand, people often build "wrapper components" for reusable form controls that add some functionality that the core component does not implement. When doing so, consider if others may benefit from this added functionality, and if it makes sense to do so, add that behavior to the core component.

**Fix**: think carefully about what the right level of abstraction is for your feature. If your change is relevant only to one particular feature, it probably doesn't belong in a component shared by features other than the feature you care about.

## Don't implement pseudo-class methods on functional components

Functional components are nice. They are generally more elegant and concise, and have a very pleasant syntax.

They also do not have constructors, and do not support class methods.

```jsx
const SomeComponent = props => {
  const handleClick = () => { props.onClick; }

  return (
    <div onClick={handleClick}>
      I'm so stateless and pure!
    </div>
  );
}
```

Here, `handleClick()` is a function masquerading as a class method. However, it is not on the prototype of `<SomeComponent/>`, and thus is redefined every time `<SomeComponent/>` rerenders.

**Note**: The React team has been pretty vocal about their intention to optimize for functional components. So somewhere down the line, functional components may be much more performant. If you ever examine the transpiled code that Babel outputs, currently functional components end up calling `React.CreateClass` under the hood anyways.

**Fix**: Refactor the functional component into a class-based component, and make `handleClick` a class method.

## `Component` or `PureComponent`?

The tradeoff:

* `Component` renders FASTER when props change often, and SLOWER when props do not change often.
* `PureComponent` renders SLOWER when props change often (because of the added shallow equal checks), and FASTER when props do not change often (because renders are saved).

## Add `displayName` to functional components

If declared using fat arrow syntax, functional components do not have a display name by default. This hurts debugging, because:

1. In the React devtools, the functional component is not searchable by name
2. React perf tools get confused, and lump all render time of functional components together, leading to misleading results

This can be avoided by using `function()` syntax, but that can be more verbose. The trade-off is up to the developer.

## Do not rely on `displayName` in code

React strips `displayName` from all components by default in production mode. Thus, if you have code that examines components or instances for their `displayName` to make a decision, that code will break. If you must do generic programming with components, you can set a non-`displayName` `static` property on the component to use as a flag.

## Avoid using decorator syntax (`@`)

The current form of decorators provided by Babel exists as a stage 2 proposal pending updates, and the syntax is still up in the air. More importantly, exporting decorated components can cause unexpected behavior in unit tests (for example, importing a component decorated with `@withRouter` can lead to test failures).

Plain components should be exported as named exports, while decorated components should be exported as the default export. `compose()` from either `redux` or `lodash` can be used to make decorator chains more readable.
