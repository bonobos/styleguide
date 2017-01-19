# ADA 
#### Note 
**A** versus **AA** indicates compliance level.

## HTML Elements

### Images `<img>` 
- Images have alternate text that can be read by screen reader software **A**
- Images are not used when text can acheive the same purpose (aka don't ever have words on an image) **AA**

```html
<img class="washed-chinos" alt="Blue Washed Chinos" src="http://www.bonobos.com/images/blue-washed-chino"/>
````

### Forms and Inputs `<form>` & `<input>`
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

### SVGs `<svg>` 
- Images have alternate text that can be read by screen reader software **A**
- Images are not used when text can acheive the same purpose  **AA**


### Anchors `<a>`
- There are no empty links. **A**
- Links are clearly and logically named. **A**
- Keyboard focus is visible and clear. **AA**
- Underlined text that does not provide a link is removed.**AA** 
- Redundant links on the same page are eliminated or minimized. **AA**

```html
<a class="btn" href="/guideshop">find the nearest guideshop</a>
```

![anchor](images/anchor.png)

### Buttons `<button>`
-  Buttons are clearly and logically named. **A**
- Keyboard focus is visible and clear. **AA**
- Buttons are used consistently regardless of the user’s location in the site. **AA**

```html
<button class="gift-button">Redeem</button>
```

![button](images/button.png)

### Headings `<h1> ... <h6>`
- Headings are presented in logical order. **A**
- There are no empty heading tags **A**

```html
<h1 class="title">Get an Extra 30% Off Final Sale Items</h1>
<h2 class="description">Use code GETIT30</h2>
```

### Video `<video>`
- Recorded video content includes captions. **A**
- Video content is accompanied by text transcript or description. **A**
- Links are provided to media players required to view content. **A**

![video](images/video.png)

### Title `<title>`
-  Page titles clearly and succinctly describe page content. **A**
```html
<title data-page-category="Homepage">Better-Fitting, Better-Looking Men's Clothing &amp; Accessories | Bonobos</title>
```
