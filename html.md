# HTML

## Security

* When using an `a` tag with `target="_blank"`, make sure you append `rel="noopener"` so avoid a [phishing vulnerability](https://dev.to/ben/the-targetblank-vulnerability-by-example).

```html
<a href="https://example.com" rel="noopener" target="_blank">click here</a>
```
