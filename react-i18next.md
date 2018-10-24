# react-i18next

## Be careful of Prettier inserting `{' '}` when using `<Trans/>`

`<Trans/>` uses tagged strings like `'<0>first node</0> second node <2>third node</2>'` to translate content like `<b>bold</b> unbolded <i>italicized</i>`.

Prettier formats long blocks of text in HTML by inserting `{' '}`, which is a fairly standard way of adding spaces between nodes in React. However, this added space is also treated as a node by `<Trans/>`.

Thus, the following are not equivalent, even though Prettier may silently format the first into the second:

```jsx
/*
  <0/> is <b>bold</b>
  <1/> is ' unbolded '
  <2/> is <i>italicized</i>
 */
<span>
  <b>bold</b> unbolded <i>italicized</i>
</span>

/*
  <0/> is <b>bold</b>
  <1/> is ' '
  <2/> is 'unbolded'
  <3/> is ' '
  <4/> is <i>italicized</i>
 */
<span>
  <b>bold</b>
  {' '}unbolded{' '}
  <i>italicized</i>
</span>
```
