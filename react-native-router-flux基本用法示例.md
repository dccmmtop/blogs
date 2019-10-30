---
tags: [react-native,router]
date: 2018-08-27 09:04:05
---

```jsx
import React, {Component} from 'react';
import {Scene, Router, Stack} from 'react-native-router-flux';
import MapInfo from './components/mapInfo';
import Me from './components/me';
import CheckInRecordList from './components/checkInRecord';
import LandInfo from './components/landInfo';
import Advice from './components/advice';
import Login from './components/login';
import UpdateVersion from './components/updateVersion';
import CardStackStyleInterpolator from 'react-navigation/src/views/CardStack/CardStackStyleInterpolator';

export default class App extends Component {
  render() {
    return (
      <Router>
        <Stack
          key="root"
          transitionConfig={() => ({
            // 界面切换的方式，--水平滑出
            screenInterpolator: CardStackStyleInterpolator.forHorizontal,
          })}
          hideNavBar>
          <Scene key="mapInfo" component={MapInfo} initial />
          <Scene key="me" component={Me} />
          <Scene key="checkInRecordList" component={CheckInRecordList} />
          <Scene key="landInfo" component={LandInfo} />
          <Scene key="advice" component={Advice} />
          <Scene key="login" component={Login} />
          <Scene key="updateVersion" component={UpdateVersion} />
        </Stack>
      </Router>
    );
  }
}
```
