# Type Design Guidelines

Based on [.NET Framework Design Guidelines](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/type-design-guidelines).

This document provides a summary of the type design guidelines for .NET, with a focus on helping AI coding agents adhere to best practices and identify refactoring opportunities.

## General Principles

*   **API Consistency and Ease of Use**: The primary goal of type design is to create consistent and easy-to-use APIs.
*   **Well-Defined Types**: Each type should represent a well-defined set of related members, not just a random collection of functionality.
*   **Namespace Grouping**: Always define types within appropriate namespaces to group related types logically.

## Choosing the Right Type

### Classes

*   **Description**: The most common type in .NET. Classes are **reference types**, meaning variables of class types store references to objects, not the objects themselves.
*   **When to Use**:
    *   When you need object-oriented features like inheritance, polymorphism, and encapsulation.
    *   When the type represents a complex entity with behavior and state.
    *   When the type needs to be passed by reference.
    *   When the type is large or frequently modified.
*   **AI Refactoring Hint**: If an AI agent encounters a type that is large, mutable, or requires inheritance/polymorphism, but is implemented as a struct, it should flag it for potential refactoring to a class.

### Structs

*   **Description**: Value types that directly contain their data. Variables of struct types store the actual data, not references.
*   **When to Use**:
    *   For small, simple, and immutable types that behave like primitive types (e.g., `Point`, `Color`).
    *   When the type's primary purpose is to hold data, and its behavior is minimal.
    *   When the type is frequently copied, as copying structs is generally more efficient than copying references to large objects.
    *   **Do not use structs if the type needs to be polymorphic or if you need object identity.**
*   **AI Refactoring Hint**: If an AI agent encounters a small, immutable type that is frequently copied and does not require object identity or polymorphism, but is implemented as a class, it should flag it for potential refactoring to a struct.

### Interfaces

*   **Description**: Define contracts that can be implemented by both classes and structs. Interfaces specify a set of members that a type must implement.
*   **When to Use**:
    *   To define a contract for a set of related behaviors that different types can implement.
    *   To achieve polymorphism without using inheritance (e.g., `IEnumerable`, `IDisposable`).
    *   To simulate multiple inheritance (as C# does not support multiple inheritance for classes).
*   **AI Refactoring Hint**: If an AI agent identifies a set of classes sharing common behavior that could be abstracted, it should suggest creating an interface.

### Enums

*   **Description**: A special kind of value type used to define a small, fixed set of named constant values.
*   **When to Use**:
    *   To represent a set of mutually exclusive values (e.g., days of the week, status codes).
    *   To improve code readability and maintainability by replacing magic numbers with meaningful names.
*   **AI Refactoring Hint**: If an AI agent finds code using integer constants to represent a fixed set of values, it should suggest refactoring to an enum.

### Static Classes

*   **Description**: Classes that cannot be instantiated and contain only static members (static methods, properties, fields, and events).
*   **When to Use**:
    *   As containers for utility methods that do not operate on instance data (e.g., `System.Math`, extension methods).
    *   When you need to provide global functionality or state that is shared across the application.
*   **AI Refactoring Hint**: If an AI agent encounters a class that is never instantiated and only contains static members, it should suggest making it a static class.

### Special Types

*   **Delegates, Exceptions, Attributes, Arrays, and Collections**: These are specialized reference types designed for specific purposes. Use them according to their specific design purpose and established patterns.