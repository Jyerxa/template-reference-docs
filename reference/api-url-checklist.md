# REST API URL Design Checklist

## General Principles
1. **Clarity and Readability**
   - Ensure URLs are easy to read and understand.
   - Avoid using special characters and ensure URLs are human-readable.

2. **Consistency**
   - Use a consistent pattern for URL structures across the API.

## Resource Naming
1. **Nouns, Not Verbs**
   - Use nouns to represent resources (e.g., `/users`, `/orders`), not actions.

2. **Plural Forms**
   - Use plural nouns to represent collections of resources (e.g., `/users`, `/products`).

3. **Lowercase Letters**
   - Use lowercase letters in URLs (e.g., `/users`, `/orders`).

## Resource Hierarchy and Relationships
1. **Hierarchical Structure**
   - Represent relationships using a hierarchical URL structure (e.g., `/users/{userId}/orders`).

2. **Resource Nesting**
   - Nest URLs to show resource ownership or relationships (e.g., `/authors/{authorId}/books`).

3. **Avoid Deep Nesting**
   - Avoid overly deep nesting to keep URLs manageable (e.g., `/users/{userId}/orders/{orderId}/items` is acceptable; deeper nesting should be avoided).

## Filtering, Sorting, and Pagination
1. **Query Parameters for Filtering**
   - Use query parameters to filter resources (e.g., `/products?category=electronics`).

2. **Sorting**
   - Use query parameters for sorting (e.g., `/products?sort=price`).

3. **Pagination**
   - Implement pagination using query parameters (e.g., `/items?page=2&limit=10`).

## Actions on Resources
1. **Sub-Resource for Actions**
   - Use sub-resources to represent actions that change the state of a resource (e.g., `/orders/{orderId}/cancel`).

2. **HTTP Methods for Actions**
   - Use appropriate HTTP methods to perform actions (e.g., `POST /orders` to create an order, `PUT /orders/{orderId}` to update an order).

## Resource Identification
1. **Use of IDs**
   - Use unique identifiers to represent specific resources (e.g., `/users/{userId}`, `/orders/{orderId}`).

2. **Avoid Action Words**
   - Avoid using action words in URLs (e.g., `/createUser` should be `/users` with `POST` method).

## Versioning
1. **Version in URL**
   - Include the API version in the URL (e.g., `/v1/users`).

## HTTP Methods Mapping
1. **GET**
   - Retrieve resources (e.g., `GET /users`).

2. **POST**
   - Create new resources (e.g., `POST /users`).

3. **PUT**
   - Update existing resources (e.g., `PUT /users/{userId}`).

4. **PATCH**
   - Partially update resources (e.g., `PATCH /users/{userId}`).

5. **DELETE**
   - Delete resources (e.g., `DELETE /users/{userId}`).

## URL Length
1. **Keep URLs Short**
   - Avoid excessively long URLs. Keep them concise while maintaining clarity.

## Resource Collection and Single Resource
1. **Collection URL**
   - Use plural nouns for collections (e.g., `/users`).

2. **Single Resource URL**
   - Use the resource ID for single resources (e.g., `/users/{userId}`).

## Use of Hyphens
1. **Hyphens for Readability**
   - Use hyphens to improve readability in URLs (e.g., `/my-products`, not `/my_products`).

By following this checklist, you can ensure that your REST API URLs are well-designed, consistent, and easy to use, contributing to a better overall API experience.
