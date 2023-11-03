---
sidebar_position: 1
---

# Button 

The React Native framework provides a basic yet essential component known as `Button`, which is used for handling user button presses. The `Button` component is easy to use and can be customized to a certain extent. It is a part of React Native's core components and APIs.

## Usage

To use the `Button` component, you must first import it from React Native.

```javascript
import { Button } from 'react-native';
```

Once imported, you can render the Button component within your component's render method or functional component return statement.

```jsx 
<Button
  onPress={() => {
    alert('You tapped the button!');
  }}
  title="Press Me"
/>
```

## Props

The `Button` component in React Native is used for rendering a basic button and handling user interaction through touches. It supports several props that allow for basic customization:

- `onPress` (Function): A handler that is called when the user taps the button. This callback is required.
- `title` (string): The text that's displayed on the button, which is also a required field.
- `color` (string): Changes the button color. On iOS, it affects the text color, while on Android, it changes the background color.
- `disabled` (boolean): If set to `true`, the button will be disabled and non-interactive.
- `testID` (string): Used to locate this component in tests using frameworks like Jest or Detox.

## Example

Here is a straightforward example of how to implement the `Button` component in a React Native application:

```jsx
import React from 'react';
import { View, Button, Alert } from 'react-native';

const App = () => {
  const onButtonPress = () => {
    Alert.alert('Button Clicked!', 'You have pressed the button.');
  };

  return (
    <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
      <Button
        onPress={onButtonPress}
        title="Press Me"
        color="#1E90FF"
        accessibilityLabel="Tap this button to trigger an alert"
      />
    </View>
  );
};

export default App;
```

:::note

For more information head over to React native's website and documentation.

[Click Here](https://reactnative.dev/docs/getting-started)

:::