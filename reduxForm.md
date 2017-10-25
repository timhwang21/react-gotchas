# Redux Form

## Avoid `connect`ing all form values if you can avoid it

It is handy for a form to be able to access all its values. However, if the entire form values are made accessible, every time any value changes, a prop change will register and the entire form will rerender.

Sometimes it is unavoidable to access form values in a form. Consider using Redux Form's own selectors, such as the `formValueSelector()`.

## Be aware of default values

By default, if you do not pass `initialValues` to a form, Redux Form will try to set all registered field values to the empty string `''`. This is because React throws a warning when trying to pass `undefined` or `null` to an `<input/>` element.

If you strictly want the value passed to a component to be "empty", you have to add `parse` and `normalize` props to your `<Field/>` component to translate the empty string into whatever empty value you want.