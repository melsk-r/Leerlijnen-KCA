---
title: "4.4 HTTP-protocol voor REST-API's"
date: 2026-03-04
weight: 5
leerlijn: 4
paragraaf: "4.4"
leerdoel: "Leerdoel nog toe te voegen"
---


## 4.4 HTTP-protocol voor REST-API's

Beheerst HTTP als protocol voor REST-API's (methods, status codes, headers, caching).

### HTTP-basics voor API's

**HTTP** (Hypertext Transfer Protocol) vormt de basis van moderne web-API's. In REST-architectuur wordt HTTP gebruikt zoals het bedoeld is: als een complete application-layer protocol voor resource-manipulatie.

#### HTTP Request-structuur

```http
POST /api/personen HTTP/1.1
Host: api.gemeente.nl
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
User-Agent: Gemeente-App/2.1.0
Content-Length: 156

{
  "bsn": "123456789",
  "voornaam": "Jan",
  "achternaam": "Berg",
  "geboortedatum": "1985-03-15"
}
```

#### HTTP Response-structuur

```http
HTTP/1.1 201 Created
Content-Type: application/json
Location: /api/personen/pers_abc123
ETag: "v1.2.3"
Cache-Control: private, max-age=3600
Content-Length: 234

{
  "id": "pers_abc123",
  "bsn": "123456789", 
  "voornaam": "Jan",
  "achternaam": "Berg",
  "geboortedatum": "1985-03-15",
  "created_at": "2024-03-05T14:30:00Z",
  "updated_at": "2024-03-05T14:30:00Z",
  "_links": {
    "self": "/api/personen/pers_abc123",
    "adres": "/api/personen/pers_abc123/adres"
  }
}
```

### HTTP Methods voor CRUD-operaties

#### GET - Resource opvragen

```http
# Single resource
GET /api/personen/123456789 HTTP/1.1
Host: api.gemeente.nl
Accept: application/json

# Collection met filters
GET /api/personen?achternaam=Berg&geboortejaar=1985 HTTP/1.1
Host: api.gemeente.nl
Accept: application/json

# Response
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: public, max-age=300

{
  "data": [
    {
      "id": "pers_123",
      "voornaam": "Jan",
      "achternaam": "Berg"
    }
  ],
  "pagination": {
    "total": 1,
    "page": 1,
    "per_page": 25
  }
}
```

#### POST - Resource aanmaken

```http
POST /api/personen HTTP/1.1
Host: api.gemeente.nl
Content-Type: application/json
Content-Length: 95

{
  "bsn": "987654321",
  "voornaam": "Maria",
  "achternaam": "Jansen"
}

# Success Response
HTTP/1.1 201 Created
Location: /api/personen/pers_xyz789
Content-Type: application/json

{
  "id": "pers_xyz789",
  "bsn": "987654321",
  "voornaam": "Maria", 
  "achternaam": "Jansen",
  "created_at": "2024-03-05T14:30:00Z"
}

# Error Response
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json

{
  "type": "https://api.gemeente.nl/problems/validation-error",
  "title": "Validation Failed",
  "status": 400,
  "detail": "Het verstrekte BSN is al in gebruik",
  "instance": "/api/personen",
  "invalid_params": [
    {
      "name": "bsn",
      "reason": "BSN moet uniek zijn"
    }
  ]
}
```

#### PUT - Resource volledig vervangen

```http
PUT /api/personen/pers_123 HTTP/1.1
Host: api.gemeente.nl
Content-Type: application/json
If-Match: "v1.2.3"

{
  "bsn": "123456789",
  "voornaam": "Johannes",
  "achternaam": "van den Berg",
  "geboortedatum": "1985-03-15"
}

# Success Response
HTTP/1.1 200 OK
Content-Type: application/json
ETag: "v1.2.4"

{
  "id": "pers_123",
  "bsn": "123456789",
  "voornaam": "Johannes",
  "achternaam": "van den Berg",
  "geboortedatum": "1985-03-15",
  "updated_at": "2024-03-05T14:35:00Z"
}

# Conflict Response (optimistic locking)
HTTP/1.1 412 Precondition Failed
Content-Type: application/problem+json

{
  "type": "https://api.gemeente.nl/problems/stale-data",
  "title": "Resource Modified",
  "status": 412,
  "detail": "De resource is gewijzigd door een andere gebruiker"
}
```

#### PATCH - Gedeeltelijke resource-update

