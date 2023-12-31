---
sidebar_position: 5
---

# Event Details

The `Event Details` will be responsible for displaying the event details for every event. 

## Dependencies

This component uses the following external libraries:

- `react`: The core React library for building user interfaces.
- `react-native`: A library for building mobile applications using React Native.
- `react-native-paper`: A library that provides components and theming for React Native apps.

```jsx
import React,{useState} from 'react';
import { View, Text, StyleSheet, Image, ScrollView, Dimensions,TextInput,KeyboardAvoidingView,TouchableOpacity,Linking } from 'react-native';
import { useRoute } from '@react-navigation/native';
import { Button } from 'react-native-paper';
import { CircularImage } from '../../Components/CircleImage';
import { accentColors, primaryColors } from '../../Components/Colors';
import RedLine from './Components/RedLine';
import { FIREBASE_AUTH } from '../../../src/firebase_init/firebase';
import CommentSection from '../../Components/CommentSection';
```

## Props

The `EventScreenDetails` component does not accept any props.

## State

The component has the following state:

- `editMode` (array): Stores the fetched event data.
- `editedTitle` (boolean): Represents the status of editable title.
- `editedDate` boolean:Represents the status of editable date.
- `editedLocation` boolean:Represents the status of editable Location.
- `editedDescription` boolean:Represents the status of editable description.

The following are the names and google map links associated with each location. 
```jsx
const locationUrls = {
  "Barnes & Noble Bookstore": "https://maps.app.goo.gl/PDGzFzzMJV7bKwdt5",
  "Bogard Hall (Engineering)": "https://maps.app.goo.gl/wLQkNYtuhjJV6muM8",
  "Lambright Sports & Wellness Center": "https://maps.app.goo.gl/Au3HGL1Lnqt9EZVx7",
  "Integrated Engineering and Science Building (IESB)": "https://maps.app.goo.gl/4ywvr9sKaVsTPma36",
  "College of Business": "https://maps.app.goo.gl/X9SvkghA1AJkwfLp6",
  "Tolliver Hall/Post Office": "https://maps.app.goo.gl/5aRS8NqXFWAMz5nW8",
  "Carson-Taylor Hall (Human Ecology & Science)": "https://maps.app.goo.gl/Pf7vTWnyYv3XYtn96",
};
```

```jsx
const EventDetailsScreen = () => {
  const route = useRoute();
  const { event } = route.params;

  const [editMode, setEditMode] = useState(false);
  const [editedTitle, setEditedTitle] = useState(event.Title);
  const [editedDate, setEditedDate] = useState(event.Date);
  const [editedLocation, setEditedLocation] = useState(event.Location);
  const [editedDescription, setEditedDescription] = useState(event.Description);

  const screenHeight = Dimensions.get('window').height;
  const viewHeightPercentage = 30;
  const viewHeight = (screenHeight * viewHeightPercentage) / 100;

  const User_ID = FIREBASE_AUTH.currentUser?.uid;
```

## Functions

`

### handleEditButton
The following sets the edit Mode to true. If and only if the creator matches the id.
```jsx
const handleEditButton = () => {
    if (event.Creator === User_ID) {
      setEditMode(true);
    }
  };
```
### handleSaveEvent
When the document is saved it updated on the firebase. This is the function that should be called.
```jsx
const handleSaveEvent = async (eventId) => {
    try {
      const eventRef = doc(firestore, 'Event_data', eventId); // Construct the reference to the event document in "Event_data" collection
      await updateDoc(eventRef, {                             // Update the document in Firebase with the edited data
        Title: editedTitle,
        Description: editedDescription,
      });
      setEditMode(false);                                     // Exit edit mode
      alert('Event edited successfully.');
    } catch (error) {console.error('Error editing event:', error);}};

```
### handleSaveButton
This handles the save button implementaion. When the save button is pushed it will set the editMode to false.
```jsx
const handleSaveButton = () => {
    // Implement the logic to update the event data in your Firebase or state management here
    setEditMode(false);
    // Display a success message or navigate back to the previous screen
  };
```
### openGoogleMaps
The following will read the data that the object is linked with and open the correct link.
```jsx
const openGoogleMaps = (location) => {
    const url = locationUrls[location];
    if (url) {
      Linking.openURL(url)
        .then((data) => {
          console.log('Google Maps opened:', data);
        })
        .catch((error) => {
          console.error('Error opening Google Maps:', error);
        });
    } else {
      console.error('No URL found for the given location:', location);
    }
  };

