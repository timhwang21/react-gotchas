# Redux Form

## Avoid `connect`ing all form values if you can avoid it

It is handy for a form to be able to access all its values. However, if the entire form values are made accessible, every time any value changes, a prop change will register and the entire form will rerender.

Sometimes it is unavoidable to access form values in a form. Consider using Redux Form's own selectors, such as the `formValueSelector()`.