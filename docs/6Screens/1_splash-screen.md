---
sidebar_position: 1
---

# SplashScreen 

The `SplashScreen` will be the first screen that the user sees when opening the application. It displays a background image (`BGImage`) and the LaTech logo (`Logo`) with app title, sign-up button, and existing user button. The component utilizes the `ImageBackground`, `View`, `Text`, `StyleSheet`, and `Image` components from React Native. It also uses the `Provider` and `Button` components from `react-native-paper` for theming and button styling.

## Dependencies

This component uses the following external libraries:

- `react`: The core React library for building user interfaces.
- `react-native`: A library for building mobile applications using React Native.
- `LoginScreen`: This will be our custom LoginScreen, we have to import it in, in order to navigate to it.
- `SignUpScreen`: This will be our custom SignUpScreen, we have to import it in, in order to navigate to it.
- `FIREBASE_AUTH`: Firebase Wuthenticate will authenticate the current user.
- `AsyncStorage`: This library allows storage to be stored on the app between session. It will be used to store login information.
- `enc,AES`: Because we will be storing Login credentials encryption is needed, this is what this library does.
- `SignInWithEmailAndPassword`: This logins a user if they have ana account setup in Firebase.

therefore you will need to import all of them as so: 

```js
import * as React from 'react';
import { useState,useEffect } from 'react';
import { ImageBackground, View, Text, StyleSheet, Image } from 'react-native';
import { Button,Portal, DefaultTheme, Provider as PaperProvider } from 'react-native-paper';
import LoginScreen from './LoginScreen';
import SignUpScreen from './SignUpScreen';
import { FIREBASE_AUTH } from '../../../src/firebase_init/firebase';
import AsyncStorage from "@react-native-async-storage/async-storage";
import { enc, AES } from 'react-native-crypto-js';
import { signInWithEmailAndPassword} from "firebase/auth";
```

## Setting our Constants

The constants that are set up are references to the assets folder so that we will be able to retrieve the Splash background Image and the Latech Logo. As well as a to reference the FIREBASE_AUTH object so that we can verify the person logging in.

```js
const BGImage = require('../../../assets/SplashBG.png');
const Logo = require('../../../assets/LaTechLogo.png');
const auth = FIREBASE_AUTH;
```
Now we will step inside the Component and establish a constant within the component. This assures the constant is only called when the component is called rather then just being called once outside the component.

:::note
Why use useState? [Click here](../3React%20Native%20Basics/useState.md)  
:::
```js
export default function SplashScreen({ navigation }) {
  const [loading, setLoading] = useState(false);
}
```

## Functions

### SignIn

The SignIn function does exactly what the name implies, it calls the signInWithEmailAndPassword function to attempt to sign someone in. In this custom function it signs someone in and then takes them to the TabNavigator Screen.

