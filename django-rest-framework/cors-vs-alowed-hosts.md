# CORS vs ALLOWED_HOSTS

* `ALLOWED_HOSTS` = “Which hostnames can serve Django directly.”

* CORS = “Which origins are allowed to make cross-origin JS calls to Django.”

```
Not in ALLOWED_HOSTS -> Django rejects with 400
Not in CORS_ALLOWED_ORIGINS -> Django still processes and sends reponse -> Browser refuses to pass it to JS
```

## `ALLOWED_HOSTS`

`ALLOWED_HOSTS` lets you specify the host/domain the backend/server can run on.

* It is useful server-side to prevent HTTP host header attacks.

* Django checks the `Host:` header in the HTTP request and if this is not in `ALLOWED_HOSTS` it rejects the request with a *400 Bad Request*.



## CORS

CORS lets you define which frontend/client origins are allowed to call your backend (apart from the ones on the same scheme, domain, and port). It has nothing to do with which domain you serve your backend app on. It only allows your frontend apps to interact with your APIs without having to deploy 
all of them on the same domain.

* It is useful browser-side to restrict requests on making cross-origin requests.

* The browser checks the `Origin:` header when JS in one site calls another domain/port.

* It the server doesn't reply with `Access-Control-Allow-Origin`, the browser blocks the response.

CORS is only relevant for browsers. Other servers/scripts/programs will still be able to call your API.

```
React app (http://localhost:3000) fetches 'http://127.0.0.1/auth/login'

Browser adds "Origin: http://localhost:3000" to the request

Django receives the request. If django-cors-headers is configured, it adds:
    'Access-Control-Allow-Origin: http://localhost:3000'

Browser inspects response:
    header matches origin 
        ? release response to JS 
        : block access in JS, even if request reached Django
```