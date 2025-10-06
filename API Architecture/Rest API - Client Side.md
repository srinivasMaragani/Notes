## 🌍 1. Base Understanding — What a Web Client Does

A **web client** is any application that sends HTTP requests to a **RESTful API** and processes HTTP responses.

-   Examples: Angular app, React app, Postman, curl, or backend service calling another API.
    
-   It uses the **HTTP protocol** (usually over HTTPS/TLS) to communicate.
    

A RESTful API client must know how to:

-   Construct HTTP requests (with method, headers, body).
    
-   Handle HTTP responses (status codes, headers, body).
    
-   Handle caching, authentication, error recovery, and rate limits.
    

***

## ⚙️ 2. HTTP Request Fundamentals

Each REST request has key components:

| Component | Description |
| --- | --- |
| **HTTP Method** | Defines action on resource (GET, POST, PUT, PATCH, DELETE, OPTIONS, HEAD). |
| **URL / Endpoint** | The resource path (e.g., `/users/123`). |
| **Headers** | Metadata like authentication, content type, caching rules, etc. |
| **Body** | Data sent to server (mainly for POST/PUT/PATCH). |
| **Query Params** | Used for filtering, sorting, pagination, etc. (e.g., `/users?page=2`). |

***

## 📦 3. HTTP Methods (CRUD Mapping)

| Method | Meaning | Typical Use |
| --- | --- | --- |
| **GET** | Read | Retrieve resource(s). |
| **POST** | Create | Add new resource. |
| **PUT** | Update (replace) | Replace full resource. |
| **PATCH** | Update (partial) | Update part of a resource. |
| **DELETE** | Delete | Remove resource. |
| **OPTIONS** | Discover | Returns allowed methods for resource. |
| **HEAD** | Metadata only | Like GET but returns only headers. |

***

## 📤 4. HTTP Headers (Essential for Clients)

| Header | Purpose |
| --- | --- |
| **Authorization** | Used for authentication (Bearer token, Basic auth, etc.). |
| **Accept** | Declares what content types client can handle (e.g., `application/json`). |
| **Content-Type** | Declares what format client is sending (e.g., `application/json`). |
| **Cache-Control** | Controls caching behavior (e.g., `no-cache`, `max-age=3600`). |
| **If-None-Match / ETag** | Used for conditional requests and cache validation. |
| **User-Agent** | Identifies client application. |
| **X-Request-ID / Idempotency-Key** | Used to uniquely identify a request (for retries). |

***

## 📡 5. URL & Resource Design (Client Awareness)

-   URLs represent **resources**, not actions (e.g., `/users/123` not `/getUserById`).
    
-   Use query parameters for filtering/sorting:  
    `/users?role=admin&sort=asc&page=2`
    
-   Clients should not hardcode full URLs — base URL should be configurable (e.g., dev/staging/prod).
    

***

## 🔄 6. Caching (Client-Side REST Concept)

Caching reduces redundant API calls.

-   **LocalStorage / IndexedDB**: Persist data in browser.
    
-   **In-Memory Cache**: Hold recent responses during session.
    
-   **HTTP Cache Headers**:
    
    -   `Cache-Control`
        
    -   `Expires`
        
    -   `ETag` / `If-None-Match`
        
-   The client uses these to decide if it can reuse cached responses or must re-fetch.
    

***

## 🔐 7. Authentication & Authorization

Clients must send credentials securely, usually via headers:

| Auth Type | Description | Example |
| --- | --- | --- |
| **Basic Auth** | Base64 encoded username/password | `Authorization: Basic YWRtaW46cGFzcw==` |
| **Bearer Token (OAuth2, JWT)** | Access token after login | `Authorization: Bearer eyJhbGciOi...` |
| **API Key** | Static key in header or query | `x-api-key: abc123` |

Clients must securely store tokens (e.g., session storage, not localStorage for JWTs).

***

## 🌐 8. CORS (Cross-Origin Resource Sharing)

**CORS** is enforced by browsers (not servers).

-   It restricts scripts in one domain (say, `frontend.com`) from calling APIs in another (`api.backend.com`) unless the server allows it.
    
-   The server must send headers like:
    
    `Access-Control-Allow-Origin: https://frontend.com Access-Control-Allow-Methods: GET, POST, PUT, DELETE Access-Control-Allow-Headers: Content-Type, Authorization`
    
