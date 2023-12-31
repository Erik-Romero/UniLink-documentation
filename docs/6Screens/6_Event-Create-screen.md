---
sidebar_position: 6
---

# Event Create Details

The `CreateEventScreen` will be responsible for displaying a screen that allows users to create events.

## Dependencies

This component uses the following external libraries:

- `react`: The core React library for building user interfaces.
- `react-native`: A library for building mobile applications using React Native.
- `react-native-paper`: A library that provides components and theming for React Native apps.

```jsx
import React, { useState, useEffect } from 'react';
import { View, Text, StyleSheet, ScrollView, TouchableOpacity } from 'react-native';
import { TextInput, Button } from 'react-native-paper';
import HandleUserEvents from '../../../src/firebase_init/handleUserEvents';
import CalendarPicker from 'react-native-calendar-picker';
import { FIREBASE_AUTH } from '../../../src/firebase_init/firebase';
import * as ImagePicker from 'expo-image-picker';
import { CircularImage } from '../../Components/CircleImage';
import { getStorage, ref, uploadBytes, getDownloadURL } from "firebase/storage";
import { Picker } from '@react-native-picker/picker';
```
## Props

The `CreateEventScreen` component accepts the following props.

- `navigation` This allows the screen to inherent the parents navigation object.
- `Route` This allows the screen to find variables from a previous screen.

## State

The component has the following state:

- `users` (array): Stores the fetched event data.
- `refreshing` (boolean): Represents the refreshing status of the ScrollView.

```jsx

const storage = getStorage(); 

export default function CreateEventScreen({ navigation, route }) {
  const { onRefresh } = route.params;
  const [selectedValue, setSelectedValue] = useState(null);
  const [showPicker, setShowPicker] = useState(false);
  const [title, setTitle] = useState("");
  const [desc, setDesc] = useState("");
  const [sponser, setSponser] = useState("");
  const [date, setDate] = useState("");
  const [selectedImage, setSelectedImage] = useState(null);
  const [selectedStartDate, setSelectedStartDate] = useState(null);
  const [Image_link, setImage_link] = useState("");
  const [uploading, setUploading] = useState(false);
```

## Functions

### togglePicker
 This function is in charge of rendering and unrendering the picker.

 ```jsx 
  const togglePicker = () => {
    setShowPicker(!showPicker);
  };
 ```
### onDateChange
 When the date is chnaged on the calender this is what updates the object accordingly 
 ```jsx
 const onDateChange = (date) => {
    setSelectedStartDate(date);
    const datestring = (date ? date.toString() : '');
    const index = datestring.indexOf('2023') + 4;
    setDate(datestring.substring(0, index));
  };
 ```
### isFormValid
 When you press the enter button it verifies to see if everything was inputed correctly.
```jsx
const isFormValid = () => {
    return title.trim() !== "" && desc.trim() !== "" && sponser.trim() !== "" && date.trim() !== "";
  };
```
### handleEnter
 The handleEnter button is what is in charge of uploading the data to firebase. 
 ```jsx
  const handleEnter = async () => {
    if (!isFormValid()) {
      alert("Please fill out all fields before entering the event.");
      return;
    }

    const storageRef = ref(storage, `images/${FIREBASE_AUTH.currentUser?.uid}/${Date.now()}.jpg`);
    const response = await fetch(selectedImage);
    const blob = await response.blob();

    try {
      const snapshot = await uploadBytes(storageRef, blob);
      const downloadURL = await getDownloadURL(snapshot.ref);
      setImage_link(downloadURL);
      console.log('Download URL:', downloadURL);

      const createdBy = FIREBASE_AUTH.currentUser?.uid;
      HandleUserEvents(title, desc, sponser, date, selectedValue, createdBy, downloadURL);
      setTitle("");
      setDesc("");
      setSponser("");
      setDate("");
      setSelectedValue(null);
      navigation.goBack();

      if (onRefresh) {
        onRefresh();
      }

      setUploading(false);
    } catch (error) {
      console.error("Error uploading image:", error);
      setUploading(false);
    }
  };
 ```
### handlePicture
 The handle Picture is called within handleEnter to upload the image async from the function.

```jsx
const handlePicture = async () => {
    let _image = await ImagePicker.launchImageLibraryAsync({
      mediaTypes: ImagePicker.MediaTypeOptions.Images,
      allowsEditing: true,
      aspect: [4, 3],
      quality: 1,
    });

    if (!_image.cancelled) {
      setUploading(true);
      setSelectedImage(_image.uri);
    }
  };
```


## Styles

The component uses the `StyleSheet` object from `react-native` to define styles for the container, scrollView, card, and text. The design is set to display the cards in a horizontal layout with a maximum height of 400. Each card has a width of 300, a height of 400, and a margin of 4 for spacing.

Note: This documentation provides an overview of the `CreateEventScreen` component and its functionalities. For a comprehensive understanding and code implementation details, it is recommended to refer to the actual code.




## Code

