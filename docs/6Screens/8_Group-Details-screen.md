---
sidebar_position: 8
---

# Group Details Screen

The `HomeScreen` will be responsible for displaying a home screen with sections for events. It fetches data from Firebase and renders the information in a single scroll view.

## Dependencies

This component uses the following external libraries:

- `react`: The core React library for building user interfaces.
- `react-native`: A library for building mobile applications using React Native.
- `react-native-paper`: A library that provides components and theming for React Native apps.


## Props

The `HomeScreen` component does not accept any props.

## State

The component has the following state:

- `users` (array): Stores the fetched event data.
- `refreshing` (boolean): Represents the refreshing status of the ScrollView.


## useEffect Hook

The `useEffect` hook is used to fetch the event data from Firebase when the component mounts. It calls the `fetchEventData` function, which internally uses the `fetchData` function from `../DBFunctions/FetchData` to fetch event data and updates the `users` state.


## Pull-to-Refresh with RefreshControl

The component implements pull-to-refresh functionality using the `RefreshControl` component from `react-native`. When users pull down the ScrollView, it triggers the `onRefresh` function from `../DBFunctions/RefreshFunctions`, which updates the `refreshing` state and refetches the event data.



## Styles

The component uses the `StyleSheet` object from `react-native` to define styles for the container, scrollView, card, and text. The design is set to display the cards in a horizontal layout with a maximum height of 400. Each card has a width of 300, a height of 400, and a margin of 4 for spacing.

Note: This documentation provides an overview of the `HomeScreen` component and its functionalities. For a comprehensive understanding and code implementation details, it is recommended to refer to the actual code.




## Code

```jsx
import React from 'react';
import { View,StyleSheet, KeyboardAvoidingView} from 'react-native';
import RedLine from '../Home_Screens/Components/RedLine';
import { Image } from 'expo-image';
import { createMaterialTopTabNavigator } from '@react-navigation/material-top-tabs';
import { NavigationContainer } from '@react-navigation/native';
import { Dimensions } from 'react-native';
import GroupUpdates from './GroupUpdates';
import GroupDetail from './GroupDetail';
const { height } = Dimensions.get('window');

const GroupDetailsScreen = ({ route }) => {
  const { groupDetails } = route.params;
  console.log('Group Details:', groupDetails);
  const Tab = createMaterialTopTabNavigator();
  
  function TabScreens() {
    return (
      <NavigationContainer independent={true}>
        <Tab.Navigator>
          <Tab.Screen name="Details" component={GroupDetail} initialParams={{groupDetails}}/>
          <Tab.Screen name="Updates" component={GroupUpdates} initialParams={{groupDetails}}/>
        </Tab.Navigator>
      </NavigationContainer>
    );
  }

  return (
    <KeyboardAvoidingView style={{ flex: 1 }} behavior="padding" enabled>
      <View style={styles.container}>
        <Image source={{uri: groupDetails.Image_Link}} style={styles.image}/>
        <RedLine />
        <TabScreens/>
      </View>
    </KeyboardAvoidingView>
  );
};

const styles = StyleSheet.create({
  container: {
    flexGrow: 1,
  },
  image: {
    width: '100%',
    height: height / 8, // Set the height to one-third of the screen height
  },
});

export default GroupDetailsScreen;

```