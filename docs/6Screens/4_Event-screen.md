---
sidebar_position: 4
---

# Event Screen

The `HomeScreen` will be responsible for displaying a home screen with sections for events. It fetches data from Firebase and renders the information in a single scroll view.

## Dependencies

This component uses the following external libraries:

- `react`: The core React library for building user interfaces.
- `react-native`: A library for building mobile applications using React Native.
- `react-native-paper`: A library that provides components and theming for React Native apps.

```jsx
import * as React from 'react';
import { StyleSheet, SafeAreaView,Text, ScrollView, StatusBar, RefreshControl, View, ActivityIndicator } from 'react-native';
import { FAB, DefaultTheme, Provider as PaperProvider } from 'react-native-paper';
import EventCard from './Components/EventCard';
import { useState, useEffect } from 'react';
import { fetchData } from '../../DBFunctions/FetchData';
import { primaryColors } from '../../Components/Colors';
import RedLine from './Components/RedLine';
import { FIREBASE_AUTH } from '../../../src/firebase_init/firebase';
import { updateProfile } from 'firebase/auth';
```

## Props

The `EventsScreen` component accepts the following props:

- `navigation`: A navigation object provided by React Navigation to handle screen navigation.
- `userDetails`: userDetails was fetched in the home screen and was passed into the event screen



## Setting our Constants

The component has the following state:

- `refreshing` (boolean): Represents the refreshing status of the ScrollView.
- `users` (array): Stores the fetched event data.

```jsx
export default function EventsScreen({ navigation,userDetails }) {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
```
## Functions

### useEffect Hook

The `useEffect` hook is used to fetch the event data from Firebase when the component mounts. It calls the `fetchEventData` function, which internally uses the `fetchData` function from `../DBFunctions/FetchData` to fetch event data and updates the `users` state.

```jsx
useEffect(() => {
    const fetchEventData = async () => {
      try {
        const eventData = await fetchData();
        setUsers(eventData);
      } catch (error) {
        console.error('Error fetching data:', error);
      } finally {
        setLoading(false); // Set loading to false when the data is fetched
      }
    };
    fetchEventData();
  }, []);
```

### Pull-to-Refresh with RefreshControl

The component implements pull-to-refresh functionality using the `RefreshControl` component from `react-native`. When users pull down the ScrollView, it triggers the `onRefresh` function from `../DBFunctions/RefreshFunctions`, which updates the `refreshing` state and refetches the event data.

```jsx
const onRefresh = async () => {
    try {
      setLoading(true); // Set loading to true while fetching data
      const usersData = await fetchData(); // Call the fetchData function
      setUsers(usersData);
      const currentUser = FIREBASE_AUTH.currentUser;
      if (currentUser) {
        const newDisplayName =userDetails.FirstName+" "+userDetails.LastName;
        updateProfile(currentUser,{
          displayName: newDisplayName,
        })
        .then(() => {
          console.log("Display name updated successfully:", newDisplayName);
        })
        .catch((error) => {
          console.error("Error updating display name:", error.message);
        });
        
      } else {
        console.log("No user is currently authenticated.");
      }
    } catch (error) {
      console.error('Error refreshing data:', error);
    } finally {setTimeout(() => {
      setLoading(false)
    }, 500);
    }
  };
```

### Custom Floating Action Button

The component defines a custom `FloatingButton` component, a floating action button rendered as an icon with a background color. When pressed, it navigates to the `CreateEventScreen` using the provided `navigation` prop.

```jsx
const FloatingButton = () => (
    <FAB backgroundColor={'#3498db'} icon="plus" style={styles.fab} onPress={() => navigation.navigate('CreateEventScreen', {onRefresh: onRefresh,})}/>
  );

```


## Styles

The component uses the `StyleSheet` object from `react-native` to define styles for the container, scrollView, text, backdrop, and the custom floating action button. This approach ensures consistent and maintainable styling.

### Theme Configuration

The `theme` object is used to configure the theme for the `PaperProvider`. It extends the `DefaultTheme` and customizes the colors for primary and accent to match the application's visual style.