```http
PATCH /api/personen/pers_123 HTTP/1.1
Host: api.gemeente.nl
Content-Type: application/json-patch+json
If-Match: "v1.2.4"

[
  {
    "op": "replace",
    "path": "/voornaam",
    "value": "Jan"
  },
  {
    "op": "add",
    "path": "/emailadres", 
    "value": "jan.vandenberg@example.com"
  }
]

# Alternative: JSON Merge Patch
PATCH /api/personen/pers_123 HTTP/1.1
Content-Type: application/merge-patch+json

{
  "voornaam": "Jan",
  "emailadres": "jan.vandenberg@example.com"
}
```

#### DELETE - Resource verwijderen

```http
DELETE /api/personen/pers_123 HTTP/1.1
Host: api.gemeente.nl
If-Match: "v1.2.4"

# Success Response (no content)
HTTP/1.1 204 No Content

# Success Response (with info)
HTTP/1.1 200 OK
Content-Type: application/json

{
  "message": "Persoon succesvol verwijderd",
  "deleted_at": "2024-03-05T14:40:00Z"
}

# Conflict Response
HTTP/1.1 409 Conflict
Content-Type: application/problem+json

{
  "type": "https://api.gemeente.nl/problems/cannot-delete",
  "title": "Cannot Delete Resource",
  "detail": "Persoon kan niet worden verwijderd vanwege actieve zaken"
}
```

### HTTP Status Codes

#### Success Codes (2xx)

```http
200 OK              # Successful GET, PUT, PATCH
201 Created         # Successful POST (resource created)
202 Accepted        # Request accepted (async processing)
204 No Content      # Successful DELETE or PUT (no response body)
206 Partial Content # Partial GET (range requests)
```

#### Client Error Codes (4xx)

```http
400 Bad Request         # Malformed request
401 Unauthorized        # Authentication required
403 Forbidden           # Authorization failed
404 Not Found           # Resource doesn't exist
405 Method Not Allowed  # HTTP method not supported
406 Not Acceptable      # Unsupported Accept header
409 Conflict           # Resource conflict (e.g., duplicate)
410 Gone               # Resource permanently deleted
412 Precondition Failed # If-Match, If-None-Match failed
413 Payload Too Large   # Request body too large
415 Unsupported Media Type # Unsupported Content-Type
422 Unprocessable Entity   # Semantic validation failed
429 Too Many Requests      # Rate limiting
```

#### Server Error Codes (5xx)

```http
500 Internal Server Error  # Generic server error
501 Not Implemented        # Feature not implemented
502 Bad Gateway           # Upstream service error
503 Service Unavailable   # Temporary service outage
504 Gateway Timeout       # Upstream service timeout
```

### HTTP Headers voor API's

#### Request Headers

**Authentication/Authorization:**
```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=
X-API-Key: abc123-def456-ghi789
```

**Content Negotiation:**
```http
Accept: application/json
Accept: application/xml, application/json;q=0.8
Accept-Language: nl-NL, en;q=0.8
Accept-Encoding: gzip, deflate
```

**Conditional Requests:**
```http
If-Match: "v1.2.3"              # Only if resource unchanged
If-None-Match: "v1.2.3"         # Only if resource changed  
If-Modified-Since: Wed, 21 Oct 2024 07:28:00 GMT
If-Unmodified-Since: Wed, 21 Oct 2024 07:28:00 GMT
```

**Custom Headers:**
```http
X-Request-ID: req_abc123        # Request tracing
X-Correlation-ID: corr_xyz789   # Cross-service correlation
X-Client-Version: 2.1.0         # Client application version
X-Organization-Code: 0363       # Municipal organization code
```

#### Response Headers  

**Content Information:**
```http
Content-Type: application/json; charset=utf-8
Content-Length: 1234
Content-Language: nl-NL
Content-Encoding: gzip
```

**Caching:**
```http
Cache-Control: public, max-age=3600
Cache-Control: private, no-cache, no-store
ETag: "v1.2.3"
Last-Modified: Wed, 21 Oct 2024 07:28:00 GMT
Expires: Thu, 22 Oct 2024 07:28:00 GMT
```

**CORS (Cross-Origin Resource Sharing):**
```http
Access-Control-Allow-Origin: https://gemeente-app.nl
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization
Access-Control-Max-Age: 86400
```

**Security:**
```http
Strict-Transport-Security: max-age=31536000; includeSubDomains
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
```

**Rate Limiting:**
```http
X-RateLimit-Limit: 1000        # Requests per time window
X-RateLimit-Remaining: 856      # Remaining requests
X-RateLimit-Reset: 1609459200   # Reset timestamp
Retry-After: 60                 # Retry after N seconds
```

