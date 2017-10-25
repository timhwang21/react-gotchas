# `render()`

## Avoid expensive logic in `render()`

If you have expensive logic within `render()`, consider moving it to a helper function. While you can't avoid running the complex operation, you can at least avoid redefining the process multiple times.

## `renderFoo()` helpers

Oftentimes we see `renderFoo()` being called in `render()` as some intermediary value. Consider if it makes sense to break this out into a separate component. This can help debugging, and make extending and refactoring easier in the future by separating concerns into separate files.

Alternatively, `get Foo()` syntax can be used to be even more terse.