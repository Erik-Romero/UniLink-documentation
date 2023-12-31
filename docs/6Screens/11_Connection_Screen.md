---
sidebar_position: 11
---

# ConnectionScreen

The `ConnectionScreen` is the screen that handles the friend's list side of the application. There are two tabs, one for connected people and one for all other mutual connections.  In the Mutual Connections user's can click the Connect button to send a request to that user.  That user will then receieve a notification to either accept or decline the sender's connect request. 

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
import React, { useEffect, useState} from 'react';
import { StyleSheet, SafeAreaView, ScrollView, StatusBar, RefreshControl,TextInput, TouchableOpacity, Text, View, Button } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createMaterialTopTabNavigator } from '@react-navigation/material-top-tabs';
import { createStackNavigator } from '@react-navigation/stack'; 
import ConnectCard from '../Components/ConnectCard';
import MutualCard from '../Components/MutualCard';
import { updateDoc, doc, collection, getDocs,getDoc} from '@firebase/firestore';
import { firestore, FIREBASE_AUTH } from '../../src/firebase_init/firebase';
import { fetchUserData } from '../Components/UserData';
import { Picker } from '@react-native-picker/picker';
import OthersProfileScreen from './OthersProfileScreen';
```

## Functions for fetching user data from firebase database 

```js
export const fetchData = async () => {
  try {
    const eventsRef = collection(firestore, 'User_data');
    const querySnapshot = await getDocs(eventsRef);
    const usersData = [];
    querySnapshot.forEach((doc) => {
      const { Email, Password, Username, Major, About_me, Skills, Interests, Connections, Projects, Experience, true_connections } = doc.data();
      usersData.push({
        id: doc.id,
        Email,
        Password,
        Username,
        Major,
        About_me, 
        Skills, 
        Interests,
        Projects,
        Connections,
        Experience,
        true_connections

      });
    });
    return usersData;
  } catch (error) {
    console.error('Error fetching data:', error);
    return [];
  }
};
export const fetchAllUsers = async () => {
  try {
    const eventsRef = collection(firestore, 'User_data');
    const querySnapshot = await getDocs(eventsRef);
    const usersData = [];
    querySnapshot.forEach((doc) => {
      const {FirstName, LastName, Major,Profile_Image,Connections,Id, Skills, Interests, Projects, Experience} = doc.data();
      usersData.push({
        id:doc.id,
        Id,
        FirstName,
        LastName,
        Major,
        Profile_Image,
        Connections,
        Skills,
        Interests,
        Projects, 
        Experience,
      });
    });
    return usersData;
  } catch (error) {
    console.error('Error fetching data:', error);
    return [];
  }
};
```

## Rest of the code for network screen
```js

