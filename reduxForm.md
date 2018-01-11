# Redux Form

## Avoid `connect`ing all form values if you can avoid it

It is handy for a form to be able to access all its values. However, if the entire form values are made accessible, every time any value changes, a prop change will register and the entire form will rerender.

Sometimes it is unavoidable to access form values in a form. Consider using Redux Form's own selectors, such as the `formValueSelector()`. If using `formValueSelector()` with multiple fields, consider using a cached version of the selector as illustrated below to avoid creating new object references:

```javascript
const formValueReselector = formName => fields => {
  const formSelector = formValueSelector(formName);
  return createSelector(fields.map(field => state => formSelector(state, field)), (...values) =>
    fields.reduce((acc, field, idx) => ({ ...acc, [field]: values[idx] }), {}),
  );
};

const mapStateToProps = () => {
  const mySelector = formValueReselector('myForm')(['field1', 'field2']);
  return (state, props) => ({formValues: mySelector(state});
};
```

## Be aware of default values

By default, if you do not pass `initialValues` to a form, Redux Form will try to set all registered field values to the empty string `''`. This is because React throws a warning when trying to pass `undefined` or `null` to an `<input/>` element.

If you strictly want the value passed to a component to be "empty", you have to add `parse` and `normalize` props to your `<Field/>` component to translate the empty string into whatever empty value you want.

## Be aware of passing props with changing object references

This applies to all components, but is particularly tempting for `<Field/>`s. Some field props are much easier to used when declared on-the-fly, for example composed rules, or arrays of rules:

```javascript
render() {
  const rules = [
    withMsg('Some custom message')(required),
    max(10),
  ];

  return (
    <Field {...otherProps} validate={rules}/>
  );
}
```

Every time `render()` is called, both rules are recreated as new functions, and the `rules` array is recreated as a new array object. This leads to a rerender. In a form with many fields, the performance impact can be very noticeable.

## Be aware of blur semantics

Blur is core to our validation display logic, so when writing components it's important to know how blurring fits into the Redux Form life cycle. Blur handlers (and other event handlers in general) provided by Redux Form are polymorphic -- the first argument is an "`eventOrValue`". This can lead to confusion for user actions that don't have associated events, per se.

`onBlur()` tries to set field value when called, which is important to note if a component has an internal data processing step (such that the raw value is not what you want to use as `value`). Importantly, `onBlur()` does different things depending on what the first argument is:
* `Event`: tries to access `event.target.value` and sets the field value to this
* `undefined` (or no argument): does not try to set field value (but dispatches a blur action)
* Anything else: directly uses the passed argument as the field value

## `onChange()` vs. lifecycle for dependent fields

It seems natural to pass a change handler to a `<Field/>` if you want another field's value to change when this field changes. However, the change handler will miss any change that was not caused by a change event. Examples include cascading changes (changing field 1 changes field 2, which changes field 3), or entirely external changes (results of a network request cause a field to change). Thus, it's safer to rely on React lifecycle methods to detect field values changing.

Of course, in trivial cases it is both less verbose and more readable to use `onChange()`. Use your best judgment, keeping in mind the benefits of each, and the fact that mixing both semantics in the same form is probably the most confusing approach.
