# HTML Injection

## Overview

A critical aspect of front-end security is validating and sanitizing user input. While validation often occurs on the back end, some input is processed entirely on the front end and never reaches the server. Therefore, input validation must happen on **both** the front end and back end.

HTML injection occurs when unfiltered user input is rendered directly on a page—either from a database (like user comments) or through front-end JavaScript. This allows attackers to inject malicious HTML, such as fake login forms to steal credentials, or deface the page entirely with modified content or ads.

## Examples of HTML Injection

**Malicious Login Forms**: Attackers can inject HTML to create phishing pages that capture user credentials.

**Web Page Defacing**: Injecting HTML to change the page's appearance, insert ads, or completely alter content, causing severe reputational damage.

## Practical Example

Consider a simple page with a button that prompts for a name and displays it:

```html
<!DOCTYPE html>
<html>
<body>
    <button onclick="inputFunction()">Click to enter your name</button>
    <p id="output"></p>

    <script>
        function inputFunction() {
            var input = prompt("Please enter your name", "");
            if (input != null) {
                document.getElementById("output").innerHTML = "Your name is " + input;
            }
        }
    </script>
</body>
</html>
```

Without input sanitization, this is vulnerable to HTML injection. An attacker could inject:

```html
<style> body { background-image: url('https://academy.hackthebox.com/images/logo.svg'); } </style>
```

The browser would render this as legitimate HTML, changing the page's background image immediately. Since the injection occurs on the front end, refreshing the page resets the changes.

**Note**: This vulnerability is particularly dangerous because attackers have complete control over how their input is displayed.
