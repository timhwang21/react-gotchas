# Higher order components

## Do not extend ComposedComponent

Several references online discuss the differences between extending `Component` versus the component passed to the HOC as an argument. While there are valid use-cases for both, extending `ComposedComponent` is INCOMPATIBLE with our `_Base` and `PureBase` autobinding components. Components that extend `ComposedComponent` will have an incorrect reference to `this`.

