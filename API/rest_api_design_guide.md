# REST API Designing -- Complete Guide (Interview + Practical Notes)

## 1. What is REST API

REST (Representational State Transfer) is an architectural style for
designing networked applications using HTTP.

Principles: - Stateless - Client--Server architecture - Resource-based
URLs - Uses HTTP methods - Representation using JSON/XML

------------------------------------------------------------------------

## 2. REST API Design Principles

### 1. Use Nouns, Not Verbs

Correct: GET /users GET /users/101

Incorrect: GET /getUsers POST /createUser

------------------------------------------------------------------------

### 2. Use Proper HTTP Methods

GET → Fetch data\
POST → Create\
PUT → Update entire resource\
PATCH → Partial update\
DELETE → Delete

------------------------------------------------------------------------

### 3. Use Proper Status Codes

200 OK → Success\
201 Created → Resource created\
204 No Content → Success without body\
400 Bad Request → Client error\
401 Unauthorized\
403 Forbidden\
404 Not Found\
500 Internal Server Error

------------------------------------------------------------------------

## 3. Ways to Design REST APIs (Approaches)

### Approach 1: Resource-Based Design (Recommended)

Example:

GET /orders\
GET /orders/{id}\
POST /orders\
DELETE /orders/{id}

This is the most widely used approach.

------------------------------------------------------------------------

### Approach 2: Hierarchical Resources

Example:

GET /users/101/orders\
GET /users/101/orders/500

Used when resources have parent-child relationships.

------------------------------------------------------------------------

### Approach 3: Query-Based Filtering

Example:

GET /orders?status=SHIPPED\
GET /users?age=30

Useful for searching and filtering.

------------------------------------------------------------------------

### Approach 4: Pagination

Example:

GET /orders?page=1&size=10

Best practice: - Default page size - Limit max size

------------------------------------------------------------------------

### Approach 5: Sorting

Example:

GET /orders?sort=price,desc

------------------------------------------------------------------------

### Approach 6: Versioning

Ways to version APIs:

1.  URI Versioning /v1/orders

2.  Header Versioning Accept: application/vnd.company.v1+json

3.  Query Param Versioning /orders?version=1

Most common: URI versioning

------------------------------------------------------------------------

## 4. Idempotency

Idempotent operations produce the same result when repeated.

Idempotent: GET, PUT, DELETE

Non-idempotent: POST

------------------------------------------------------------------------

## 5. Example REST API Design (Order Service)

Endpoints:

GET /orders\
GET /orders/{id}\
POST /orders\
PUT /orders/{id}\
DELETE /orders/{id}

------------------------------------------------------------------------

## 6. Spring Boot Code Example

### Controller Example

``` java
@RestController
@RequestMapping("/orders")
public class OrderController {

    @GetMapping
    public List<String> getOrders() {
        return List.of("Order1", "Order2");
    }

    @GetMapping("/{id}")
    public String getOrderById(@PathVariable int id) {
        return "Order " + id;
    }

    @PostMapping
    public String createOrder() {
        return "Order created";
    }

    @PutMapping("/{id}")
    public String updateOrder(@PathVariable int id) {
        return "Order updated " + id;
    }

    @DeleteMapping("/{id}")
    public String deleteOrder(@PathVariable int id) {
        return "Order deleted " + id;
    }
}
```

------------------------------------------------------------------------

## 7. Request and Response Best Practices

### Request

-   Validate inputs
-   Use DTOs
-   Use meaningful field names

### Response

Use consistent structure:

Example: { "timestamp": "2026-02-13", "status": 200, "message":
"Success", "data": {} }

------------------------------------------------------------------------

## 8. Error Handling Best Practice

Example response:

{ "timestamp": "2026-02-13", "status": 400, "error": "Invalid request",
"path": "/orders" }

------------------------------------------------------------------------

## 9. Security Best Practices

-   Use HTTPS
-   Use JWT/OAuth2
-   Validate inputs
-   Rate limiting
-   Logging and monitoring

------------------------------------------------------------------------

## 10. Interview Summary Answer

If interviewer asks:

How do you design a REST API?

Answer:

I design REST APIs using resource-based URLs, proper HTTP methods,
meaningful status codes, and versioning. I also support pagination,
filtering, and sorting. APIs are stateless, secured with HTTPS and
authentication mechanisms like JWT. Error handling and response
structures are standardized for consistency.

------------------------------------------------------------------------

## 11. Quick Checklist (Before Releasing API)

-   Naming conventions followed
-   Proper HTTP methods used
-   Status codes correct
-   Pagination supported
-   Versioning implemented
-   Error responses standardized
-   Security enabled
