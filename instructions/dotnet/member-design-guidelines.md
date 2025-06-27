# Member Design Guidelines

Based on [.NET Framework Design Guidelines](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/member-design-guidelines).

This document provides a summary of the member design guidelines for .NET, with a focus on helping AI coding agents adhere to best practices and identify refactoring opportunities.

## General Principles

*   **API Consistency and Ease of Use**: The primary goal is to promote consistent and easy-to-use APIs.
*   **Clarity**: Member names should clearly indicate their purpose and behavior.

## Member Overloading

*   **Do not have overloads with parameters at the same position and similar types yet with different semantics.** This can lead to confusion and unexpected behavior.
    *   **AI Refactoring Hint**: Flag methods with identical parameter counts and similar types but different logical meanings for those parameters. Suggest renaming or reordering parameters to differentiate them.
*   **Do provide a logical series of overloads from simplest to most complex.** This allows users to choose the most convenient overload for their needs.
    *   **AI Refactoring Hint**: Identify method sets that could benefit from a simpler overload (e.g., a method with many optional parameters could have a simpler overload with fewer parameters).

## Properties vs. Methods

*   **Use properties for members that logically represent an attribute of the object.** Properties should be lightweight and return data.
*   **Use methods for members that perform an action.** Methods typically involve computation, side effects, or return a new object.
*   **Properties should not be computationally complex or have observable side effects.** Getting a property value should be a fast operation and should not change the state of the object in a way that is visible to the caller.
    *   **AI Refactoring Hint**: Flag properties that perform complex calculations, network calls, or modify object state. Suggest refactoring them into methods.

## Constructors

*   **Do minimal work in the constructor.** Constructors should primarily initialize the object's state. Avoid complex logic, long-running operations, or calling virtual members.
    *   **AI Refactoring Hint**: Flag constructors that contain significant business logic, I/O operations, or calls to virtual methods. Suggest moving such logic to a separate initialization method.
*   **Provide simple constructors, ideally with no parameters.** This makes it easy to create instances of the class.
*   **Consider using a static factory method instead of a constructor** if the semantics of the desired operation do not map directly to the construction of a new instance (e.g., when returning an existing instance or a subclass).
    *   **AI Refactoring Hint**: If a constructor has complex logic for determining which type to instantiate or if it might return an existing instance, suggest a static factory method.
*   **Do not throw exceptions from static constructors.** If a static constructor fails, the type will not be usable, and subsequent attempts to access static members will result in `TypeInitializationException`.
    *   **AI Refactoring Hint**: Flag static constructors that throw exceptions. Suggest handling errors internally or throwing exceptions from instance methods.

## Events

*   **Use the `EventHandler` delegate type for events.** For events with custom data, use `EventHandler<TEventArgs>`.
*   **Use a protected virtual method to raise each event.** This allows subclasses to handle the event before it is exposed to external subscribers.
*   **Name events with a verb or a verb phrase.** (e.g., `Click`, `Closing`).
*   **Name event handlers with the "EventHandler" suffix.** (e.g., `Button_ClickEventHandler`).
*   **Name event argument classes with the "EventArgs" suffix.** (e.g., `CustomEventArgs`).
    *   **AI Refactoring Hint**: Check for consistent event naming and usage of `EventHandler` and `EventArgs` patterns.

## Fields

*   **Do not use public or protected instance fields.** Expose data through properties instead, which provide better encapsulation and allow for future changes without breaking compatibility.
    *   **AI Refactoring Hint**: Flag public or protected instance fields and suggest converting them to properties.
*   **Use private or internal fields and provide access through properties.**
*   **For public static read-only fields (constants), use SCREAMING_SNAKE_CASE.** These should represent values that will never change.
    *   **AI Refactoring Hint**: Flag public static mutable fields. Suggest making them `readonly` or converting them to properties if they need to be mutable.

## Operator Overloading

*   **Avoid defining operator overloads, except in types that should feel like primitive (built-in) types.** Overloading operators can lead to confusion if their behavior is not intuitive.
*   **Do not be "cute" when defining operator overloads; the result of the operation should be immediately obvious.**
*   **Do overload operators in a symmetric fashion** (e.g., if you overload `==`, also overload `!=`).
    *   **AI Refactoring Hint**: Flag non-intuitive operator overloads or asymmetric operator pairs. Suggest providing methods with friendly names instead of operator overloads if the semantics are not clear.

## Parameter Design

*   **Avoid using `out` or `ref` parameters.** Prefer returning values or using `Tuple` or custom types to return multiple values.
    *   **AI Refactoring Hint**: Flag methods using `out` or `ref` parameters and suggest alternative return mechanisms.
*   **Do not pass reference types by reference.** This can lead to confusing behavior.
*   **Do place all `out` parameters after all by-value and `ref` parameters.**
*   **Do be consistent in naming parameters** when overriding members or implementing interface members.