function ConnectionsScreen({ navigation, route }) {
  const { handleNavigate } = route.params;
  // Common fetching logic for both screens
  const [loading, setLoading] = useState(false);
  const [users, setUsers] = useState([]);
  const [userDetails, setUserDetails] = useState(null);
  const [usersData, setUsersData] = useState(null);
  const [searchText, setSearchText] = useState('');
  

  const fetchDataAndUserData = async () => {
    setLoading(true); // Set loading state to true when fetching data
    const usersData = await fetchAllUsers();
    setUsers(usersData);
    setUsersData(usersData);
    const userData = await fetchUserData(FIREBASE_AUTH.currentUser?.uid);
    setUserDetails(userData[0]);
    setLoading(false); // Set loading state to false when data fetching is complete
  };

  const onRefresh = async () => {
    try {
      fetchDataAndUserData();  
      userDetails.true_connections = userDetails.Connections;
    } catch (error) {
      console.error('Error refreshing data:', error);
    } finally {
      setTimeout(() => {
        setLoading(false);
      }, 500);
    }
  };

  useEffect(() => {
    fetchDataAndUserData();  
    fetchData();
  }, []);
  
  const handleDisconnect = async (userToRemove) => {
    try {
      userDetails.Connections = userDetails.Connections?.filter(item => item !== userToRemove);
      const documentRef = doc(firestore,'User_data', userDetails.id);
      try {
        await updateDoc(documentRef, userDetails);
        console.log('Document updated successfully');
      } catch (error) {
        console.error('Error updating document:', error);
      }
    } catch (error) {
      console.error('Error disconnecting user', error);
    }
    onRefresh();

  };

  let filteredArrayC = usersData?.filter(item => userDetails?.true_connections.includes(item.id));


  if(searchText==''){
    filteredArrayC=filteredArrayC?.filter(item=>item.Id!==FIREBASE_AUTH?.currentUser.uid);
  }
  else{
    filteredArrayC=filteredArrayC?.filter(item=>item.FirstName.includes(searchText));
    
  }
  

  return (
    <SafeAreaView style={styles.container}>
      <ScrollView
        style={styles.scrollView}
        refreshControl={<RefreshControl refreshing={loading} onRefresh={onRefresh} />} >
          <TextInput
        style={styles.searchInput}
        placeholder="Search..."
        onChangeText={(text) => setSearchText(text)}
        value={searchText}
      />
        {filteredArrayC?.map((user) => (
          <MutualCard user={user.id} userID={user.id} AllUsers={users} route={route} handlenavigate= {() => handleNavigate()} onDisconnect={() => handleDisconnect(id)} />
        ))}
         <Button title="Go to MessageScreen" onPress={() => handleNavigate()} />
      </ScrollView>
    </SafeAreaView>
  );
}

// Change- Screen for displaying Mutual connections 
function MutualsScreen(navigaiton, ) {
  
  const [loading, setLoading] = useState(false);
  const [usersData, setUsersData] = useState(null);
  const [userDetails, setUserDetails] = useState(null);
  const [showPicker, setShowPicker] = useState(false);
  const [selectedMajor, setSelectedMajor] = useState("");


  const fetchDataAndUserData = async () => {
    setLoading(true); // Set loading state to true when fetching data
    const usersData = await fetchAllUsers();
    setUsersData(usersData);
    const userData = await fetchUserData(FIREBASE_AUTH.currentUser?.uid);
    setUserDetails(userData[0]);
    setLoading(false); // Set loading state to false when data fetching is complete
  };

  const onRefresh = async () => {
    try {
      fetchDataAndUserData();  
      userDetails.true_connections = userDetails.Connections;
    } catch (error) {
      console.error('Error refreshing data:', error);
    } finally {
      setTimeout(() => {
        setLoading(false);
      }, 500);
    }
  };

  const handleConnect = async (selectedUser, selectedUserID) => {
    if (!userDetails.Connections.includes(selectedUserID)) {
    try {
      userDetails.Connections= [...userDetails?.Connections,selectedUserID];
      const documentRef = doc(firestore,'User_data', userDetails.id);
      const notifRef=doc(firestore,'Notifications',selectedUserID);
      
        
      try {
        const docSnapshot = await getDoc(notifRef);
        let Connects = [];
        if (docSnapshot.exists()) {
        // Access the 'Connects' field from the document data
          Connects = docSnapshot.data()?.Connects||[];
          console.log('Connects:', Connects);
        } else {
          console.log('Document does not exist.');
        }
        await updateDoc(documentRef, userDetails);
        await updateDoc(notifRef,{
          Connects:[...Connects,{
            id: userDetails.id,
            type: "Mutuals",
            title: userDetails.FirstName,
            image: userDetails.Profile_Image,
            user_id:selectedUserID
          }],
        })
        console.log('Document updated successfully');
      } catch (error) {
        console.error('Error updating document:', error);
      }
    } catch (error) {
      console.error('Error connecting user', error);
    }
    onRefresh();
  }};

  useEffect(() => {
    fetchDataAndUserData();  
    fetchData();
  }, []);

  let filteredArray = usersData?.filter(item => !userDetails?.true_connections.includes(item.id));

  if(selectedMajor==''){
    filteredArray=filteredArray?.filter(item=>item.Id!==FIREBASE_AUTH?.currentUser.uid);
  }
  else{
    filteredArray=filteredArray?.filter(item=>item.Major==selectedMajor);
    
  }

  const togglePicker = () => {
    setShowPicker(!showPicker);
  };

  return (
    <SafeAreaView style={styles.container}> 
      <ScrollView
      style={styles.scrollView}
      refreshControl={<RefreshControl refreshing={loading} onRefresh={onRefresh} />}>
        <TouchableOpacity onPress={togglePicker}>
          <View style={{ borderWidth: 1, borderColor: 'black', padding: 10 }}>
            <Text>{selectedMajor || 'Search by Majors'}</Text>
          </View>
        </TouchableOpacity>
          {showPicker && (
          <Picker
            selectedValue={selectedMajor}
            onValueChange={(itemValue) => {
              setSelectedMajor(itemValue);
              setShowPicker(false);
            }}
          >
            <Picker.Item label="Select Major" value="" />
            <Picker.Item label="Accounting" value="Accounting" />
            <Picker.Item label="Aerospace Engineering" value="Aerospace Engineering" />
            <Picker.Item label="Biology" value="Biology" />
            <Picker.Item label="Biomedical Engineering" value="Biomedical Engineering" />
            <Picker.Item label="Business Administration" value="Business Administration" />
            <Picker.Item label="Chemical Engineering" value="Chemical Engineering" />
            <Picker.Item label="Chemistry" value="Chemistry" />
            <Picker.Item label="Civil Engineering" value="Civil Engineering" />
            <Picker.Item label="Computer Engineering" value="Computer Engineering" />
            <Picker.Item label="Computer Science" value="Computer Science" />
            <Picker.Item label="Electrical Engineering" value="Electrical Engineering" />
            <Picker.Item label="Environmental Engineering" value="Environmental Engineering" />
            <Picker.Item label="Finance" value="Finance" />
            <Picker.Item label="Industrial Engineering" value="Industrial Engineering" />
            <Picker.Item label="Information Technology" value="Information Technology" />
            <Picker.Item label="Marketing" value="Marketing" />
            <Picker.Item label="Mechanical Engineering" value="Mechanical Engineering" />
            <Picker.Item label="Physics" value="Physics" />
            <Picker.Item label="Psychology" value="Psychology" />
            <Picker.Item label="Software Engineering" value="Software Engineering" />
          </Picker>
        )}
        {filteredArray?.map((user) => (
          <ConnectCard user = {user} handlenavigate= {() => handleNavigate()}  onConnect={() => handleConnect(user, user.id)}/>   
        ))}
      </ScrollView>
    </SafeAreaView>
  );
}

