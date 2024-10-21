
# React Component Development Guide  & Review Checklist


As we move from a rapid prototyping to production code development mindset, it is necessary to start giving our component design more attention.  The following is intended to be living documentation of development guidelines and best practices to help facilitate the delivery of efficient, maintainable, testable, and easily changeable components.    


## General Best Practices

1. Prefer Functional Components with hooks over class based components 
2. Keep components small and focused
3. keep minimal state and derive state where possible
4. Avoid unnecessary re-renders:
	1. Use [useMemo](https://react.dev/reference/react/useMemo) and [useCallbacks](https://react.dev/reference/react/useCallback) to cache calculations and functions
	1. Use `key` prop when rendering lists 
			- Use stable keys, avoid using indexes for dynamic data, this could lead to unwanted side effects.
	1. Avoid inline Functions: Define functions outside of render methods when possible to prevent re-creations.
5. Return clean-up functions in `useEffect` hooks to prevent memory leaks
6. Ensure all dependencies are specified in dependency arrays (If this isn't possible LEAVE A COMMENT EXPLAINING WHY)
7.  Extract logic from the component file where possible (especially Styles!!!)
8. Prefer typed object as state over many `useState` hooks
9. Use `context api` over prop drilling
10. Extract complex logic into custom hooks


## Component Design

The guiding principle of react component design should be that of **single responsibility** . The intent and usage of a component should be easily and immediately discernable to other developers reading your code. This is attainable by keeping components small and focused and having a clear purpose for each component you write.  To help avoid the temptation of the  *God Component* continually consider what type of component you are working on. 

### Component Types

#### Presentation Components: 
- Concerned with how things look. 
- Receive data and callbacks exclusively via props
- Do not manage their own state (except for UI state like toggle visibility)

#### Container (Smart) Components
- Concerned with how things work
- Handle data fetching and state management
- Pass data into presentational components via props
- Server as bridge between application logic and presentation components (Application logic should live outside of react components all together)

*see Compound Component Pattern or render props as patterns for when closer coupling of components is called for. 