```jsx
const theme = {
  ...DefaultTheme,
  roundness: 2,
  colors: {
    ...DefaultTheme.colors,
    primary: '#3498db',
    accent: '#f1c40f',
  },
};

```
### The styles object
```jsx

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: StatusBar.currentHeight,
  },
  scrollView: {
    marginHorizontal: 10,
  },
  text: {
    fontSize: 42,
  },
  backdrop: {
    backgroundColor: 'rgba(0, 0, 0, 0.5)',
  },
  fab: {
    position: 'absolute',
    margin: 16,
    right: 0,
    bottom: 0,
  },
  loadingContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  header: {
    backgroundColor:primaryColors.blue,
    justifyContent:'center',
    alignItems:'center',
  },
  headerText:{
    fontSize:40, 
    fontWeight:'bold',
    color:'white',
    textAlign: 'center', // Center text horizontally
    textAlignVertical: 'center',
  },
});
```

## Code


```jsx
export default function EventsScreen({ navigation,userDetails }) {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);

  // Function to handle pull-to-refresh
  const onRefresh = async () => {
    try {
      setLoading(true); // Set loading to true while fetching data
      const usersData = await fetchData(); // Call the fetchData function
      setUsers(usersData);
      const currentUser = FIREBASE_AUTH.currentUser;
      if (currentUser) {
        const newDisplayName =userDetails.FirstName+" "+userDetails.LastName;
        updateProfile(currentUser,{
          displayName: newDisplayName,
        })
        .then(() => {
          console.log("Display name updated successfully:", newDisplayName);
        })
        .catch((error) => {
          console.error("Error updating display name:", error.message);
        });
        
      } else {
        console.log("No user is currently authenticated.");
      }
    } catch (error) {
      console.error('Error refreshing data:', error);
    } finally {setTimeout(() => {
      setLoading(false)
    }, 500);
    }
  };

  useEffect(() => {
    const fetchEventData = async () => {
      try {
        const eventData = await fetchData();
        setUsers(eventData);
      } catch (error) {
        console.error('Error fetching data:', error);
      } finally {
        setLoading(false); // Set loading to false when the data is fetched
      }
    };
    fetchEventData();
  }, []);

  // Custom FloatingButton component to navigate to CreateEventScreen
  const FloatingButton = () => (
    <FAB backgroundColor={'#3498db'} icon="plus" style={styles.fab} onPress={() => navigation.navigate('CreateEventScreen', {onRefresh: onRefresh,})}/>
  );

  return (
    <PaperProvider theme={theme}>
      <SafeAreaView style={styles.container}>
        {loading ? (
          // Show the loading icon when data is being fetched
          <View style={styles.loadingContainer}>
            <ActivityIndicator size="large" color="#3498db" />
          </View>
        ) : (
          // Show the ScrollView when data is loaded
          <ScrollView
            style={styles.scrollView}
            // Add a RefreshControl to enable pull-to-refresh functionality
            refreshControl={<RefreshControl refreshing={loading} onRefresh={onRefresh} />}
          >  
            <View style={[styles.header]}>
              <Text style={styles.headerText}>Welcome Back {userDetails?.FirstName}! </Text>
            </View>
            <RedLine/>
            {/* Render the EventCard component and pass the fetched event data as props */}
            <EventCard users={users} />
          </ScrollView>
        )}
      </SafeAreaView>
      {/* Render the custom FloatingButton */}
      <FloatingButton />
    </PaperProvider>
  );
}

// Theme configuration for PaperProvider
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
    paddingTop: StatusBar.currentHeight,
  },
  scrollView: {
    marginHorizontal: 10,
  },
  text: {
    fontSize: 42,
  },
  backdrop: {
    backgroundColor: 'rgba(0, 0, 0, 0.5)',
  },
  fab: {
    position: 'absolute',
    margin: 16,
    right: 0,
    bottom: 0,
  },
  loadingContainer: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  header: {
    backgroundColor:primaryColors.blue,
    justifyContent:'center',
    alignItems:'center',
  },
  headerText:{
    fontSize:40, 
    fontWeight:'bold',
    color:'white',
    textAlign: 'center', // Center text horizontally
    textAlignVertical: 'center',
  },
});


```