// Tabs for Current/Mutal Connections
const Tab = createMaterialTopTabNavigator();
const LatechStack = createStackNavigator(); 
function LatechStackScreen() {
  return (
    <LatechStack.Navigator screenOptions={{headerShown: false, }}>
      <LatechStack.Screen name="LatechNews" component={ConnectionsScreen} />
    </LatechStack.Navigator>
  );
}

export default function NetworkScreen({ navigation }) {
  const handleNavigateToMessageScreen = () => {
    // Navigate to the "MessageScreen"
    console.log('done');
    navigation.navigate('Message');
  };
  return (
     
    <NavigationContainer independent = {true}>
      <Tab.Navigator>
  <Tab.Screen
    name="Connected People"
    component={ConnectionsScreen}
    initialParams={{ handleNavigate: handleNavigateToMessageScreen }}
  />
  <Tab.Screen
    name="People you may know"
    component={MutualsScreen}
    initialParams={{ handleNavigate: handleNavigateToMessageScreen }}
  />
</Tab.Navigator>
    </NavigationContainer>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    paddingTop: StatusBar.currentHeight,
  },
  scrollView: {
    backgroundColor: 'white',
    marginHorizontal: 20,
  },
  text: {
    fontSize: 42,
  },
  searchInput: {
    fontSize: 18, // Slightly larger font size
    paddingVertical: 14, // Increased vertical padding
    paddingLeft: 20, // Left padding for text
    color: '#333', // Text color
  },
});
```