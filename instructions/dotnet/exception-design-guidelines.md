# Exception Design Guidelines

Based on [.NET Framework Design Guidelines](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/exception-design).

This document provides a summary of the exception design guidelines for .NET, with a focus on helping AI coding agents adhere to best practices and identify refactoring opportunities.

## General Principles

*   **Do not throw `System.Exception` or `System.SystemException`**.
    *   **AI Refactoring Hint**: Flag any `throw new Exception()` or `throw new SystemException()` statements. Suggest using more specific exception types.
*   **Avoid catching `System.Exception` or `System.SystemException`** in framework code, unless re-throwing.
    *   **AI Refactoring Hint**: Flag `catch (Exception ex)` or `catch (SystemException ex)` blocks that do not re-throw the exception. Suggest catching more specific exception types.
*   **Do not throw or derive from `ApplicationException`**.
    *   **AI Refactoring Hint**: Flag any `throw new ApplicationException()` or classes deriving from `ApplicationException`. Suggest using `Exception` as the base class for custom exceptions.

## Specific Exception Types

*   **`InvalidOperationException`**: Throw when an object is in an improper state for the method call.
    *   **AI Refactoring Hint**: If a method is called on an object that is not in a valid state for that operation, and the error is not due to invalid arguments, suggest throwing `InvalidOperationException`.
*   **`ArgumentException` (and subclasses)**: Throw when a method receives invalid arguments (e.g., `ArgumentNullException`, `ArgumentOutOfRangeException`). Set the `ParamName` property when throwing subclasses.
    *   **AI Refactoring Hint**: If a method receives a `null` argument where it's not allowed, suggest throwing `ArgumentNullException`. If an argument is out of its valid range, suggest `ArgumentOutOfRangeException`. Ensure `ParamName` is set.

## Exceptions to Avoid Throwing (Publicly Callable APIs)

*   `NullReferenceException`
*   `AccessViolationException`
*   `IndexOutOfRangeException`
*   `StackOverflowException`

These exceptions are typically indicative of a bug in the code and are thrown by the execution engine. Publicly callable APIs should not explicitly or implicitly throw them.
    *   **AI Refactoring Hint**: Flag any explicit `throw new NullReferenceException()`, `throw new IndexOutOfRangeException()`, or `throw new AccessViolationException()` statements. Suggest fixing the underlying bug that would cause these exceptions.
    *   **AI Refactoring Hint**: Flag any explicit `throw new StackOverflowException()`. Suggest refactoring recursive code to avoid stack overflow.

## Best Practices

*   **Use Predefined Exceptions**: Only create new exception classes when existing ones are not applicable and a more specific error type is needed for programmatic handling.
    *   **AI Refactoring Hint**: If a new custom exception is being created, check if an existing .NET exception type could adequately represent the error condition.
*   **Avoid Exceptions for Normal Control Flow**: For common error cases, consider returning `null`, a default value, or using `Try*` methods (e.g., `TryParse`, `TryGetValue`) instead of throwing an exception. Exceptions are for exceptional, unexpected error conditions.
    *   **AI Refactoring Hint**: If an exception is being used to signal a common or expected failure condition, suggest refactoring to a `Try*` pattern or returning a status indicator.
*   **Custom Exceptions**: Create for specific business domain errors, to wrap system-level exceptions (to make the code more technology-agnostic), or to add more context to the error (e.g., a record ID).
    *   **AI Refactoring Hint**: When a custom exception is defined, ensure it provides meaningful context and is used for specific, programmatically handleable error scenarios.
*   **Performance**: Exceptions are resource-intensive. To minimize overhead:
    *   Use `Try*` methods (e.g., `TryParse`) when available.
    *   Validate inputs before performing operations to prevent exceptions.
    *   Design APIs to help users avoid common exceptions.
    *   **AI Refactoring Hint**: Identify code paths where exceptions are frequently thrown and caught. Suggest pre-validation or alternative API designs to reduce exception overhead.
*   **Resource Management**: Use `try/catch/finally` blocks or `using` statements to ensure proper resource cleanup, even when exceptions occur.
    *   **AI Refactoring Hint**: Check for unmanaged resources or `IDisposable` objects that are not properly disposed of in the presence of exceptions. Suggest using `using` statements or `finally` blocks.