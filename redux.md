# Redux

## Naming Events

When naming events, aim to describe the event instead of describing the effect of the event.
For example, if you are writing a function that is called when a form is submitted that saves an order, you should name the function `submittedForm()` or `formSubmitted()`, something in past tense implying that the intended action has just happened, and not `saveOrder()`, since saving an order is a reaction to the form being submitted and not actually descriptive of the function's action. This way if the form later changes to do something different, the name remains relevant.
