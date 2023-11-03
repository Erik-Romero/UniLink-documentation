---
sidebar_position: 7
---

# Group Screen

The `HomeScreen` will be responsible for displaying a home screen with sections for events. It fetches data from Firebase and renders the information in a single scroll view.

## Dependencies

This component uses the following external libraries:

- `react`: The core React library for building user interfaces.
- `react-native`: A library for building mobile applications using React Native.
- `react-native-paper`: A library that provides components and theming for React Native apps.

```jsx
import React, { useState, useEffect } from 'react';
import { SafeAreaView, RefreshControl, View, ActivityIndicator, FlatList } from 'react-native';
import { FAB, DefaultTheme, Provider as PaperProvider } from 'react-native-paper';
import { styles } from './Stylings/GroupScreenStyles';
import { cardstyles } from './Stylings/GroupCardStyles';
import GroupCard from './Components/GroupCards';
import ChipTags from './Components/ChipTags';
import { FIREBASE_AUTH } from '../../../src/firebase_init/firebase';
import { collection, getDocs } from 'firebase/firestore';
import { firestore } from '../../../src/firebase_init/firebase';
```
## Props

The `GroupScreen` component accepts the following props.

- `navigation` This allows the screen to inherent the parents navigation object.

## State

The component has the following state:

- `groups` (array): Stores the fetched event data.
- `loading` (boolean): Represents the loading status of the page.
- `selectedTag` (boolean): Represents the current tag the posts should be filtered too.
- `auth` (boolean): This is the current user authentication token.

```jsx
export default function GroupsScreen({ navigation }) {
  const [groups, setGroups] = useState([]);
  const [loading, setLoading] = useState(true);
  const [selectedTag, setSelectedTag] = React.useState('All');
  const auth = FIREBASE_AUTH;
```
## Functions 
### useEffect Hook

The `useEffect` hook is used to fetch the event data from Firebase when the component mounts. It calls the `fetchEventData` function, which internally uses the `fetchData` function from `../DBFunctions/FetchData` to fetch event data and updates the `users` state.

```jsx

  useEffect(() => {
    fetchDataFromFirebase();
  }, []);

  const fetchDataFromFirebase = async () => {
    try {
      setLoading(true);
      const groupCollectionRef = collection(firestore, 'Groups');
      const groupSnapshot = await getDocs(groupCollectionRef);
      const groupData = groupSnapshot.docs.map(doc => doc.data());
      setGroups(groupData);
    } catch (error) {
      console.error('Error fetching group data:', error);
    } finally {
      setLoading(false);
    }
  };
  ```

### onRefresh
```jsx
const onRefresh = async () => {
    console.log("Refresh");
    fetchDataFromFirebase();
  };
```
### FloatingButton
```jsx
const FloatingButton = () => (
    <FAB backgroundColor={'#3498db'} icon="plus" style={styles.fab} onPress={() => navigation.navigate('GroupCreation', { onRefresh: onRefresh })} />
  );
```
### filteredProjectByTag
```jsx
const filterProjectsByTag = (tag) => {
    return tag === 'All' ? groups : groups.filter(project => project.Tags.includes(tag));
  };
```
### handlePress
```jsx
 const handlePress = (project) => {
    console.log('Project pressed:', project);
    const isMember = project.MembersID.includes(auth?.currentUser.uid);
    if (isMember) {
      navigation.navigate('GroupUsers', { groupDetails: project });
    } else {
      navigation.navigate('GroupDetailsScreen', { groupDetails: project });
    }
  };
```
### renderItem
```jsx
const renderItem = ({ item }) => (
    <GroupCard
      item={item}
      handlePress={handlePress}
    />
  );
```

## Code

```jsx
import React, { useState, useEffect } from 'react';
import { SafeAreaView, RefreshControl, View, ActivityIndicator, FlatList } from 'react-native';
import { FAB, DefaultTheme, Provider as PaperProvider } from 'react-native-paper';
import { styles } from './Stylings/GroupScreenStyles';
import { cardstyles } from './Stylings/GroupCardStyles';
import GroupCard from './Components/GroupCards';
import ChipTags from './Components/ChipTags';
import { FIREBASE_AUTH } from '../../../src/firebase_init/firebase';
import { collection, getDocs } from 'firebase/firestore';
import { firestore } from '../../../src/firebase_init/firebase';

export default function GroupsScreen({ navigation }) {
  const [groups, setGroups] = useState([]);
  const [loading, setLoading] = useState(true);
  const [selectedTag, setSelectedTag] = React.useState('All');
  const auth = FIREBASE_AUTH;

  // Function to handle pull-to-refresh
  const onRefresh = async () => {
    console.log("Refresh");
    fetchDataFromFirebase();
  };

  useEffect(() => {
    fetchDataFromFirebase();
  }, []);

  const fetchDataFromFirebase = async () => {
    try {
      setLoading(true);
      const groupCollectionRef = collection(firestore, 'Groups');
      const groupSnapshot = await getDocs(groupCollectionRef);
      const groupData = groupSnapshot.docs.map(doc => doc.data());
      setGroups(groupData);
    } catch (error) {
      console.error('Error fetching group data:', error);
    } finally {
      setLoading(false);
    }
  };

  // Custom FloatingButton component to navigate to CreateEventScreen
  const FloatingButton = () => (
    <FAB backgroundColor={'#3498db'} icon="plus" style={styles.fab} onPress={() => navigation.navigate('GroupCreation', { onRefresh: onRefresh })} />
  );

  const filterProjectsByTag = (tag) => {
    return tag === 'All' ? groups : groups.filter(project => project.Tags.includes(tag));
  };

  const handlePress = (project) => {
    console.log('Project pressed:', project);
    const isMember = project.MembersID.includes(auth?.currentUser.uid);
    if (isMember) {
      navigation.navigate('GroupUsers', { groupDetails: project });
    } else {
      navigation.navigate('GroupDetailsScreen', { groupDetails: project });
    }
  };

  const renderItem = ({ item }) => (
    <GroupCard
      item={item}
      handlePress={handlePress}
    />
  );

  return (
    <PaperProvider theme={theme}>
      <SafeAreaView style={styles.container}>
        {loading ? (
          <View style={styles.loadingContainer}>
            <ActivityIndicator size="large" color="#3498db" />
          </View>
        ) : (
          <View style={styles.scrollView}>
            <ChipTags selectedTag={selectedTag} setSelectedTag={setSelectedTag} />
            <FlatList
              data={filterProjectsByTag(selectedTag)}
              renderItem={renderItem}
              keyExtractor={(item) => item.id.toString()}
              numColumns={2}
              contentContainerStyle={cardstyles.flatListContainer
              }
              refreshControl={<RefreshControl refreshing={loading} onRefresh={onRefresh} />}
            />
          </View>
        )}
      </SafeAreaView>
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
```