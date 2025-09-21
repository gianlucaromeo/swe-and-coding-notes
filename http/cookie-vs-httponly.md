# Normal Cookie vs. HttpOnly Cookie

> **Normal cookies** are readable and writable from JavaScript, while **HttpOnly cookies** are **server-only**.

### Normal Cookie
- Accessible by both client-side JavaScript and the server.
- Can be read or modified using `document.cookie`.
- Higher exposure to cross-site scripting (XSS) attacks since malicious scripts can exfiltrate cookie data.
- Useful when client-side code needs to directly interact with cookie values.

### HttpOnly Cookie
- Marked with the `HttpOnly` attribute.
- Inaccessible to client-side JavaScript; cannot be read or modified using `document.cookie`.
- Only sent automatically in HTTP requests to the server.
- Reduces attack surface against XSS by preventing scripts from accessing authentication tokens or session identifiers.

## Table of differences

| Feature                           | Normal Cookie                                                                                                  | HttpOnly Cookie                                                                          |
| --------------------------------- | -------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| **Accessibility from JavaScript** | Can be read or modified using `document.cookie`.                                                               | Cannot be accessed via JavaScript (`document.cookie` will not show it).                  |
| **Use case**                      | Storing non-sensitive data like UI preferences, feature flags, or analytics identifiers.                       | Storing sensitive data such as authentication tokens or session IDs.                     |
| **XSS protection**                | Vulnerable: if an attacker injects JavaScript (XSS), they can steal the cookie value.                          | Safer: even if an attacker injects JavaScript, the cookie cannot be read by the script.  |
| **Transmission**                  | Automatically sent with every HTTP request to the domain, unless restricted with flags (`Secure`, `SameSite`). | Same behavior: automatically sent with every request.                                    |
| **Setting**                       | Can be set by client-side JS (`document.cookie = "key=value"`).                                                | Must be set by the server in the `Set-Cookie` HTTP header with the `HttpOnly` attribute. |
| **Visibility**                    | Developer tools (Application → Cookies) will show the cookie and indicate if it’s `HttpOnly`.                  | Same, but flagged as `HttpOnly` and not modifiable via JS.                               |

### Example

**Normal cookie**

```http
Set-Cookie: theme=dark; Path=/; Secure; SameSite=Lax
```

**HttpOnly cookie**

```http
Set-Cookie: access_token=abc123; HttpOnly; Path=/; Secure; SameSite=Strict
```
