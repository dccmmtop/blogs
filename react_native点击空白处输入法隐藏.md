### 当有输入框获取到焦点时，输入法便会显示，否则输入法隐藏

可以用`TouchWithoutFeedback`作为最外层的容器，并添加一个点击时间处理，让所有的输入控件失去焦点，这样当点击空白处输入法就会隐藏。
父容器和子组件都有点击事件时，会执行子组件的点击事件，父容器的点击事件不会被触发。

### demo

```jsx
hideInputBox = () => {
    this.refs.textInput1.blur();
    this.refs.textInput2.blur();
  };

  render() {
    return (
      <TouchableWithoutFeedback
        style={styles.container}
        onPress={() => {
          this.hideInputBox();
        }}
      >
        <View style={styles.container}>
          <View style={styles.inputGroup}>

            <TextInput
              ref="textInput1"
            />
          </View>
          <View style={[styles.inputGroup]}>
            <TextInput
              ref="textInput2"
            />
          </View>
        </View>
      </TouchableWithoutFeedback>
    );
    const styles = StyleSheet.create({
  container: {
    justifyContent: "center",
    flex: 1
  },
    })
```
