# Usage Guidelines

Based on [.NET Framework Design Guidelines](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/usage-guidelines).

This document provides a summary of the usage guidelines for common types in .NET, with a focus on helping AI coding agents adhere to best practices and identify refactoring opportunities.

## General Principles

These guidelines cover the direct use of built-in Framework types and operator overloading in publicly accessible APIs. The goal is to ensure API consistency and ease of use.

## Specific Type Usage

### `System.DateTime` and `System.DateTimeOffset`

*   **`System.DateTime`**: Represents a date and time. It can be local time, UTC, or unspecified.
*   **`System.DateTimeOffset`**: Represents a date and time, together with an offset from UTC. Use `DateTimeOffset` for storing date and time information that is independent of the local time zone, especially when dealing with distributed systems or persistent data.
*   **AI Refactoring Hint**: If an AI agent encounters `DateTime` being used for persistent storage or in scenarios where time zone ambiguity could lead to issues, it should suggest refactoring to `DateTimeOffset`.

### `System.ICloneable`

*   **Avoid implementing `ICloneable`**. The `ICloneable` interface has ambiguities regarding deep vs. shallow copies, which can lead to confusion and incorrect usage.
*   If cloning is necessary, provide a type-specific cloning method (e.g., `Clone()`, `Copy()`) that clearly defines the cloning behavior (deep or shallow).
*   **AI Refactoring Hint**: Flag implementations of `ICloneable`. Suggest replacing them with clearly named `Clone()` or `Copy()` methods.

### `System.IComparable<T>` and `System.IEquatable<T>`

*   **`System.IComparable<T>`**: Implement `IComparable<T>` for types that have a natural ordering (e.g., numeric types, strings, dates). This allows instances of the type to be sorted.
*   **`System.IEquatable<T>`**: Implement `IEquatable<T>` for types that need to define value equality. This is more efficient than overriding `Equals(object)` for value types and helps avoid boxing.
*   **AI Refactoring Hint**: If an AI agent observes custom types being compared using `==` or `Equals()` without `IEquatable<T>` implemented, or if they are frequently sorted without `IComparable<T>`, it should suggest implementing these interfaces.

### `System.Nullable<T>`

*   **Use `Nullable<T>`** (or the `?` syntax in C#) for value types that can be assigned `null`. This is useful for representing missing or undefined values for value types.
*   **AI Refactoring Hint**: If an AI agent sees `null` checks on value types that are not `Nullable<T>`, it should suggest using `Nullable<T>`.

### `System.Uri`

*   **Use `Uri`** for representing Uniform Resource Identifiers. The `Uri` class provides robust parsing, validation, and manipulation of URIs, handling various schemes and components correctly.
*   **AI Refactoring Hint**: If an AI agent encounters string manipulation for URLs or URIs, it should suggest using the `System.Uri` class for safer and more reliable handling.

### `IDisposable` (Dispose Pattern)

*   **Implement the Dispose Pattern** for types that own unmanaged resources (e.g., file handles, network connections) or managed resources that need explicit cleanup (e.g., database connections, streams).
*   Ensure that the `Dispose()` method is idempotent (can be called multiple times without error) and that it releases all held resources.
*   Use `using` statements to ensure that `IDisposable` objects are correctly disposed of, even if exceptions occur.
*   **AI Refactoring Hint**: If an AI agent identifies classes that manage external resources but do not implement `IDisposable`, or if `IDisposable` objects are not being used within `using` statements, it should flag these as potential resource leaks.

### Arrays

*   **Prefer `System.Collections.Generic.List<T>` or other generic collections over arrays** for most scenarios, especially when the size of the collection is not fixed or when you need more functionality (e.g., adding/removing elements).
*   Use arrays when you need a fixed-size, contiguous block of memory, and performance is critical.
*   **AI Refactoring Hint**: If an AI agent sees arrays being used where dynamic sizing or more advanced collection features are needed, it should suggest refactoring to `List<T>` or other appropriate generic collections.

### Attributes

*   **Design custom attributes carefully.** Attributes are metadata and should not contain business logic. They are primarily for declarative programming.
*   **AI Refactoring Hint**: If an AI agent finds attributes containing complex logic or state, it should suggest refactoring that logic out of the attribute.

### Collections

*   **Prefer generic collections** (e.g., `List<T>`, `Dictionary<TKey, TValue>`) over non-generic collections (e.g., `ArrayList`, `Hashtable`) to ensure type safety and better performance.
*   **Return interfaces for collections** (e.g., `IEnumerable<T>`, `ICollection<T>`, `IList<T>`) rather than concrete types (e.g., `List<T>`) from public methods. This provides flexibility and prevents consumers from casting to specific implementations.
*   **AI Refactoring Hint**: Flag usage of non-generic collections. Suggest returning collection interfaces instead of concrete types from public APIs.

### Serialization

*   **Consider the implications of serialization** when designing types that will be serialized. Ensure that all necessary data is included and that sensitive data is handled securely.
*   **AI Refactoring Hint**: If an AI agent identifies types that are being serialized, it should check for proper serialization attributes and handling of sensitive data.

### Equality Operators (`==` and `!=`)

*   **Do not overload operators unless the semantics are obvious and intuitive.** Overloading `==` and `!=` is generally recommended for value types that have value equality semantics.
*   **Overload symmetric operators in pairs** (e.g., if you overload `==`, you must also overload `!=`).
*   **Do not throw exceptions from operator overloads.** Operators should be reliable and predictable.
*   **AI Refactoring Hint**: If an AI agent finds custom value types that are compared for equality but do not overload `==` and `!=`, it should suggest implementing them. Conversely, if reference types overload these operators, it should flag them for review, as reference equality is usually the default and expected behavior.