-   Clients don’t control CORS; they just face errors when APIs don’t permit cross-origin access.
    

***

## 🧩 9. Content Negotiation

The client tells the server **what format** it wants data in:

`Accept: application/json`

The server may respond with:

`Content-Type: application/json`

or

`Content-Type: application/xml`

So the client must parse the response accordingly.

***

## 🧾 10. Response Codes & Handling

| Status | Meaning | Example Client Action |
| --- | --- | --- |
| **200 OK** | Success | Process data |
| **201 Created** | Resource created | Show success message |
| **204 No Content** | Success with no data | Do nothing |
| **400 Bad Request** | Invalid input | Show validation errors |
| **401 Unauthorized** | Invalid credentials | Redirect to login |
| **403 Forbidden** | No permission | Show “Access denied” |
| **404 Not Found** | Resource missing | Show “Not found” |
| **409 Conflict** | Duplicate or version issue | Retry or alert |
| **429 Too Many Requests** | Rate limit reached | Wait or retry later |
| **500 Server Error** | API fault | Log & notify user |

***

## 🔁 11. Idempotency

-   **Idempotent operations** = same request, same effect even if repeated.
    
    -   Example: `PUT /users/1` with same payload multiple times.
        
-   **Non-idempotent** = repeating changes results (e.g., `POST /orders` creates multiple orders).
    
-   To make POST safe for retries, use **Idempotency-Key** header:
    
    `Idempotency-Key: 123e4567-e89b-12d3-a456-426614174000`
    

***

## ⏳ 12. Pagination & Filtering

Clients should handle pagination to avoid overloading the server.

### Common styles:

-   `?page=2&limit=10`
    
-   `?offset=20&count=10`
    

Response includes metadata:

`{   "data": [...],   "page": 2,   "total_pages": 10,   "total_records": 100 }`

Clients use these to implement “Load more” or infinite scrolling.

***

## 📉 13. Rate Limiting Awareness

APIs often limit requests to prevent abuse.

Server includes headers:

`X-RateLimit-Limit: 1000 X-RateLimit-Remaining: 950 X-RateLimit-Reset: 1728153600`

Client should:

-   Stop sending new requests when limit is reached.
    
-   Retry after cooldown using exponential backoff.
    

***

## ⚠️ 14. Error Handling & Retry Logic

Clients must be ready for:

-   Network timeouts
    
-   5xx server errors
    
-   Rate limit (429)
    
-   Token expiry (401)
    

### Best practices:

-   Retry with exponential delay (e.g., 1s → 2s → 4s).
    
-   Stop after few retries.
    
-   Use Idempotency keys for POST retries.
    

***

## 🧠 15. Versioning Awareness

Client must specify which API version it’s consuming.

Common formats:

-   In URL: `/api/v1/users`
    
-   In Header: `Accept: application/vnd.api+json;version=2`
    

This avoids breaking client when server updates.

***

## 📚 16. API Client Libraries

Instead of writing raw HTTP, clients use libraries:

-   **JavaScript**: Axios, Fetch API, Angular HttpClient.
    
-   **.NET**: `HttpClient`, `Refit`.
    
-   **Python**: `requests`.
    
-   **Java**: `RestTemplate`, `HttpClient`.
    

These handle headers, body parsing, and response promises.

***

## ⚙️ 17. Request Throttling (Client-Side Logic)

Clients should not overwhelm servers.

-   Implement debounce or delay on user actions (like search input).
    
-   Batch multiple requests if possible.
    
-   Cancel pending requests when user navigates away.
    

***

## 🧰 18. Logging & Monitoring

Clients should log:

-   Request URL, headers, and body (excluding sensitive data)
    
-   Response time & status
    
-   Retry attempts
    

Helps diagnose network or server issues.

***

## 📄 19. Security Practices

-   Always use **HTTPS**.
    
-   Avoid storing tokens in insecure storage.
    
-   Sanitize data before displaying.
    
-   Validate server certificate (TLS).
    

***

## 🧩 20. Client-Side REST Design Patterns

| Pattern | Purpose |
| --- | --- |
| **Repository Pattern (client)** | Organize API calls by resource (e.g., UserService, AuthService). |
| **DTOs (Data Transfer Objects)** | Map API responses to clean client models. |
| **Interceptor (middleware)** | Attach headers or handle global errors automatically. |
| **Reactive Patterns** | Use Observables/Promises to handle async API calls. |