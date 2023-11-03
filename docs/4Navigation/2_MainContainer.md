---
sidebar_position: 4
---

# Main Stack

This file is where every screen on our app originates from.

Therfore we call every screen, create a constant and populate the stack with the screens.

```jsx
//////////////////////////////////////////////////////////////////////////////////////
//////////////////This page is The inital container purposes/////////////////////////////////
//////////////////////////////////////////////////////////////////////////////////////
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import { TabNavigator } from './Screens/TabNavigator';
//////////////////////////////////////////////////////////////////////////////////////
/////////////////////////////Screen Imports///////////////////////////////////////////
//////////////////////////////////////////////////////////////////////////////////////
import NewUserScreen from './Screens/First_Screens/NewUserScreen';
import SplashScreen from './Screens/First_Screens/SplashScreen'; 
import CamScreen from './Screens/CamScreen';
import UploadThing from './Components/uploadThing';
import MessageScreen from './Screens/Messaging_screens/MessageScreen';
import LoginScreen from './Screens/First_Screens/LoginScreen';
import SignUpScreen from './Screens/First_Screens/SignUpScreen';
import NotifScreen from './Screens/NotifScreen';
import PortfolioScreen from './Screens/PortfolioScreen';
import CollaborationScreen from './Screens/CollaborationScreen';
import UserProfile from './Screens/UserProfile';
//////////////////////////////////////////////////////////////////////////////////////
////////////////////Variable Names for the Screens////////////////////////////////////
//////////////////////////////////////////////////////////////////////////////////////
export const ExistingUser = "TabNavigator"; 
const NewUser = "NewUserScreen";
const Splash = "SplashScreen";
export const Cams = "CamScreen";
const uploading= "UploadThing"
const messageName='Message';
const portfolioName = 'PortfolioScreen';
const collaborationName = 'CollaborationScreen'
export const Login='LoginScreen';
const SignUp ='SignUpScreen';
export const Notifications = "NotifScreen";
```

The following is where the MainStack is actaully initalized.

The prop headershown false/true is considered for each individual screen since some screen have further stacks  

```jsx
////////////////////////////////////////////////////////////////////////////////////////
/////////////////// Creating the Main stack navigator //////////////////////////////////
////////////////////////////////////////////////////////////////////////////////////////
const Stack = createStackNavigator();
function MainStack() { 
  return(
    <NavigationContainer>
       <Stack.Navigator initialRouteName="Splash">
        <Stack.Screen name={Splash} component={SplashScreen} options={{headerShown: false}}/>
        <Stack.Screen name={Login} component={LoginScreen} options={{ headerShown: false }}/>
        <Stack.Screen name={NewUser} component={NewUserScreen} options={{headerShown: true}}/>
        <Stack.Screen name={Cams} component={CamScreen} options={{headerShown: true}}/>
        <Stack.Screen name={SignUp} component={SignUpScreen} options={{headerShown: false}}/>
        <Stack.Screen name={ExistingUser} component={TabNavigator} options={{ headerShown: false,  gestureEnabled: false}}/>
        <Stack.Screen name={messageName} component={MessageScreen} options={{headerShown: true}}/>
        <Stack.Screen name={portfolioName} component={PortfolioScreen} options={{headerShown: true}}/>
        <Stack.Screen name={Notifications} component={NotifScreen} options={{headerShown: true}}/>
        <Stack.Screen name={collaborationName} component={CollaborationScreen} options={{headerShown: true}}/>
        <Stack.Screen name={"UserProfile"} component={UserProfile} options={{headerShown: true}}/>
    </Stack.Navigator>
    </NavigationContainer>
  );
}
export default MainStack;

////////////////////////////////////////////////////////////////////////////////////////
//////////////////////////// Camera Stack Navigation ///////////////////////////////////
////////////////////////////////////////////////////////////////////////////////////////
const CamStack = createStackNavigator();
export const CameraNav = () => (
  <NavigationContainer independent={true}>
    <CamStack.Navigator>
      <CamStack.Screen name={uploading} component={UploadThing}/>
      <CamStack.Screen name={Cams} component={CamScreen}/>
    </CamStack.Navigator>
  </NavigationContainer>
);
////////////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////////////
```

