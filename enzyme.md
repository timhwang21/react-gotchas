# [Enzyme](https://airbnb.io/enzyme/)

## Use `data-test` attributes to tag elements

Do not overload class names and IDs for element identification in tests, as they have other purposes and hence have other reasons to change. Create dedicated `data-test` attributes. It's further recommended to use a consistent naming scheme like BEM to tag these.

## Avoid using node type to filter in `.find()`

Enzyme forwards DOM attributes from React nodes to regular DOM nodes. This can result in multiple elements (especially common for Styled Components) having the same `data-test` attribute. It can be tempting to instead search by something like `.find('span[data-test="my-test-attr"]')`; however, this makes your test unnecessarily brittle.

Instead, use the `.hostNodes()` method, which filters out all non-HTML nodes from the Enzyme wrapper.

```typescript
// This breaks if using Styled Components or something
const node = wrapper
  .find('[data-test="my-test-attr"]')
  .text()
  .equals('something')
// text() is meant to be called on 1 node; found >1 instead

// Don't do this -- the fact the node is a span is an implementation detail
// Test behavior, not implementation!
const node = wrapper
  .find('span[data-test="my-test-attr"]')
  .text()
  .equals('something')

// Do this instead!
const node = wrapper
  .find('span[data-test="my-test-attr"]')
  .hostNodes()
  .text()
  .equals('something')
```

