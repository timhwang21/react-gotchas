# Higher order components

## (RocketFuel) Do not extend ComposedComponent

Several references online discuss the differences between extending `Component` versus the component passed to the HOC as an argument. While there are valid use-cases for both, extending `ComposedComponent` is INCOMPATIBLE with our `_Base` and `PureBase` autobinding components. Components that extend `ComposedComponent` will have an incorrect reference to `this`.

## Hoist statics

Use a package like [`hoist-non-react-statics`](https://github.com/mridgway/hoist-non-react-statics) to propagate statics to the higher order component.

React documentation reference: [Static Methods Must Be Copied Over](https://reactjs.org/docs/higher-order-components.html#static-methods-must-be-copied-over)