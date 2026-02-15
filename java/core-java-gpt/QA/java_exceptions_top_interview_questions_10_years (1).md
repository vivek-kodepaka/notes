# Java Exceptions – Top Interview Questions WITH Answers (10+ Years Experience)

---

## 1. Difference between Error and Exception?
**Error** represents serious JVM problems (OOME, StackOverflow) and is not meant to be handled. **Exception** represents application-level issues that can be handled or recovered from.

---

## 2. Checked vs Unchecked exceptions – when to use each?
Checked exceptions represent recoverable conditions (IO, DB, network) and must be handled or declared. Unchecked exceptions represent programming bugs (NPE, IllegalArgumentException).

---

## 3. Why RuntimeException exists?
To represent programming errors and avoid cluttering method signatures. They promote fail-fast behavior.

---

## 4. Why Java introduced checked exceptions?
To force developers to handle recoverable failures explicitly and make API contracts clear.

---

## 5. Can we catch Error?
Yes technically, but it’s strongly discouraged because Errors indicate JVM-level failures.

---

## 6. What happens if an exception is not handled?
The JVM terminates the thread and prints the stack trace. For main thread, the application exits.

---

## 7. finally vs finalize vs try-with-resources?
- **finally**: cleanup block
- **finalize()**: GC hook (deprecated)
- **try-with-resources**: automatic resource management

---

## 8. Can finally block throw exception?
Yes, but it overrides exceptions thrown from try/catch, which is dangerous.

---

## 9. What if both catch and finally throw exceptions?
The exception from `finally` wins; the original is lost.

---

## 10. How suppressed exceptions work?
In try-with-resources, exceptions thrown during resource close are suppressed and attached to the main exception.

---

## 11. Why exception handling is expensive?
Because stack traces are captured at creation time, which involves walking the call stack.

---

## 12. Is exception handling thread-safe?
Yes. Exceptions are immutable once created and thread-confined.

---

## 13. What is exception chaining?
Wrapping one exception inside another to preserve root cause using `Throwable cause`.

---

## 14. Best practices for custom exceptions?
Use meaningful names, preserve cause, avoid overly generic exceptions.

---

## 15. Should custom exceptions be checked or unchecked?
Domain/business exceptions are usually unchecked; recoverable technical exceptions can be checked.

---

## 16. Can constructor throw exception?
Yes. If it throws checked exception, callers must handle it.

---

## 17. Can overridden method throw broader exception?
No. It can throw same or narrower checked exceptions.

---

## 18. Can static block throw exception?
Yes. It causes `ExceptionInInitializerError`.

---

## 19. Can main method throw exception?
Yes. JVM handles it and prints stack trace.

---

## 20. What happens if finally has return?
It suppresses return from try/catch – highly discouraged.

---

## 21. How does try-with-resources work internally?
The compiler generates a finally block that closes resources in reverse order.

---

## 22. AutoCloseable vs Closeable?
`Closeable` throws IOException; `AutoCloseable` can throw any exception.

---

## 23. Order of catch blocks?
From most specific to most generic. Otherwise compile-time error.

---

## 24. Multiple catch vs separate catch blocks?
Multi-catch reduces duplication when handling is identical.

---

## 25. What is precise rethrow?
Java 7 feature allowing rethrowing exact exception types.

---

## 26. How exceptions work in streams?
Checked exceptions aren’t allowed; must be wrapped or handled externally.

---

## 27. How to handle checked exceptions in lambda?
Wrap into RuntimeException or use helper methods.

---

## 28. How exceptions propagate in threads?
They don’t propagate to parent thread; use `Future.get()` or handlers.

---

## 29. What is UncaughtExceptionHandler?
Handles uncaught exceptions in threads.

---

## 30. Difference between throw and throws?
`throw` actually throws exception; `throws` declares possibility.

---

## 31. Why not use exceptions for flow control?
Because they’re expensive and reduce readability.

---

## 32. Can we serialize exceptions?
Yes. Throwable implements Serializable.

---

## 33. How stack trace is generated?
Captured at exception creation by walking call stack.

---

## 34. What is fillInStackTrace()?
Method that populates stack trace; costly operation.

---

## 35. When to log exception?
At the boundary layer where it’s handled meaningfully.

---

## 36. Exception handling in Spring Boot?
Using `@ControllerAdvice` and `@ExceptionHandler`.

---

## 37. How @ControllerAdvice works?
Intercepts exceptions globally across controllers.

---

## 38. Mapping exceptions to HTTP status?
Use `@ResponseStatus` or `ResponseEntityExceptionHandler`.

---

## 39. Global vs local exception handling?
Global for consistency, local for special cases.

---

## 40. Exception handling in microservices?
Translate to error codes, avoid leaking stack traces.

---

## 41. Can we retry on exception?
Yes, for transient failures using retry mechanisms.

---

## 42. Circuit breaker + exceptions?
Stops cascading failures by short-circuiting calls.

---

## 43. What are transient exceptions?
Temporary failures like network timeouts.

---

## 44. How to design exception hierarchy?
Base exception → technical vs business exceptions.

---

## 45. Domain vs technical exceptions?
Domain exceptions represent business rules; technical represent system failures.

---

## 46. What happens if exception in static initializer?
Class loading fails with `ExceptionInInitializerError`.

---

## 47. How JVM treats OutOfMemoryError?
May continue or crash depending on context; usually unrecoverable.

---

## 48. NoClassDefFoundError vs ClassNotFoundException?
Former at runtime, latter at class loading.

---

## 49. How exceptions affect GC?
Exceptions increase temporary objects and GC pressure.

---

## 50. Common exception-related production bugs?
Swallowed exceptions, wrong exception types, over-logging, missing root cause.

---

✅ END OF INTERVIEW QUESTIONS WITH