### [GO TO BACK](../README.md)

# 3. 처음 만드는 리액티브 네이티브 앱
> 첫 단계부터 todo앱 만들기  
> 간단한 디버깅 요령 

### 3.1 todo앱 레이아웃 작성하
http://todomvc.com/
```javascript
<View>
    <Heading />
    <Input />
    <TodoList />
    <Button />
    <TabBar />
</View>
```

### 3.2 todo앱 코드 작성하기
- npx react-native init TodoApp
##### index.js
```javascript
import React from 'react'
import { AppRegistry } from 'react-native'
import App from './app/App'
    const TodoApp = () => <App />
AppRegistry.registerComponent('TodoApp', () => TodoApp)
```
##### app/App.js
```javascript
import React, { Component } from 'react'
import { View, ScrollView, StyleSheet } from 'react-natvie'
import Heading from './Heading'

class App extends Component {
    constructor() {
        super()
        this.state = {
            inputValue: '',
            todos: [],
            type: 'All'
        }
    }
    render() {
        return (
            <View style={styles.container}>
                <ScrollView keyboardShouldPersisTaps='always' style={styls.content}>
                    <Heading />
                </ScrollView>
            </View>
        )
    }
    const styles = StyleSheet.create({
        container: {
            flex: 1,
            backgroundColor: '#f5f5f5'
        },
        content: {
            flex: 1,
            paddingTop: 60
        }
    })
    export default App
}
```
- keyboardShouldPersisTaps: 키보드가 열려있으면 닫아서 UI가 onPress 이벤트를 모두 처리
##### app/Heading.js
```javascript
import React from 'react'
import { View, Text, StyleSheet } from 'react-native'

const Heading = () => (
    <View style={styles.header}>
        <Text styles.headerText>
            todos
        </Text>
    </View>
)
const styles = StyleSheet.create({
    header: {
        marginTop: 80
    },
    headerText: {
        textAlign: 'center',
        fontSize: 72,
        color: 'rgba(175, 47, 47, 0.25)',
        fontWeight: '100'
    }
})
export default Heading
```
##### app/Input.js
```javascript
import React from 'react'
import { View, TextInput, StyleSheet } from 'react-native'
const Input = () => (
    <View style={styles.inputContainer}>
        <TextInput 
            style={styles.input}
            placeholder='What needs to be done?'
            placeholderTextColor='#CACACA'
            selectionColor='#666666' />
    </View>
)
const styles = StyleSheet.create({
    inputContainer: {
        marginLeft: 20,
        marginRight: 20,
        shadowOpacity: 0.2,
        shadowRadius: 3,
        shadowColor: '#000000',
        shadowOffset: { width: 2, height: 2 }
    },
    input: {
        height: 60,
        backgroundColor: '#ffffff',
        paddingLeft: 10,
        paddingRight: 10
    }
    export default Input
})
```
