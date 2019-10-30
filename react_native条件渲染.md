---
tags: [react-native]
---

根据不同的条件渲染不同的组件
一般的套路是把将要渲染的组件赋值给一个`const`，然后根据不同的条件使用不同的 const

```jsx
 render() {
 const emptyImage = (
  <View
    style={{
      flex: 1,
      justifyContent: "center",
      alignItems: "center"
    }}
  >
    <Image source={require("../icons/empty.png")} style={styles.emptyImg} />
    <Text style={styles.text}>你还没有任何土地</Text>
  </View>
);
const List= <FlatList.../>
    return (
    <View style={styles.container}>
        {/* 导航，返回按钮*/}
        <View style={[styles.back]}>
          <TouchableOpacity
            onPress={() => {
              Actions.pop();
            }}
            style={styles.backBtn}
          >
            <Image
              style={styles.backImg}
              source={require("../icons/back.png")}
            />
          </TouchableOpacity>
          <Text style={[styles.text, styles.title]}>我的地块</Text>
        </View>
        {this.props.list ? List : emptyImage}
      </View>
    );
  }
}
```