```
## Styles

The component uses the `StyleSheet` object from `react-native` to define styles for the container, scrollView, card, and text. The design is set to display the cards in a horizontal layout with a maximum height of 400. Each card has a width of 300, a height of 400, and a margin of 4 for spacing.


```jsx
const styles = StyleSheet.create({
  container: {
    flexGrow: 1,
    paddingBottom: 40,
  },
  blueHeader:{
    backgroundColor:primaryColors.blue,
    flexDirection: 'row',
    alignItems: 'center',
  },
  textContainer:{
    alignItems: 'center',
    flex:1,
  },
  eventImage: {
    width: '100%',
    height: 200,
    resizeMode: 'cover',
  },
  eventTitle: {
    fontSize: 24,
    fontWeight: 'bold',
    marginTop: 20,
  },
  eventDate: {
    fontSize: 16,
    marginTop: 5,
  },
  eventLocation: {
    fontSize: 16,
    marginTop: 5,
    color: '#888',
  },
  eventDescription: {
    fontSize: 18,
    marginTop: 20,
    textAlign: 'left',
    paddingHorizontal: 20,
  },
  blueHeadertext:{
    color:'#69B3E7',
    fontSize:20,
    flexWrap:'wrap'
  },
  sponsertext:{
    color:accentColors.lightblue,
    fontSize:20,
    flex:1,
    flexWrap:'wrap'
   
  },
  sponsorContainer: {
    flexDirection:'row',
    paddingLeft:50,
  },
  input: {
    marginVertical: 4,
    height: 50,
    borderWidth: 1,
    borderRadius: 4,
    padding: 10,
    backgroundColor: "#fff",
  },
  input_multiline: {
    marginVertical: 10,
    borderWidth: 1,
    borderRadius: 4,
    padding: 10,
    backgroundColor: "#fff",
    height: 200, // Adjust the height to your desired value
    textAlignVertical: 'top', // Align the text at the top of the input
  },
  
});
```



## Code

```jsx
import React,{useState} from 'react';
import { View, Text, StyleSheet, Image, ScrollView, Dimensions,TextInput,KeyboardAvoidingView,TouchableOpacity,Linking } from 'react-native';
import { useRoute } from '@react-navigation/native';
import { Button } from 'react-native-paper';
import { CircularImage } from '../../Components/CircleImage';
import { accentColors, primaryColors } from '../../Components/Colors';
import RedLine from './Components/RedLine';
import { FIREBASE_AUTH } from '../../../src/firebase_init/firebase';

const locationUrls = {
  "Barnes & Noble Bookstore": "https://maps.app.goo.gl/PDGzFzzMJV7bKwdt5",
  "Bogard Hall (Engineering)": "https://maps.app.goo.gl/wLQkNYtuhjJV6muM8",
  "Lambright Sports & Wellness Center": "https://maps.app.goo.gl/Au3HGL1Lnqt9EZVx7",
  "Integrated Engineering and Science Building (IESB)": "https://maps.app.goo.gl/4ywvr9sKaVsTPma36",
  "College of Business": "https://maps.app.goo.gl/X9SvkghA1AJkwfLp6",
  "Tolliver Hall/Post Office": "https://maps.app.goo.gl/5aRS8NqXFWAMz5nW8",
  "Carson-Taylor Hall (Human Ecology & Science)": "https://maps.app.goo.gl/Pf7vTWnyYv3XYtn96",
};

