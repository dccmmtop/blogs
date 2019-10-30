---
tags: [android]
date: 2019-02-25 09:24:49
---

https://github.com/aksonov/react-native-router-flux/issues/2487

```jsx
import React, { Component } from "react";
import { Scene, Router, Stack , Actions} from "react-native-router-flux";
import Add from "./components/new";
import { BackHandler } from "react-native";
import Show from "./components/show";
import Edit from "./components/edit";
export default class App extends Component {
  onBackPress() {
    console.log(Actions.currentScene)
    if (Actions.currentScene == 'show') {
      BackHandler.exitApp();
    }
    Actions.pop()
    return true
  }
  render() {
    return (
      <Router  backAndroidHandler={this.onBackPress} >
        <Stack key="root" hideNavBar >
          <Scene key="show" component={Show}  />
          <Scene key="add" component={Add}    />
          <Scene key="edit" component={Edit}    />
        </Stack>
      </Router>
    );
  }
}
```

