#### 模态框是父容器的一个组件，关闭和显示应该由父容器决定

### 父容器

mapInfo.js

```js
import BaseModal from './baseModal';
  constructor(props) {
    super(props);
    this.state = {
      isDialogVisible: false,
      setAlert: {
        // 向弹出框传递 签到成功或失败的信息
        alertInfo: '',
        // 签到是否成功
        isSuccess: true
      }
    };
  }
  showDialog() {
    this.setState({ isDialogVisible: true });
  }

  hideDialog() {
    this.setState({ isDialogVisible: false });
  }

  // 视图部分
  <View style={[styles.viewLocation, styles.flex]}>
    <TouchableOpacity
      onPress={() => {
        this.showDialog();
          //改变模态对话框内容
          this.setState({ setAlert: { isSuccess: true, alertInfo: '签到成功 积分 +' + addScore } });
        }).catch(error => {
          this.setState({ setAlert: { isSuccess: false, alertInfo: '签到失败' + "\n" + error } })
        })
      }}>
      <BaseModal
        hide={() => { this.hideDialog() }}
        modalVisible={this.state.isDialogVisible}
        isSuccess={this.state.setAlert.isSuccess}
        info={this.state.setAlert.alertInfo}
      />
    </TouchableOpacity>
  </View>
```

### 模态框部分

```js
import React, {Component} from 'react';
import {
  Modal,
  Text,
  TouchableOpacity,
  View,
  StyleSheet,
  Image,
} from 'react-native';

let Dimensions = require('Dimensions');
let SCREEN_WIDTH = Dimensions.get('window').width; //宽
let SCREEN_HEIGHT = Dimensions.get('window').height; //高

export default class ModalDialog extends Component {
  // 构造
  constructor(props) {
    super(props);
    this.props.modalVisible = false;
    // 此属性是一个方法
    this.props.hide = () => {};
    this.props.info = '';
    this.props.isSuccess = true;
  }

  render() {
    return (
      <Modal
        visible={this.props.modalVisible}
        transparent={true}
        onRequestClose={() => {}} //如果是Android设备 必须有此方法
      >
        <View style={styles.bg}>
          <View style={styles.dialog}>
            {/* onPress事件直接与父组件传递进来的属性挂接 */}
            <TouchableOpacity
              style={styles.dialogBtnViewItem}
              onPress={this.props.hide}>
              <View style={{alignItems: 'center'}}>
                <Image
                  style={[styles.icon, {marginBottom: 20}]}
                  source={
                    this.props.isSuccess
                      ? require('../icons/success.png')
                      : require('../icons/fail.png')
                  }
                />
                <Text>{this.props.info}</Text>
              </View>
            </TouchableOpacity>
          </View>
        </View>
      </Modal>
    );
  }
}

const styles = StyleSheet.create({
  //全屏显示 半透明 可以看到之前的控件但是不能操作了
  bg: {
    backgroundColor: 'rgba(52,52,52,0.5)', //rgba  a0-1  其余都是16进制数
    justifyContent: 'center',
    alignItems: 'center',
    flex: 1,
  },
  dialog: {
    width: SCREEN_WIDTH * 0.65,
    height: SCREEN_HEIGHT * 0.2,
    backgroundColor: 'white',
    justifyContent: 'center',
    alignContent: 'center',
    borderRadius: 4,
  },
  dialogBtnViewItem: {
    flex: 1,
    backgroundColor: 'white',
    borderRadius: 4,
    justifyContent: 'center',
    alignItems: 'center',
  },
  icon: {
    width: 50,
    height: 50,
  },
});
```
