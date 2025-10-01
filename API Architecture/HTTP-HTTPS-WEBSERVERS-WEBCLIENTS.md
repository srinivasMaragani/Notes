# **Notes: HTTP, HTTPS, and Web Clients/Servers**

***

## **1️⃣ HTTP (Hypertext Transfer Protocol)**

### **Definition**

-   HTTP is an **application layer protocol** used to **transfer data (text, HTML, JSON, images) over the Internet**.
    
-   It is **stateless**, meaning each request is independent.
    

### **Key Features**

1.  **Request/Response model:** Client sends a request; server sends a response.
    
2.  **Methods:** Common ones are:
    
    -   GET → fetch data
        
    -   POST → send data
        
    -   PUT → update data
        
    -   DELETE → remove data
        
3.  **Headers:** Metadata about request/response (Content-Type, Authorization, etc.)
    
4.  **Body:** Optional payload (JSON, HTML, form data)
    

### **Example**

`GET /api/users HTTP/1.1 Host: example.com Accept: application/json`

-   Response:
    

`HTTP/1.1 200 OK Content-Type: application/json {"id":1,"name":"Alice"}`

### **Role**

-   Enables communication between **client applications (browser/curl)** and **servers**.
    
-   Carries structured data and commands.
    

***

## **2️⃣ HTTPS (Hypertext Transfer Protocol Secure)**

### **Definition**

-   HTTPS = **HTTP over TLS**.
    
-   Provides **encryption, authentication, and data integrity**.
    

### **Key Features**

1.  **Encryption:** Data is unreadable to eavesdroppers.
    
2.  **Authentication:** Verifies server identity via **SSL/TLS certificate**.
    
3.  **Data integrity:** Ensures response is not tampered.
    

### **How it Works**

1.  TLS handshake establishes a **session key**.
    
2.  HTTP request is **encrypted** using the session key.
    
3.  Server decrypts, processes request, and encrypts response.
    

### **Example**

`curl -v https://example.com/api/users`

-   Shows TLS handshake, encrypted request, and decrypted response.
    

### **Role**

-   Secures communication over the Internet.
    
-   All modern websites use HTTPS by default.
    

***

## **3️⃣ Web Clients**

### **a. Browser**

-   A **graphical client** to access web pages.
    
-   Responsibilities:
    
    1.  Sends HTTP/HTTPS requests
        
    2.  Parses HTML, CSS, JS
        
    3.  Renders content
        
    4.  Executes scripts (JavaScript)
        
-   **Example:** Chrome, Firefox, Edge
    

***

### **b. curl**

-   A **command-line tool** for making HTTP requests.
    
-   Can send GET, POST, PUT, DELETE with headers, payloads, authentication.
    
-   **Example:**
    

`curl -X POST https://example.com/api/users \      -H "Content-Type: application/json" \      -d '{"name":"Alice"}'`

-   Role: Testing APIs, debugging HTTP traffic, scripting automated requests.
    

***

### **c. wget**

-   Command-line **file downloader**.
    
-   Can fetch files over HTTP, HTTPS, FTP, and mirror websites.
    
-   Less convenient than curl for complex API requests.
    
-   **Examples:**
    

`wget https://example.com/file.zip wget --method=POST --body-data="name=Alice" https://example.com/api/users`

-   Role: Downloading files, recursive website download, resuming interrupted downloads.
    

***

## **4️⃣ Web Servers**

### **a. Nginx**

-   High-performance **web server, reverse proxy, load balancer**.
    
-   Handles:
    
    1.  TCP connections
        
    2.  HTTP/HTTPS parsing
        
    3.  TLS termination
        
    4.  Routing requests to backend apps
        
    5.  Static file serving
        
-   **Example Configuration:**
    

`server {     listen 443 ssl;     server_name example.com;      ssl_certificate /etc/ssl/certs/example.crt;     ssl_certificate_key /etc/ssl/private/example.key;      location /api/ {         proxy_pass http://127.0.0.1:5000;     } }`

-   Role: Receives HTTP requests, forwards to backend, handles TLS, caching, load balancing.
    

***

### **b. Kestrel**

-   **Built-in web server for ASP.NET Core**.
    
-   Handles HTTP requests **inside the application**.
    
-   Usually runs behind a reverse proxy like Nginx in production.
    
-   **Example (C#):**
    

`var builder = WebApplication.CreateBuilder(args); var app = builder.Build();  app.MapGet("/api/users", () => new { Id = 1, Name = "Alice" });  app.Run();`

-   Role: Executes application code, returns HTTP response. Kestrel relies on OS networking and optionally a reverse proxy for TLS/production optimizations.
    

***

## **5️⃣ Summary Table**

| Component | Type | Role | Example |
| --- | --- | --- | --- |
| HTTP | Protocol | Application layer communication | GET / POST / DELETE requests |
| HTTPS | Protocol | Secure HTTP (TLS) | HTTPS requests |
| Browser | Client | Render pages, send HTTP requests | Chrome, Firefox |
| curl | Client | Command-line HTTP requests | API testing, debugging |
| wget | Client | Download files over HTTP/HTTPS | Website/file download |
| Nginx | Server | Web server, TLS termination, reverse proxy | Handles requests, forwards to app |
| Kestrel | Server | Application web server for ASP.NET | Processes requests inside app |

***

✅ **Key Takeaways**

1.  **Clients** (Browser, curl, wget) generate HTTP/HTTPS requests.
    
2.  **Servers** (Nginx, Kestrel) receive requests, parse HTTP, handle TLS, and respond.
    
3.  **HTTP** = plain protocol; **HTTPS** = encrypted protocol via TLS.
    
4.  Nginx is often **frontline** for TLS, caching, routing; Kestrel is **backend app server** executing logic.