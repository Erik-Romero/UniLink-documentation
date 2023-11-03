---
sidebar_position: 5
---

# Group Stack

After several months of creating stack navigators, the logic in them has become a lot more simple. In this page the only thing populating it is the navigator, no constants besides the navigation object. The imports are the screens the navigator goes towards.
```jsx
import { createStackNavigator } from "@react-navigation/stack";
import GroupDetailsScreen from "./GroupDetails";
import GroupsScreen from "./GroupScreen";
import GroupUsers from "./GroupUsers";
import { GroupSchedule } from "./GroupSchedule";
import GroupCreationScreen from "./GroupCreationScreen";

const groupStack = createStackNavigator();
export const GroupStack = () => (
  <groupStack.Navigator >
    <groupStack.Screen name={'GroupsScreen'} component={GroupsScreen} options={{headerShown: false}} />
    <groupStack.Screen name={'GroupDetailsScreen'} component={GroupDetailsScreen} options={{headerShown: false}}/>
    <groupStack.Screen name={'GroupUsers'} component={GroupUsers} options={{headerShown: false}}/>
    <groupStack.Screen name={'GroupSchedule'} component={GroupSchedule} options={{headerShown: false}}/>
    <groupStack.Screen name={'GroupCreation'} component={GroupCreationScreen} options={{headerShown: false}}/>
  </groupStack.Navigator>  
);
```

