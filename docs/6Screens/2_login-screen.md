---
sidebar_position: 2
---

# Login Screen

The `LoginScreen` will be the second screen the user sees. It wil displays two input prompts allowing the user to enter their email and password. The component utilizes the `Stylesheet`, `Button`, `keyboardAvoidingView`, `StyleSheet`, components from React Native. It also uses the Firebase Authentication to verify if a user can properly login.

## Dependencies

This component uses the following external libraries:

- `react`: The core React library for building user interfaces.
- `react-native`: A library for building mobile applications using React Native.
- `FIREBASE_AUTH`: Firebase Wuthenticate will authenticate the current user.
- `Activity Indicator`: A react native paper component that displays a loading icon.
- `SignInWithEmailAndPassword`: This logins a user if they have ana account setup in Firebase.
- `ExistingUser`: The Existing user screen contains further navigation
- `AsyncStorage`: This library allows storage to be stored on the app between session. It will be used to store login information.
- `enc,AES`: Because we will be storing Login credentials, encryption will be needed, this is what this library focuses on.

therefore you will need to import all of them as so: 

```jsx
import React, { useState } from "react";
import { View, StyleSheet, Button, KeyboardAvoidingView, ImageBackground,TextInput } from "react-native";
import { FIREBASE_AUTH } from "../../../src/firebase_init/firebase";
import { ActivityIndicator } from "react-native-paper";
import { signInWithEmailAndPassword } from "firebase/auth";
import { ExistingUser } from "../../MainStack";
import AsyncStorage from '@react-native-async-storage/async-storage';
import { enc, AES } from 'react-native-crypto-js';

```

## Setting our Constants

```jsx
const LoginScreen = ({ navigation }) => {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [loading, setLoading] = useState(false);
  const auth = FIREBASE_AUTH;
```

## Functions

### saveLoginInfo

```jsx
  const saveLoginInfo = async (email, password) => {
    try {
      const encryptedEmail = AES.encrypt(email, 'your-secret-key').toString();
      const encryptedPassword = AES.encrypt(password, 'your-secret-key').toString();
  
      await AsyncStorage.setItem('userEmail', encryptedEmail);
      await AsyncStorage.setItem('userPassword', encryptedPassword);
    } catch (error) {
      console.error('Error saving login info:', error);
    }
  };
```

### signIn

```jsx
  const signIn= async ()=>{
    setLoading(true);
    try
    {
        const response=await signInWithEmailAndPassword(auth, email,password);
        saveLoginInfo(email,password);
        navigation.replace(ExistingUser);
    }
    catch (error: any){
        console.log(error);
        alert('Sign In failed'+ error.message);
    }
    finally {
        setLoading(false);
    }
}
```
## The Screen

```jsx

  return (
    <ImageBackground
      source={require('../../../assets/image.png')}
      style={styles.backgroundImage}
    >
      <View style={styles.container}>
        <KeyboardAvoidingView behavior="padding">
          <TextInput value={email} style={styles.input} placeholder="Email" autoCapitalize="none" onChangeText={(text) => setEmail(text)} />
          <TextInput secureTextEntry={true} value={password} style={styles.input} placeholder="Password" autoCapitalize="none" onChangeText={(text) => setPassword(text)} />
          {loading ? <ActivityIndicator size={"large"} color="0000ff" /> :
            <Button title="Login" onPress={() => signIn()} />
          }
        </KeyboardAvoidingView>
      </View>
    </ImageBackground>
  );
};

```

## Styles

The component uses the `StyleSheet` object from `react-native` to define the styles. It includes styles for the logo, image, existing user button, sign-up button, splash screen text, tint view, and top menu view.


```jsx

const styles = StyleSheet.create({
  container: {
    marginHorizontal: 20,
    flex: 1,
    justifyContent: "center",
  },
  input: {
    marginVertical: 4,
    height: 50,
    borderWidth: 1,
    borderRadius: 4,
    padding: 10,
    backgroundColor: "#fff",
  },
});

export default LoginScreen;
```

