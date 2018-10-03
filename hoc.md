# Higher order components

## Hoist statics

Use a package like [`hoist-non-react-statics`](https://github.com/mridgway/hoist-non-react-statics) to propagate statics to the higher order component.

React documentation reference: [Static Methods Must Be Copied Over](https://reactjs.org/docs/higher-order-components.html#static-methods-must-be-copied-over)

## Consider using render props

Render props are an alternative way to extend components. Whereas higher order components extend behavior at component definition time, render props extend behavior at render time, which can be more flexible.

See the React documentation on [renderprops](https://reactjs.org/docs/render-props.html).