const EventDetailsScreen = () => {
  const route = useRoute();
  const { event } = route.params;

  const [editMode, setEditMode] = useState(false);
  const [editedTitle, setEditedTitle] = useState(event.Title);
  const [editedDate, setEditedDate] = useState(event.Date);
  const [editedLocation, setEditedLocation] = useState(event.Location);
  const [editedDescription, setEditedDescription] = useState(event.Description);

  const screenHeight = Dimensions.get('window').height;
  const viewHeightPercentage = 30;
  const viewHeight = (screenHeight * viewHeightPercentage) / 100;

  const User_ID = FIREBASE_AUTH.currentUser?.uid;

  const handleEditButton = () => {
    if (event.Creator === User_ID) {
      setEditMode(true);
    }
  };
  const handleSaveEvent = async (eventId) => {
    try {
      const eventRef = doc(firestore, 'Event_data', eventId); // Construct the reference to the event document in "Event_data" collection
      await updateDoc(eventRef, {                             // Update the document in Firebase with the edited data
        Title: editedTitle,
        Description: editedDescription,
      });
      setEditMode(false);                                     // Exit edit mode
      alert('Event edited successfully.');
    } catch (error) {console.error('Error editing event:', error);}};

  const handleSaveButton = () => {
    // Implement the logic to update the event data in your Firebase or state management here
    setEditMode(false);
    // Display a success message or navigate back to the previous screen
  };

  const openGoogleMaps = (location) => {
    const url = locationUrls[location];
    if (url) {
      Linking.openURL(url)
        .then((data) => {
          console.log('Google Maps opened:', data);
        })
        .catch((error) => {
          console.error('Error opening Google Maps:', error);
        });
    } else {
      console.error('No URL found for the given location:', location);
    }
  };

  return (
    <KeyboardAvoidingView style={{flex:1,}} behavior="padding" enabled>
    <ScrollView contentContainerStyle={styles.container}>
      <View style={[styles.blueHeader, { height: viewHeight }]}>
        <CircularImage imageUrl={event.Image_Link} />
        <View style={styles.textContainer}>
          <Text style={styles.blueHeadertext}>{event.Title}</Text>
          <Text style={styles.blueHeadertext}>{event.Date}</Text>
          <View style={styles.sponsorContainer}>
            <Text style={styles.sponsertext}>{event.Sponser}</Text>
          </View>
        </View>
      </View>
      <RedLine />
      {editMode ? (
        <React.Fragment>
          <TextInput
            label="Title"
            value={editedTitle}
            onChangeText={setEditedTitle}
            style={styles.input}
          />
          <TextInput
            label="Date"
            value={editedDate}
            onChangeText={setEditedDate}
            style={styles.input}
          />
          <TextInput
            label="Location"
            value={editedLocation}
            onChangeText={setEditedLocation}
            style={styles.input}
          />
          <TextInput
            label="Description"
            value={editedDescription}
            onChangeText={setEditedDescription}
            style={styles.input_multiline} // Use your custom multiline input style
            multiline // Set multiline to true
            numberOfLines={20} // You can adjust this value as needed
          />
          <Button onPress={handleSaveButton}>Save</Button>
        </React.Fragment>
      ) : (
        <React.Fragment>
          <Text style={styles.eventTitle}>{event.Title}</Text>
          <Text style={styles.eventDate}>{event.Date}</Text>
          <TouchableOpacity onPress={() => openGoogleMaps(event.Location)}>
            <Text style={styles.eventLocation}>{event.Location}</Text>
          </TouchableOpacity>
          <Text style={styles.eventDescription}>{event.Description}</Text>
          {event.Creator === User_ID && (
            <Button onPress={handleEditButton}>Edit</Button>
          )}
        </React.Fragment>
      )}
    </ScrollView>
    </KeyboardAvoidingView>
  );
};

const styles = StyleSheet.create({
  container: {
    flexGrow: 1,
    paddingBottom: 40,
  },
  blueHeader:{
    backgroundColor:primaryColors.blue,
    flexDirection: 'row',
    alignItems: 'center',
  },
  textContainer:{
    alignItems: 'center',
    flex:1,
  },
  eventImage: {
    width: '100%',
    height: 200,
    resizeMode: 'cover',
  },
  eventTitle: {
    fontSize: 24,
    fontWeight: 'bold',
    marginTop: 20,
  },
  eventDate: {
    fontSize: 16,
    marginTop: 5,
  },
  eventLocation: {
    fontSize: 16,
    marginTop: 5,
    color: '#888',
  },
  eventDescription: {
    fontSize: 18,
    marginTop: 20,
    textAlign: 'left',
    paddingHorizontal: 20,
  },
  blueHeadertext:{
    color:'#69B3E7',
    fontSize:20,
    flexWrap:'wrap'
  },
  sponsertext:{
    color:accentColors.lightblue,
    fontSize:20,
    flex:1,
    flexWrap:'wrap'
   
  },
  sponsorContainer: {
    flexDirection:'row',
    paddingLeft:50,
  },
  input: {
    marginVertical: 4,
    height: 50,
    borderWidth: 1,
    borderRadius: 4,
    padding: 10,
    backgroundColor: "#fff",
  },
  input_multiline: {
    marginVertical: 10,
    borderWidth: 1,
    borderRadius: 4,
    padding: 10,
    backgroundColor: "#fff",
    height: 200, // Adjust the height to your desired value
    textAlignVertical: 'top', // Align the text at the top of the input
  },
  
});

export default EventDetailsScreen;

```
