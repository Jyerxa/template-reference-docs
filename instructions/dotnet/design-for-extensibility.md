# Design for Extensibility

Based on [.NET Framework Design Guidelines](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/designing-for-extensibility).

This document provides a summary of the design for extensibility guidelines for .NET, with a focus on helping AI coding agents adhere to best practices and identify refactoring opportunities.

## General Principles

*   **Cost-Benefit Analysis**: Extensibility adds complexity and cost. It should only be introduced when there is a clear need and the benefits outweigh the costs.
*   **Least Costly Mechanism**: For any given extensibility requirement, choose the least costly mechanism that meets the requirements. It's easier to add extensibility later than to remove it without breaking changes.

## Extensibility Mechanisms

### Unsealed Classes with Protected Members

*   **Description**: This is the most common and straightforward extensibility mechanism. By leaving a class unsealed and exposing protected members, you allow inheritors to extend or modify its behavior.
*   **When to Use**: When you anticipate that users will need to customize or add functionality to your class through inheritance.
*   **AI Refactoring Hint**: If an AI agent identifies a class that is frequently being subclassed or extended through utility methods, but is sealed or lacks protected members, it should suggest making it unsealed and exposing protected members for extensibility.

### Events and Callbacks

*   **Description**: Provide a loosely coupled way to add behavior to a system without requiring direct modification of the core code. Events allow a class to notify other classes when something interesting happens.
*   **When to Use**: When you want to allow consumers of your API to respond to specific actions or state changes without tightly coupling them to your implementation.
*   **AI Refactoring Hint**: If an AI agent observes a pattern where consumers are frequently polling for state changes or using tightly coupled mechanisms to react to internal events, it should suggest introducing events or callbacks.

### Virtual Members

*   **Description**: Allow subclasses to override and modify the behavior of base class methods. This provides a hook for customization within the inheritance hierarchy.
*   **When to Use**: When you want to provide a default implementation but allow derived classes to specialize or extend that behavior.
*   **AI Refactoring Hint**: If an AI agent sees a base class method that is often reimplemented in derived classes with similar but slightly modified logic, it should suggest making the base method virtual.

### Abstractions (Abstract Types and Interfaces)

*   **Description**: Offer the highest degree of flexibility by defining contracts that can be implemented by various types. Interfaces define a contract without providing any implementation, while abstract classes can provide partial implementation.
*   **When to Use**:
    *   **Interfaces**: When you want to define a contract that can be implemented by any class or struct, regardless of its position in the inheritance hierarchy. Ideal for defining capabilities (e.g., `IDisposable`, `IEnumerable`).
    *   **Abstract Classes**: When you want to provide a common base for a family of types, sharing some implementation while leaving other parts to be implemented by derived classes.
*   **AI Refactoring Hint**: If an AI agent identifies a set of classes that share a common set of methods but do not share a common base class, it should suggest creating an interface or abstract base class.

### Base Classes for Implementing Abstractions

*   **Description**: While interfaces provide maximum flexibility, they can be difficult to implement if they define many members. Providing abstract base classes that implement interfaces can simplify the implementation for consumers.
*   **When to Use**: When you define a complex interface and want to provide a convenient starting point for implementers.
*   **AI Refactoring Hint**: If an AI agent observes that multiple implementations of a complex interface share significant common code, it should suggest providing an abstract base class that implements the common parts.

## Preventing Extensibility (Sealing)

*   **Description**: Explicitly prevents further inheritance (sealing a class) or overriding (sealing a member).
*   **When to Use**:
    *   **Security**: To prevent malicious code from overriding behavior.
    *   **Performance**: To allow for optimizations that are not possible with virtual methods.
    *   **Stability**: When the design of a class or member is complete and stable, and you want to prevent unintended changes by derived classes.
    *   **Simplicity**: When extensibility is not desired or would add unnecessary complexity.
*   **AI Refactoring Hint**: If an AI agent identifies a class or member that is not intended for extension, or where extension could lead to security vulnerabilities or performance issues, it should suggest sealing it. Conversely, if a sealed class is frequently being extended through composition or utility methods, it might indicate a need to reconsider its sealed status if extensibility is desired.