```jsx
import React, { useState, useEffect } from 'react';
import { View, Text, StyleSheet, ScrollView, TouchableOpacity } from 'react-native';
import { TextInput, Button } from 'react-native-paper';
import HandleUserEvents from '../../../src/firebase_init/handleUserEvents';
import CalendarPicker from 'react-native-calendar-picker';
import { FIREBASE_AUTH } from '../../../src/firebase_init/firebase';
import * as ImagePicker from 'expo-image-picker';
import { CircularImage } from '../../Components/CircleImage';
import { getStorage, ref, uploadBytes, getDownloadURL } from "firebase/storage";
import { Picker } from '@react-native-picker/picker';

const storage = getStorage(); 

export default function CreateEventScreen({ navigation, route }) {
  const { onRefresh } = route.params;
  const [selectedValue, setSelectedValue] = useState(null);
  const [showPicker, setShowPicker] = useState(false);
  const [title, setTitle] = useState("");
  const [desc, setDesc] = useState("");
  const [sponser, setSponser] = useState("");
  const [date, setDate] = useState("");
  const [selectedImage, setSelectedImage] = useState(null);
  const [selectedStartDate, setSelectedStartDate] = useState(null);
  const [Image_link, setImage_link] = useState("");
  const [uploading, setUploading] = useState(false);

  const togglePicker = () => {
    setShowPicker(!showPicker);
  };

  const onDateChange = (date) => {
    setSelectedStartDate(date);
    const datestring = (date ? date.toString() : '');
    const index = datestring.indexOf('2023') + 4;
    setDate(datestring.substring(0, index));
  };

  const isFormValid = () => {
    return title.trim() !== "" && desc.trim() !== "" && sponser.trim() !== "" && date.trim() !== "";
  };

  const handleEnter = async () => {
    if (!isFormValid()) {
      alert("Please fill out all fields before entering the event.");
      return;
    }

    const storageRef = ref(storage, `images/${FIREBASE_AUTH.currentUser?.uid}/${Date.now()}.jpg`);
    const response = await fetch(selectedImage);
    const blob = await response.blob();

    try {
      const snapshot = await uploadBytes(storageRef, blob);
      const downloadURL = await getDownloadURL(snapshot.ref);
      setImage_link(downloadURL);
      console.log('Download URL:', downloadURL);

      const createdBy = FIREBASE_AUTH.currentUser?.uid;
      HandleUserEvents(title, desc, sponser, date, selectedValue, createdBy, downloadURL);
      setTitle("");
      setDesc("");
      setSponser("");
      setDate("");
      setSelectedValue(null);
      navigation.goBack();

      if (onRefresh) {
        onRefresh();
      }

      setUploading(false);
    } catch (error) {
      console.error("Error uploading image:", error);
      setUploading(false);
    }
  };

  const handlePicture = async () => {
    let _image = await ImagePicker.launchImageLibraryAsync({
      mediaTypes: ImagePicker.MediaTypeOptions.Images,
      allowsEditing: true,
      aspect: [4, 3],
      quality: 1,
    });

    if (!_image.cancelled) {
      setUploading(true);
      setSelectedImage(_image.uri);
    }
  };

  return (
    <ScrollView style={styles.scrollView}>
      <View>
        <Text>Enter Details for your event.</Text>
        <TextInput label="Title" value={title} onChangeText={setTitle} />
        <TextInput label="Description" value={desc} onChangeText={setDesc} />
        <TextInput label="Sponsor" value={sponser} onChangeText={setSponser} />

        <CalendarPicker onDateChange={onDateChange} selectedDayColor="#69B3E7" todayBackgroundColor="#FFFFFF" allowRangeSelection={false} />
        <View style={styles.textStyle}>
          <Text style={styles.textStyle}> Selected Start Date :</Text>
          <CircularImage imageUrl={selectedImage} />
        </View>

        <Text>Select Location:</Text>
        <TouchableOpacity onPress={togglePicker}>
          <View style={{ borderWidth: 1, borderColor: 'black', padding: 10 }}>
            <Text>{selectedValue || 'Select a location'}</Text>
          </View>
        </TouchableOpacity>

        {showPicker && (
          <Picker
            selectedValue={selectedValue}
            onValueChange={(itemValue, itemIndex) => {
              setSelectedValue(itemValue);
              setShowPicker(false);
            }}
          >
            <Picker.Item label="Barnes & Noble Bookstore" value="Barnes & Noble Bookstore" />
            <Picker.Item label="Daniel D. Reneau Biomedical  Engineering Building" value="Daniel D. Reneau Biomedical  Engineering Building" />
            <Picker.Item label=" Lambright Sports & Wellness Center" value=" Lambright Sports & Wellness Center" />
            <Picker.Item label="Integrated Engineering and Science Building (IESB)" value="Integrated Engineering and Science Building (IESB)" />
            <Picker.Item label="College of Business" value="College of Business" />
            <Picker.Item label="Carson-Taylor Hall (Human Ecology & Science)" value="Carson-Taylor Hall (Human Ecology & Science)" />
            <Picker.Item label="Tolliver Hall/Post Office" value="Tolliver Hall/Post Office" />
          </Picker>
        )}

        <Button onPress={handlePicture}>Picture</Button>
        <Button onPress={handleEnter}>Enter</Button>
        <Button onPress={() => {
          navigation.goBack();
          setTitle("");
          setDesc("");
          setSponser("");
          setDate("");
        }}>
          Dismiss
        </Button>
      </View>
    </ScrollView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#ffffff',
  },
  textStyle: {
    marginTop: 10,
  },
  titleStyle: {
    textAlign: 'center',
    fontSize: 20,
    margin: 20,
  },
  scrollView: {
    backgroundColor: 'white',
    marginHorizontal: 10,
  },
  buttonView: {
    padding: 8,
    paddingHorizontal: 8,
    justifyContent: 'space-between',
  },
});

```