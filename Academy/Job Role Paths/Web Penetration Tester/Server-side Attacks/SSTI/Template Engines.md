# Template Engines

A template engine is software that combines pre-defined templates with dynamically generated data. Web applications commonly use template engines to generate dynamic responses while maintaining consistent structure across pages (e.g., shared headers and footers). Popular examples include Jinja and Twig.

## Templating

Template engines require two inputs:
- **Template**: A string or file with placeholders for dynamic values
- **Values**: Key-value pairs inserted at marked locations

Generating the final string from template and values is called **rendering**.

### Basic Example

Template syntax varies by engine. Using Jinja as an example:

```jinja2
Hello {{ name }}!
```

When rendered with `name="vautia"`, produces:

```txt
Hello vautia!
```

### Advanced Operations

Modern template engines support programming constructs like loops and conditionals:

```jinja2
{% for name in names %}
Hello {{ name }}!
{% endfor %}
```

Rendered with `names=["vautia", "21y4d", "Pedant"]`, produces:

```txt
Hello vautia!
Hello 21y4d!
Hello Pedant!
```
