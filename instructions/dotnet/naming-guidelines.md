# Naming Guidelines

Based on [.NET Framework Design Guidelines](https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/naming-guidelines).

This document provides a summary of the naming guidelines for .NET, with a focus on helping AI coding agents adhere to best practices and identify refactoring opportunities.

## General Principles

*   **Consistency**: Follow a consistent set of naming conventions to improve usability and readability across projects.
*   **Clarity**: Names should be easily understood and convey the function of each element.
*   **Scope**: These guidelines primarily apply to publicly exposed APIs (public or protected types and members, and explicitly implemented interfaces).

## Capitalization Styles

*   **PascalCase**:
    *   Used for all public members, types, and namespaces.
    *   Examples: `MyClass`, `CalculateTotal`, `System.Collections`.
    *   **AI Refactoring Hint**: If an AI agent encounters public members, types, or namespaces not in PascalCase, it should flag them for refactoring.

*   **camelCase**:
    *   Used for parameters, local variables, and private fields.
    *   Examples: `inputParameter`, `localVariable`, `_privateField`.
    *   **AI Refactoring Hint**: Parameters or local variables not in camelCase should be refactored.

*   **SCREAMING_SNAKE_CASE**:
    *   Used for public static read-only fields (constants).
    *   Examples: `MAX_SIZE`, `DEFAULT_TIMEOUT`.
    *   **AI Refactoring Hint**: Public constants not in SCREAMING_SNAKE_CASE should be refactored.

## General Naming Conventions

*   **Abbreviations**:
    *   Avoid abbreviations unless they are widely accepted and understood (e.g., `UI`, `DB`).
    *   If using two-letter abbreviations, use PascalCase (e.g., `DB`).
    *   For longer abbreviations, capitalize only the first letter (e.g., `Xml`).
    *   **AI Refactoring Hint**: Identify and suggest expansion for ambiguous or non-standard abbreviations.

*   **Descriptive Names**:
    *   Use descriptive names that clearly indicate the purpose or function.
    *   Avoid overly generic names like `DoSomething`, `HandleData`.
    *   **AI Refactoring Hint**: Flag generic names and suggest more specific alternatives based on context.

## Naming Assemblies and DLLs

*   Use PascalCase.
*   Name assemblies based on the primary namespace they contain (e.g., `MyCompany.MyProduct.dll` for `MyCompany.MyProduct` namespace).

## Naming Namespaces

*   Use PascalCase.
*   Follow the format `CompanyName.TechnologyName` (e.g., `Microsoft.Win32`).
*   Avoid using the same name for a namespace and a type within that namespace.
*   **AI Refactoring Hint**: Check for namespace naming consistency and potential conflicts with type names.

## Naming Classes, Structs, and Interfaces

*   **Classes and Structs**:
    *   Use nouns or noun phrases.
    *   Use PascalCase.
    *   Examples: `FileStream`, `HttpRequest`.
    *   **AI Refactoring Hint**: Flag classes/structs with verb names or incorrect casing.

*   **Interfaces**:
    *   Prefix with the letter "I".
    *   Use PascalCase.
    *   Use nouns, adjective phrases, or verb phrases (e.g., `IDisposable`, `IComparable`, `ICommand`).
    *   **AI Refactoring Hint**: Identify interfaces missing the "I" prefix or with incorrect casing.

## Naming Type Members

*   **Methods**:
    *   Use verbs or verb phrases.
    *   Use PascalCase.
    *   Examples: `CalculateTotal`, `OpenFile`.
    *   **AI Refactoring Hint**: Flag methods with noun names or incorrect casing.

*   **Properties**:
    *   Use nouns or noun phrases.
    *   Use PascalCase.
    *   Examples: `Count`, `BackgroundColor`.
    *   **AI Refactoring Hint**: Flag properties with verb names or incorrect casing.

*   **Events**:
    *   Use verbs or verb phrases.
    *   Use PascalCase.
    *   Often named with the past tense of the verb (e.g., `Click` for `Clicked` event).
    *   **AI Refactoring Hint**: Check for consistent event naming patterns.

*   **Fields (Public/Protected)**:
    *   Avoid public or protected instance fields. Use properties instead.
    *   For public static read-only fields (constants), use SCREAMING_SNAKE_CASE.
    *   **AI Refactoring Hint**: Flag public/protected instance fields for conversion to properties.

## Naming Parameters

*   Use descriptive names.
*   Use camelCase.
*   Examples: `value`, `startIndex`.
*   **AI Refactoring Hint**: Flag parameters with non-descriptive names or incorrect casing.