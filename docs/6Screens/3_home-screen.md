---
sidebar_position: 3
---

# Home Screen

The `HomeScreen` will be responsible for displaying a home screen with sections for events. It fetches data from Firebase and renders the information in a single scroll view.

## Dependencies

This component uses the following external libraries:

- `react`: The core React library for building user interfaces.
- `react-native`: A library for building mobile applications using React Native.
- `react-native-paper`: A library that provides components and theming for React Native apps.

Therefore we will need to import them all as so:

```js
import React,{ useState, useEffect } from 'react';
import { View, StyleSheet, StatusBar, ActivityIndicator,} from 'react-native';
import { fetchData } from '../../DBFunctions/FetchData';
import { FIREBASE_AUTH } from '../../../src/firebase_init/firebase';
import { primaryColors } from '../../Components/Colors';
import { fetchUserData} from '../../Components/UserData';
import EventsScreen from './EventsScreen';
import updateGroupIds from '../../DBFunctions/updateGroupIds';
import { updateProfile } from 'firebase/auth';
```


## Setting our Constants

```jsx
const HomeScreen = ({navigation}) => {
  const [users, setUsers] = useState([]);
  const [userDetails, setUserDetails] = useState(null);
  const [isLoading, setIsLoading] = useState(true); // State to track if data is being fetched
```
## Functions

### fetchDataandUserData

```jsx 
   const fetchDataAndUserData = async () => {
    setIsLoading(true); // Set loading state to true when fetching data
    const eventData = await fetchData();
    setUsers(eventData);
    const userData = await fetchUserData(FIREBASE_AUTH.currentUser?.uid);
    setUserDetails(userData[0]);
    console.log(FIREBASE_AUTH.currentUser?.uid);
    console.log(userData[0]);
    const newDisplayName =userData[0].FirstName+" "+userData[0].LastName;
    updateProfile(FIREBASE_AUTH.currentUser,{
      displayName: newDisplayName,
    })
    .then(() => {
      console.log("Display name updated successfully:", newDisplayName);
    })
    .catch((error) => {
      console.error("Error updating display name:", error.message);
    });
```

### useEffect Hook

The `useEffect` hook is used to fetch the event data from Firebase when the component mounts. It calls the `fetchEventData` function, which internally uses the `fetchData` function from `../DBFunctions/FetchData` to fetch event data and updates the `users` state.

```jsx
  useEffect(() => {
    updateGroupIds();
    fetchDataAndUserData();      
  },[]);
```

## The Screen

```jsx
    return (
    <View style={styles.container}>
      {isLoading ? 
      ( // Check if data is loading and show the ActivityIndicator
        <View style={styles.loadingContainer}>
          <ActivityIndicator size="large" color="white" />
        </View>  
      ) : (
        <EventsScreen userDetails={userDetails} navigation={navigation}/>     
      )}
    </View>
    );
};
```

## Styles

The component uses the `StyleSheet` object from `react-native` to define styles for the container, scrollView, card, and text. The design is set to display the cards in a horizontal layout with a maximum height of 400. Each card has a width of 300, a height of 400, and a margin of 4 for spacing.

Note: This documentation provides an overview of the `HomeScreen` component and its functionalities. For a comprehensive understanding and code implementation details, it is recommended to refer to the actual code.

```jsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: StatusBar.currentHeight,
    backgroundColor: primaryColors.blue,
  },
  loadingContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
});

export default HomeScreen;
```

## Code



