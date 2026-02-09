# JavaScript

JavaScript is one of the most used languages in the world, primarily for web and mobile development. It typically runs on the front end within browsers, though back end implementations like **NodeJS** enable full-stack development.

While HTML and CSS control page appearance, JavaScript manages functionality and interactivity. Without it, web pages would be static and non-interactive.

## Loading JavaScript

JavaScript is loaded via the `<script>` tag:

```html
<script type="text/javascript">
    // JavaScript code
</script>
```

Remote scripts use the `src` attribute:

```html
<script src="./script.js"></script>
```

## Basic Example

```javascript
document.getElementById("button1").innerHTML = "Changed Text!";
```

This changes the content of the `button1` element. For experimentation, try [JSFiddle](https://jsfiddle.net/).

## Common Uses

- Real-time page updates and dynamic content
- User input processing
- HTTP requests via **Ajax** for back-end interaction
- Advanced animations combined with CSS
- Complex automation

Modern browsers execute JavaScript client-side without server intervention, enabling fast performance.

## Popular Frameworks

- **Angular**
- **React**
- **Vue**
- **jQuery**

See [framework comparisons](https://en.wikipedia.org/wiki/Comparison_of_JavaScript-based_web_frameworks) for more details.
