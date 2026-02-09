# HTML

HTML (HyperText Markup Language) is the foundational component of web applications' front end. It defines the basic structure of web pages, including titles, forms, images, and other elements that browsers interpret and display to users.

## Basic Example

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Page Title</title>
    </head>
    <body>
        <h1>A Heading</h1>
        <p>A Paragraph</p>
    </body>
</html>
```

## HTML Structure

HTML elements follow a tree structure:

```
document
├── html
    ├── head
    │   └── title
    └── body
        ├── h1
        └── p
```

Each HTML element is enclosed in opening and closing tags (e.g., `<p>content</p>`). Tags can include attributes like `id` or `class` for styling: `<p id="para1">`.

## URL Encoding

Browsers only recognize ASCII characters in URLs. Non-ASCII characters must be percent-encoded as `%` followed by two hexadecimal digits.

| Character | Encoding |
|-----------|----------|
| space | %20 |
| ! | %21 |
| " | %22 |
| # | %23 |
| $ | %24 |
| % | %25 |
| & | %26 |
| ' | %27 |
| ( | %28 |
| ) | %29 |

Tools like Burp Suite can encode/decode characters and strings.

## Usage

- `<head>`: Contains metadata (title, styles, scripts) not displayed on the page
- `<body>`: Contains visible page content
- `<style>`: Holds CSS code
- `<script>`: Holds JavaScript code

### DOM (Document Object Model)

The W3C defines DOM as a platform-neutral interface for dynamically accessing and updating document content, structure, and style.

**Three DOM standards:**
- **Core DOM** – Standard model for all document types
- **XML DOM** – Standard model for XML documents
- **HTML DOM** – Standard model for HTML documents

Understanding DOM structure helps locate elements by `id`, tag name, or class, which is essential for identifying vulnerabilities (e.g., XSS) and manipulating page elements.
