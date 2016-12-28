# Redux

## Naming Events

* When naming events, aim to describe the event instead of describing the effect of the event. For example, an event is fired when a form is submitted, as a result the order is saved. You should name the event `formSubmitted`, the past tense implies that the intended action has just happened, and not `saveOrder`, since saving an order is a reaction to the form being submitted and not actually descriptive of the functions action. This way, if the form later changes to do something different, the name remains relevant. Not only that, but new functionality can also react to that event firing.
* You can also set up props from the component's global state instead of the wrapping webpack file by using a connect decorator which will ignore the webpack file and get the initial props directly from the global state. Connector props and props passed in from the parent should be unique.
