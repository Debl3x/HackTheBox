# Automatic Modification

We may want to apply certain modifications to all outgoing HTTP requests or all incoming HTTP responses in certain situations. In these cases, we can utilize automatic modifications based on rules we set, so the web proxy tools will automatically apply them.

## Automatic Request Modification

Let us start with an example of automatic request modification. We can choose to match any text within our requests, either in the request header or request body, and then replace it with different text. For the sake of demonstration, let's replace our User-Agent with `HackTheBox Agent 1.0`, which may be handy in cases where we may be dealing with filters that block certain User-Agents.

### Burp Match and Replace

We can go to **Proxy > Proxy settings > HTTP match and replace rules** and click on Add in Burp. As the screenshot below shows, we will set the following options:

| Option | Value | Description |
|--------|-------|-------------|
| Type | Request header | Since the change we want to make will be in the request header and not in its body. |
| Match | `^User-Agent.*$` | The regex pattern that matches the entire line with User-Agent in it. |
| Replace | `User-Agent: HackTheBox Agent 1.0` | This is the value that will replace the line we matched above. |
| Regex match | True | We'll use regex to match any value that matches the pattern we specified above. |

Once we enter the above options and click Ok, our new Match and Replace option will be added and enabled and will start automatically replacing the User-Agent header in our requests with our new User-Agent.

### ZAP Replacer

ZAP has a similar feature called **Replacer**, which we can access by pressing `[CTRL+R]` or clicking on Replacer in ZAP's options menu. Click on Add and add the same options:

| Option | Value |
|--------|-------|
| Description | HTB User-Agent |
| Match Type | Request Header |
| Match String | User-Agent |
| Replacement String | HackTheBox Agent 1.0 |
| Enable | True |

ZAP also provides the option to set the Initiators, which we can access by clicking on the other tab in the window shown above. Initiators enable us to select where our Replacer option will be applied. We will keep the default option of **Apply to all HTTP(S) messages**.

## Automatic Response Modification

The same concept can be used with HTTP responses as well. To automatically enable any characters in the IP field for easier command injection, we can go to **Proxy > Options > Match and Replace** in Burp to add another rule.

This time we will use the type of **Response body** since the change we want to make exists in the response's body. We do not have to use regex as we know the exact string we want to replace.

| Option | Value |
|--------|-------|
| Type | Response body |
| Match | `type="number"` |
| Replace | `type="text"` |
| Regex match | False |

Try adding another rule to change `maxlength="3"` to `maxlength="100"`.

### ZAP Replacer for Response Modification

**Exercise 1:** Apply the same rules with ZAP Replacer:

**Change input type to text:**
- Match Type: Response Body String
- Match Regex: False
- Match String: `type="number"`
- Replacement String: `type="text"`
- Enable: True

**Change max length to 100:**
- Match Type: Response Body String
- Match Regex: False
- Match String: `maxlength="3"`
- Replacement String: `maxlength="100"`
- Enable: True

### Additional Exercises

**Exercise 2:** Try adding a rule that automatically adds `;ls;` when we click on Ping, by matching and replacing the request body of the Ping request.
