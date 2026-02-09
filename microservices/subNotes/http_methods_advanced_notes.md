
# Advanced HTTP Methods – Interview Notes

## 1. HTTP Methods Overview

HTTP methods define the action to be performed on a resource in REST APIs.

| Method | Purpose                                   | Idempotent | Safe | Cacheable |
|--------|-------------------------------------------|--------|--------|--------|
| GET | Retrieve data                             | Yes | Yes | Yes |
| POST | Create resource                           | No | No | Rare |
| PUT | Full update                               | Yes | No | No |
| PATCH | Partial update                            | Usually No | No | No |
| DELETE | Delete resource                           | Yes | No | No |
| HEAD | Same as GET but headers only | Yes | Yes | Yes |
| OPTIONS | Supported methods / CORS                       | Yes | Yes | No |

---

# 2. Safe vs Idempotent (Important Interview Topic)

Safe:
- Does NOT change server state
- Example: GET, HEAD, OPTIONS

Idempotent:
- Multiple identical requests produce same result
- Example: GET, PUT, DELETE

Example:
DELETE /users/1
Calling multiple times → user remains deleted.

---

# 3. Real‑World Scenarios

## Payment APIs
Problem:
Network retry may create duplicate payment.

Solution:
Use Idempotency-Key header:
Idempotency-Key: 123abc

Server checks if request already processed.

---

## Retry Mechanism
Safe to retry automatically:
- GET
- PUT
- DELETE

Not safe:
- POST (unless idempotency key used)

---

# 4. PUT vs POST (Very Common Question)

POST:
- Create resource
- Server decides ID
Example:
POST /orders

PUT:
- Update or create at known URI
Example:
PUT /users/100

Rule:
If client knows ID → PUT
If server generates ID → POST

---

# 5. PATCH vs PUT

PUT:
- Full replacement

PATCH:
- Partial update

Example:
PATCH /users/1
{
  "email": "new@email.com"
}

---

# 6. Caching Behavior

GET:
- Cached by browser and proxies

POST:
- Not cached by default

Headers affecting cache:
Cache-Control
ETag

---

# 7. REST API Design Example

GET    /users
GET    /users/{id}
POST   /users
PUT    /users/{id}
PATCH  /users/{id}
DELETE /users/{id}

---

# Interview Questions with Answers (Collapsible)

<details>
<summary>1. What is the difference between Safe and Idempotent methods?</summary>

Safe methods do not modify server state.
Idempotent methods may modify state but repeated calls result in same state.

Example:
GET → Safe and Idempotent
DELETE → Idempotent but not Safe

</details>

<details>
<summary>2. Why is POST not idempotent?</summary>

Each POST request may create a new resource.
Repeated calls may create duplicates.

</details>

<details>
<summary>3. When should PUT be used instead of POST?</summary>

Use PUT when:
- Resource ID is known
- Full update is required
- Operation should be idempotent

</details>

<details>
<summary>4. When should PATCH be preferred over PUT?</summary>

When updating only a few fields instead of replacing the entire resource.

</details>

<details>
<summary>5. Which HTTP methods are cacheable?</summary>

Primarily GET and HEAD.
POST can be cached but rarely implemented.

</details>

<details>
<summary>6. Why should GET never modify data?</summary>

Because:
- Browsers cache GET
- Crawlers may trigger GET
- Proxies may retry automatically

</details>

<details>
<summary>7. What is the purpose of the OPTIONS method?</summary>

It returns supported HTTP methods and is used in CORS preflight requests.

</details>

<details>
<summary>8. What is the HEAD method used for?</summary>

Used to retrieve headers without body.
Commonly used to:
- Check file size
- Validate resource existence

</details>

<details>
<summary>9. How do you prevent duplicate order creation in POST?</summary>

Use:
- Idempotency keys
- Unique transaction IDs

</details>

<details>
<summary>10. Which HTTP methods are safe to retry automatically?</summary>

GET, PUT, DELETE, HEAD, OPTIONS

POST requires idempotency handling.

</details>

---

# Quick Revision (Interview Ready)

Safe Methods:
GET, HEAD, OPTIONS

Idempotent Methods:
GET, PUT, DELETE, HEAD, OPTIONS

Most asked differences:
GET vs POST
PUT vs PATCH
Safe vs Idempotent
POST retry problem
Caching behavior