### HTTP Caching voor API's

#### Cache-Control Directive

**Public resources:**
```http
# Publicly cacheable (CDN, proxy, browser)
Cache-Control: public, max-age=3600

# Example: Reference data, configuration
GET /api/landen HTTP/1.1

HTTP/1.1 200 OK
Cache-Control: public, max-age=86400
ETag: "landen-v2.1"

{
  "landen": [
    {"code": "NL", "naam": "Nederland"},
    {"code": "BE", "naam": "België"}
  ]
}
```

**Private resources:**
```http
# Only client-cacheable (sensitive data)
Cache-Control: private, max-age=300

# Example: Personal data
GET /api/personen/123456789 HTTP/1.1

HTTP/1.1 200 OK
Cache-Control: private, max-age=300
ETag: "pers-123456789-v1.5"

{
  "bsn": "123456789",
  "voornaam": "Jan",
  "achternaam": "Berg"
}
```

**No caching:**
```http
# Never cache (dynamic, sensitive data)
Cache-Control: no-cache, no-store, must-revalidate
Pragma: no-cache
Expires: 0

# Example: Authentication tokens
POST /api/auth/token HTTP/1.1

HTTP/1.1 200 OK
Cache-Control: no-cache, no-store, must-revalidate
Expires: 0

{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expires_in": 3600
}
```

#### ETag-based Caching

**Strong ETags:**
```http
# Initial request
GET /api/personen/123 HTTP/1.1

HTTP/1.1 200 OK  
ETag: "v1.2.3"
Content-Type: application/json

{"id": "123", "naam": "Jan Berg"}

# Subsequent request with If-None-Match
GET /api/personen/123 HTTP/1.1
If-None-Match: "v1.2.3"

HTTP/1.1 304 Not Modified
ETag: "v1.2.3"
Cache-Control: private, max-age=300
```

**Weak ETags:**
```http
# For semantically equivalent resources
HTTP/1.1 200 OK
ETag: W/"v1.2.3"    # Weak ETag (W/ prefix)
```

#### Conditional Updates

```http
# Safe updates with optimistic locking
PUT /api/personen/123 HTTP/1.1
If-Match: "v1.2.3"
Content-Type: application/json

{"naam": "Johannes Berg"}

# Success response
HTTP/1.1 200 OK
ETag: "v1.2.4"

# Conflict response (resource changed by another client)
HTTP/1.1 412 Precondition Failed
ETag: "v1.2.5"

{
  "error": "Resource was modified by another client",
  "current_version": "v1.2.5"
}
```

### REST API Design Patterns

#### Resource Naming

```http
# Collections (plural nouns)
GET /api/personen
POST /api/personen

# Individual resources  
GET /api/personen/123456789
PUT /api/personen/123456789
DELETE /api/personen/123456789

# Nested resources
GET /api/personen/123456789/adressen
POST /api/personen/123456789/adressen
GET /api/personen/123456789/adressen/456

# Actions (when CRUD isn't sufficient)
POST /api/personen/123456789/verify-identity
POST /api/personen/123456789/change-address
```

#### Query Parameters

```http
# Filtering
GET /api/personen?achternaam=Berg&woonplaats=Amsterdam

# Sorting  
GET /api/personen?sort=achternaam,voornaam
GET /api/personen?sort=-geboortedatum  # Descending

# Pagination
GET /api/personen?page=2&limit=25
GET /api/personen?offset=50&limit=25

# Field selection
GET /api/personen?fields=bsn,voornaam,achternaam

# Search
GET /api/personen?q=Jan+Berg
GET /api/personen?search=achternaam:Berg AND woonplaats:Amsterdam
```

#### Response Envelopes

**Simple data response:**
```json
{
  "id": "123",
  "voornaam": "Jan",
  "achternaam": "Berg"
}
```

**Collection response:**
```json
{
  "data": [
    {"id": "123", "naam": "Jan Berg"},
    {"id": "124", "naam": "Maria Jansen"}
  ],
  "meta": {
    "total": 245,
    "count": 2,
    "current_page": 1,
    "total_pages": 123
  },
  "links": {
    "self": "/api/personen?page=1",
    "first": "/api/personen?page=1",
    "next": "/api/personen?page=2",
    "last": "/api/personen?page=123"
  }
}
```

