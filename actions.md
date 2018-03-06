# Actions

## Actions should be pure

One large advantage Redux gives us is that its [event sourcing](https://martinfowler.com/eaaDev/EventSourcing.html) approach allows us to replay history. However, if our actions are impure, our regenerated state will be incorrect.

Most know that actions should be pure, but it is easy to accidentally introduce impurity, especially in actions that contain logic.

Consider the following action (using a pseudocode-ish thunk):

```javascript
const fetchDataForLastWeek = async (dispatch) => {
	const data = await api.get(
		'some/endpoint',
		params: {
			startDate: moment().subtract(7, 'days'),
			endDate: moment(),
		},
	);

	return dispatch(Constants.LAST_WEEK_DATA_FETCHED, data);
}
```

Because the action body creates the current time, replaying this action from the event log will lead to a different API request. If the action were to truly be pure, the date should be passed as an argument:

```diff
-const fetchDataForLastWeek = async (dispatch) => {
+const fetchDataForLastWeek = endDate => async (dispatch) => {
	const data = await api.get(
		'some/endpoint',
		params: {
-			startDate: moment().subtract(7, 'days'),
+			startDate: endDate.subtract(7, 'days'),
-			endDate: moment(),
+			endDate,
		},
	);

	return dispatch(Constants.LAST_WEEK_DATA_FETCHED, data);
}
```

Note that accessing the API is itself impure, but we have to assume that two equivalent calls to the API give the same output (which isn't a strong guarantee). Martin Fowler describes a [gateway](https://martinfowler.com/eaaCatalog/gateway.html) as a possible alternative, e.g. a caching layer, but this comes with its own set of issues.