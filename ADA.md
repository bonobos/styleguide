# ADA
## HTML Elements
### `<img>` Images
- Images have alternate text that can be read by screen reader software Level **A**
- Images are not used when text can acheive the same purpose  **AA**

```html
<img class="washed-chinos" alt="Blue Washed Chinos" src="http://www.bonobos.com/images/blue-washed-chino"/>
````

### `<form>` &   `<input>`Forms and Inputs
- Invalid form input is identified to the user **A**
- Forms have labels and legends that can be read by screen reader software  **A**
-  Keyboard focus is visible and clear. **AA**

If a form element has a visible label, add a `<label>` sibling element and put the ID as the `for` value.

```html
<form>
    <label for="email_address">Email Address</label>
    <input id="email_address" name="emailAddress" type="email" />
</form>
```
If the form does not have a visible label, you can use the `aria-label` tag for the same purpose and it will be compatible with screen readers.
```html
<form>
    <input id=""email_address name="emailAddress" type="email" aria-label="Email Address" />
</form>
```
