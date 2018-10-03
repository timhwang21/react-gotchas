# `mapDispatchToProps()`

## Using props in action creators can cause rerenders

From the Redux documentation:

> If your mapDispatchToProps function is declared as taking two parameters, it will be called with dispatch as the first parameter and the props passed to the connected component as the second parameter, **and will be re-invoked whenever the connected component receives new props.**

Normally, this will not affect rerenders, because the bound action creators will be re-created when props change, which causes a rerender anyways. However, when implementing `shouldComponentUpdate()`, using props may cause unexpected rerenders.

**Fix**: Instead of accessing the prop in `mapDispatchToProps`, make the bound action creator take an argument, and define a class method that passes in the necessary props to the action creator as arguments.

## Passing bound action creators as props can cause rerenders

Oftentimes it is useful to pass bound action creators as props. However, because of the above issue, this can cause unwanted rerenders when passing the props to components implementing `shouldComponentUpdate()` or extending `PureComponent`.

**Fix**: Again, wrapping the action creator in a class method in the parent will avoid this issue.
