---
sidebar_position: 1
---

# App
 
The App.js file is the inital entry point to the application. Therefore all the file App.js will do is import MainStack and Mainstack will then have all of our app's functionality. 

We make this distinction to have a layer difference between our entry point and our main logic for the application.

```jsx
import MainStack from './Navigation/MainStack';
function App() {
  return <MainStack />;
}
export default App;
```