**Error response (RFC 7807 Problem Details):**
```json
{
  "type": "https://api.gemeente.nl/problems/validation-error",
  "title": "Input validation failed",
  "status": 400,
  "detail": "Het verstuurde BSN heeft een incorrect formaat",
  "instance": "/api/personen",
  "invalid-params": [
    {
      "name": "bsn",
      "reason": "BSN must be exactly 9 digits"
    }
  ],
  "trace-id": "req_abc123"
}
```

### HTTP Performance Optimization

#### Connection Management

```javascript
// HTTP/1.1 Keep-Alive
const http = require('http');
const agent = new http.Agent({
  keepAlive: true,
  keepAliveMsecs: 1000,
  maxSockets: 25
});

const options = {
  hostname: 'api.gemeente.nl',
  agent: agent
};
```

#### Compression

```http
# Request with compression support
GET /api/personen HTTP/1.1
Accept-Encoding: gzip, deflate, br

# Response with compression
HTTP/1.1 200 OK
Content-Encoding: gzip
Content-Length: 512     # Compressed size
Vary: Accept-Encoding
```

#### HTTP/2 Features

```http
# Server Push (preemptively send related resources)
Link: </api/personen/123/adres>; rel=preload; as=fetch
Link: </api/personen/123/documenten>; rel=preload; as=fetch

# Header Compression (HPACK)
# Automatically handled by HTTP/2 protocol

# Multiplexing
# Multiple requests over single connection
```

### Security Considerations

#### HTTPS Enforcement

```http
# Redirect HTTP to HTTPS
HTTP/1.1 301 Moved Permanently
Location: https://api.gemeente.nl/api/personen
Strict-Transport-Security: max-age=31536000
```

#### Input Validation

```http
# Request
POST /api/personen HTTP/1.1
Content-Type: application/json

{
  "bsn": "123456789',DROP TABLE personen;--",
  "voornaam": "<script>alert('xss')</script>"
}

# Response (proper validation)
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json

{
  "type": "https://api.gemeente.nl/problems/validation-error",
  "title": "Input validation failed",
  "invalid-params": [
    {
      "name": "bsn",
      "reason": "BSN may only contain digits"
    },
    {
      "name": "voornaam", 
      "reason": "HTML tags are not allowed"
    }
  ]
}
```

#### Rate Limiting Implementation

```javascript
// Express.js rate limiting
const rateLimit = require('express-rate-limit');

const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 1000, // Limit each IP to 1000 requests per windowMs
  message: 'Te veel requests van dit IP-adres',
  standardHeaders: true,
  legacyHeaders: false,
  handler: (req, res) => {
    res.status(429).json({
      error: 'Rate limit exceeded',
      retry_after: Math.ceil(req.rateLimit.resetTime / 1000)
    });
  }
});

app.use('/api/', apiLimiter);
```

### Debugging HTTP API's

#### curl Examples

```bash
# Basic GET request with headers
curl -H "Accept: application/json" \
     -H "Authorization: Bearer $TOKEN" \
     https://api.gemeente.nl/api/personen/123456789

# POST with JSON data
curl -X POST \
     -H "Content-Type: application/json" \
     -d '{"bsn":"123456789","voornaam":"Jan"}' \
     https://api.gemeente.nl/api/personen

# PUT with optimistic locking
curl -X PUT \
     -H "Content-Type: application/json" \
     -H "If-Match: v1.2.3" \
     -d '{"voornaam":"Johannes"}' \
     https://api.gemeente.nl/api/personen/123

# Verbose output for debugging
curl -v -X GET \
     -H "Accept: application/json" \
     https://api.gemeente.nl/api/personen/123456789

# Test caching behavior
curl -H "If-None-Match: v1.2.3" \
     https://api.gemeente.nl/api/personen/123456789
```

#### HTTP Client Tools

- **Postman**: GUI-based API testing met collections
- **Insomnia**: Alternative REST client  
- **HTTPie**: User-friendly command-line HTTP client
- **Thunder Client**: VS Code extension voor API testing

Het HTTP-protocol vormt de fundament van moderne REST-API's. Door HTTP-features zoals methoden, status codes, headers en caching correct te gebruiken, kunnen overheidsorganisaties robuuste, performante en veilige API's bouwen die voldoen aan moderne standaarden en verwachtingen.

**Resources:**
- [HTTP/1.1 Specification (RFC 7231)](https://tools.ietf.org/html/rfc7231)
- [HTTP/2 Specification (RFC 7540)](https://tools.ietf.org/html/rfc7540)
- [REST API Guidelines Nederland](https://docs.geostandaarden.nl/api/API-Strategie/)
- [Problem Details for HTTP APIs (RFC 7807)](https://tools.ietf.org/html/rfc7807)