:::note
For further documentation on signInWithEmailandPassword [Click Here](https://firebase.google.com/docs/auth/web/password-auth)
:::

```js
 // Set up your sign-in function
  const signIn = async (email, password) => {
    setLoading(true);
    try {
      const response = await signInWithEmailAndPassword(auth, email, password);
      // If sign-in is successful, update the user state
      navigation.replace('TabNavigator');
    } catch (error) {
        console.log(error);
    } finally {
        setLoading(false);
    }
  };

```

### loadLoginInfo

This functino checks to see if the person opening the app has logged in before. It does this by checking to see if there are login credentials stored through the AsyncStorage library. If it finds any credentials it will decrypt them and sign the user in. If it fails at any point it simply does not login the user and the user will have to manually log in.

```js
// Check if there is user data saved in AsyncStorage
  const loadLoginInfo = async () => {
    try {
      const encryptedEmail = await AsyncStorage.getItem('userEmail');
      const encryptedPassword = await AsyncStorage.getItem('userPassword');
      if (encryptedEmail && encryptedPassword) {
        const decryptedEmail = AES.decrypt(encryptedEmail, 'your-secret-key').toString(enc.Utf8);
        const decryptedPassword = AES.decrypt(encryptedPassword, 'your-secret-key').toString(enc.Utf8);
        // Try to sign in with the saved credentials
        await signIn(decryptedEmail, decryptedPassword);
      }
    } catch (error) {
      console.error('Error loading login info:', error);
    }
  };
```

### useEffect

As soon as the component mounts(mount meaning: When the component is rendered) it will run this command, and every time the component is mounted again it will rerun that command. In this intance every time the component is mounted it checks to see if there is login info stored. 

```js
  useEffect(() => {
    loadLoginInfo();
  }, []);

```

## The Screen

The following is what is returned when the component is called, therefore this is what the **Visuals** are. In the following section everything is wrapped in the PaperProvider theme so that every component follows the same style. Then the image is set at the bottom then a tint ontop of it. Then a portal is used to allow text to appear above the tint and above the image

```jsx
  return (
    <PaperProvider theme={theme}>
        <ImageBackground source={BGImage} resizeMode='cover' style={styles.image}>
            <View style={styles.Tint}>
                <Portal>
                    <View style={styles.container}>
                        <View style={styles.logoContainer}>
                            <Image style={styles.logo} source={Logo} />
                        </View>
                        <View style={styles.titleContainer}>
                            <Text style={styles.SplashscreenText}>Uni-Link</Text>
                        </View>
                        <View style={styles.buttonContainer}>
                            <Button onPress={() => navigation.navigate(SignUpScreen)} mode="contained" style={styles.Signup} buttonColor={'#cb333b'}>Sign Up</Button>
                            <Button onPress={() => navigation.navigate(LoginScreen)} mode="contained" style={styles.Existinguser} buttonColor={'#cb333b'}>Existing User</Button>
                        </View>
                    </View>
                </Portal>
            </View>
        </ImageBackground>
    </PaperProvider>
  );
```

Therefore this part of the code is what the Logo and button consists off. Here everything is within a view component so that the style prop can be modified. The style prop formats where each component will be located and what size and what color it will be.

```jsx
<View style={styles.container}>
    <View style={styles.logoContainer}>
        <Image style={styles.logo} source={Logo} />
    </View>
    <View style={styles.titleContainer}>
        <Text style={styles.SplashscreenText}>Uni-Link</Text>
    </View>
    <View style={styles.buttonContainer}>
        <Button onPress={() => navigation.navigate(SignUpScreen)} mode="contained" style={styles.Signup} buttonColor={'#cb333b'}>Sign Up</Button>
        <Button onPress={() => navigation.navigate(LoginScreen)} mode="contained" style={styles.Existinguser} buttonColor={'#cb333b'}>Existing User</Button>
    </View>
</View>
```

The Buttons have an Onpress prop which allows us to set navigation accordingly. If the sign up Button is pressed it triggers the navigation.navigate() action(This can be changed to allow any other action to occur). 

```jsx
<Button 
    onPress={() => navigation.navigate(SignUpScreen)} 
    mode="contained" 
    style={styles.Signup} 
    buttonColor={'#cb333b'}>
        Sign Up
</Button>

```

In React every component needs to be closed off, that is why every tag is closed off with a secondary tag with "/" to signify the end of the component.


## UI Components

1. Background Image (`ImageBackground`):
   - Source: `BGImage`
   - Resize Mode: `'cover'`
   - Style: `styles.image`

2. Tint View (`View`):
   - Style: `styles.Tint`
   - Children:
     - LaTech Logo (`Image`): Source - `Logo`, Style - `styles.logo`
     - Top Menu View (`View`):
       - Style: `styles.TopMenu`
       - Children:
         - App Title (`Text`): Style - `styles.SplashscreenText`
         - Sign Up Button (`Button`):
           - OnPress: Navigate to `NewUserScreen`
           - Mode: `'contained'`
           - Style: `styles.Signup`
           - Button Color: `'#cb333b'`
         - Existing User Button (`Button`):
           - OnPress: Navigate to `MainContainer`
           - Mode: `'contained'`
           - Style: `styles.Existinguser`
           - Button Color: `'#cb333b'`

## Styles

The component uses the `StyleSheet` object from `react-native` to define the styles. It includes styles for the logo, image, existing user button, sign-up button, splash screen text, tint view, and top menu view.


```js

const theme = {
  ...DefaultTheme,
  roundness: 2,
  colors: {
    ...DefaultTheme.colors,
    primary: '#3498db',
    accent: '#f1c40f',
  },
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  logoContainer: {
    marginBottom: 20, // Add margin between logo and title
  },
  logo: {
    width: 200,
    height: 180,
    alignSelf: 'center',
  },
  titleContainer: {
    marginBottom: 10, // Add margin between title and buttons
  },
  SplashscreenText: {
    fontSize: 60,
    fontWeight: 'bold',
    color: 'white',
  },
  buttonContainer: {
    alignItems: 'center',
    width: '100%', // Set the width of the button wrapper
    marginTop: 10,
  },
  Existinguser: {
    marginTop: 20,
    width: '40%',
  },
  Signup: {
    marginTop: 10,
    width: '40%',
  },
  image: {
    flex: 1,
    justifyContent: 'center',
  },
  Tint: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
    backgroundColor: '#003087',
    opacity: 0.8,
  },
});
```

## Code

```js 
import * as React from 'react';
import { useState,useEffect } from 'react';
import { ImageBackground, View, Text, StyleSheet, Image } from 'react-native';
import { Button,Portal, DefaultTheme, Provider as PaperProvider } from 'react-native-paper';
import LoginScreen from './LoginScreen';
import SignUpScreen from './SignUpScreen';
import { FIREBASE_AUTH } from '../../../src/firebase_init/firebase';
import AsyncStorage from "@react-native-async-storage/async-storage";
import { enc, AES } from 'react-native-crypto-js';
import { signInWithEmailAndPassword} from "firebase/auth";

const BGImage = require('../../../assets/SplashBG.png');
const Logo = require('../../../assets/LaTechLogo.png');
const auth = FIREBASE_AUTH;

export default function SplashScreen({ navigation }) {
  
  const [loading, setLoading] = useState(false);
  // Set up your sign-in function
  const signIn = async (email, password) => {
    setLoading(true);
    try {
      const response = await signInWithEmailAndPassword(auth, email, password);
      // If sign-in is successful, update the user state
      navigation.replace('TabNavigator');
    } catch (error) {
        console.log(error);
    } finally {
        setLoading(false);
    }
  };
  // Check if there is user data saved in AsyncStorage
  const loadLoginInfo = async () => {
    try {
      const encryptedEmail = await AsyncStorage.getItem('userEmail');
      const encryptedPassword = await AsyncStorage.getItem('userPassword');
      if (encryptedEmail && encryptedPassword) {
        const decryptedEmail = AES.decrypt(encryptedEmail, 'your-secret-key').toString(enc.Utf8);
        const decryptedPassword = AES.decrypt(encryptedPassword, 'your-secret-key').toString(enc.Utf8);
        
        // Try to sign in with the saved credentials
        await signIn(decryptedEmail, decryptedPassword);
      }
    } catch (error) {
      console.error('Error loading login info:', error);
    }
  };
  
  useEffect(() => {
    loadLoginInfo();
  }, []);

 
  return (
    <PaperProvider theme={theme}>
      <ImageBackground source={BGImage} resizeMode='cover' style={styles.image}>
        <View style={styles.Tint}>
          <Portal>
            <View style={styles.container}>
              {/* App Logo */}
              <View style={styles.logoContainer}>
                <Image style={styles.logo} source={Logo} />
              </View>
              {/* App Title */}
              <View style={styles.titleContainer}>
                <Text style={styles.SplashscreenText}>Uni-Link</Text>
              </View>
              {/* Buttons */}
              <View style={styles.buttonContainer}>
                {/* Sign Up Button */}
                <Button onPress={() => navigation.navigate(SignUpScreen)} mode="contained" style={styles.Signup} buttonColor={'#cb333b'}>Sign Up</Button>
                {/* Existing User Button */}
                <Button onPress={() => navigation.navigate(LoginScreen)} mode="contained" style={styles.Existinguser} buttonColor={'#cb333b'}>Existing User</Button>
              </View>
            </View>
          </Portal>
        </View>
      </ImageBackground>
    </PaperProvider>
  );
}

const theme = {
  ...DefaultTheme,
  roundness: 2,
  colors: {
    ...DefaultTheme.colors,
    primary: '#3498db',
    accent: '#f1c40f',
  },
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  logoContainer: {
    marginBottom: 20, // Add margin between logo and title
  },
  logo: {
    width: 200,
    height: 180,
    alignSelf: 'center',
  },
  titleContainer: {
    marginBottom: 10, // Add margin between title and buttons
  },
  SplashscreenText: {
    fontSize: 60,
    fontWeight: 'bold',
    color: 'white',
  },
  buttonContainer: {
    alignItems: 'center',
    width: '100%', // Set the width of the button wrapper
    marginTop: 10,
  },
  Existinguser: {
    marginTop: 20,
    width: '40%',
  },
  Signup: {
    marginTop: 10,
    width: '40%',
  },
  image: {
    flex: 1,
    justifyContent: 'center',
  },
  Tint: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
    backgroundColor: '#003087',
    opacity: 0.8,
  },
});

```
