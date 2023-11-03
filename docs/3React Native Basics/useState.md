# Using useState in React Native

## Introduction

In React Native, the `useState` hook is a fundamental part of the React Hooks API. It is used to add state to functional components, allowing them to manage and update their own state.

## How to Use useState

To use `useState` in a React Native component, follow these steps:

1. **Import `useState` from React:**

```javascript
   import React, { useState } from 'react';
```

2. **Declare a state variable:**

```javascript
   const [variable, setVariable] = useState(initialValue);
```

## Why Use useState
1. Functional Components:

2. Enables state management in functional components, which previously were stateless.
Simplified Syntax:

3. Provides a concise syntax for declaring and updating state compared to class components.
Asynchronous Updates:

4. State updates with useState are asynchronous, ensuring optimal performance.
Immutability:

5. Promotes the principle of immutability, enhancing predictability and ease of debugging.
Cleaner Code:

6. Reduces boilerplate code associated with class components, making code more readable.

## Use cases in project

useState is used over variables in react native because useState allows values to be changed during runetime. Every time a useState's state is updated it triggers the compoenent in react native to remount. This is espiciallt help when updating text, components, images etc. 

However you have to be carfeul when you update a state because if called improperly it can force a screen to rerender too many times.
:::note

For more information head over to React native's website and documentation.

[Click Here](https://reactnative.dev/docs/getting-started)

:::