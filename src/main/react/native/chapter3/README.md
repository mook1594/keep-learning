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
import Input from './Input'
import Button from './Button'
import Todolist from './TodoList'
import TabBar from './TabBar'
let todoIndex = 0
class App extends Component {
    constructor() {
        super()
        this.state = {
            inputValue: '',
            todos: [],
            type: 'All'
        }
        this.submitTodo = this.submitTodo.bind(this)
        this.toggleComplete = this.toggleComplete.bind(this)
        this.deleteTodo = this.deleteTodo.bind(this)
        this.setType = this.setType.bind(this)
    }
    inputChange(inputValue) {
        console.log('Input Value: ', inputValue)
        this.setState({ inputValue })
    }
    submitTodo() {
        if(this.state.inputValue.match(/^\s*$/)) { // 공백이변 반환
            return
        }
        const todo = {
            title: this.state.inputValue,
            todoIndex,
            complete: false
        }
        todoIndex++
        const todos = [...this.state,todos, todo]
        this.setState({ todos, inputValue: '' }, () => {
            console.log('State: ', this.state)
        })
    }
    deleteTodo(todoIndex) {
        let { todos } = this.state
        todos = todos.filter((todo) => todo.todoIndex !== todoIndex)
        this.setState({ todos })
    }
    toggleComplete(todoIndex) {
        let todos = this.state.todos
        todos.forEach((todo) => {
            if(todo.todoIndex === todoIndex) {
                todo.complete = !todo.complete
            }
        })
        this.setState({ todos })
    }
    setType(type) {
        this.setState({ type })
    }
    render() {
        const { inputValue, todos, type } = this.state
        return (
            <View style={styles.container}>
                <ScrollView keyboardShouldPersisTaps='always' style={styles.content}>
                    <Heading />
                    <Input inputValue={inputValue}
                        inputChange={(text) => this.inputChange(text)} />
                    <TodoList 
                        toggleComplete={this.toggleComplete}
                        deleteTodo={this.deleteTodo}
                        todos={todos} />
                    <Button submitTodo={this.submitTodo} />
                </ScrollView>
                <TabBar type={type} setType={this.setType} />
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
const Input = ({ inputValue, inputChange }) => (
    <View style={styles.inputContainer}>
        <TextInput 
            value= = {inputValue}
            style={styles.input}
            placeholder='What needs to be done?'
            placeholderTextColor='#CACACA'
            selectionColor='#666666'
            onChangeText={inputChange} />
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
##### app/Button.js
```javascript
import React from 'react'
import { View, Text, StyleSheet, TouchableHighlight } from 'react-native'

const Button = ({ submitTodo }) => (
    <View style={styles.buttonCoontainer}>
        <TouchableHighlight underlayColor='#efefef'
            style={styles.button} onPress={submitTodo}>
            <Text style={styles.submit}>
                Submit
            </Text>
        </TouchableHighlight>
    </View>
)
const styles = StyleSheet.create({
    buttonContainer: {
        alignItems: 'flex-end'
    },
    button: {
        height: 50,
        paddingLeft: 20,
        paddingRight: 20,
        backroundColor: '#fffffff',
        width: 200,
        marginRight: 20,
        borderWidth: 1,
        borderColor: 'rgba(0,0,0,.1)',
        justifyContent: 'center',
        alignItems: 'center'
    },
    submit: {
        color: '#666666',
        fontWeight: '600'
    }
})
export default Button
```
- TouchableHighlight 버튼
##### app/Todo.js
```javascript
import React from 'react'
import { View, Text, StyleSheet } from 'react-native'
import TodoButton from './TodoButton'
const Todo = ({ todo, toggleComplete, deleteTodo }) => (
    <View style={styles.todoContainer}>
        <Text style={styles.todoText}>
            {todo.title}
        </Text>
        <View style={styles.buttons}>
            <TodoButton name='Done' complete={todo.complete}
                onPress={() => toggleComplete(todo.todoIndex) />
            <TodoButton name='Delete'
                onPress={() => deleteTodo(todo.todoIndex)} />
        </View>
    </View>
)
const styles = StyleSheet.create({
    todoContainer: {
        marginLeft: 20,
        marginRight: 20,
        backgroundColor: '#ffffff'
        borderTopWidth: 1,
        borderRightWidth: 1, 
        borderLeftWidth: 1,
        borderColor: '#ededed',
        paddingLeft: 14,
        paddingTop: 7,
        paddingBottom: 7,
        shadowOpacity: 0.2,
        shadowRadius: 3,
        shadowColor: '#000000',
        shadowOffset: { width: 2, height: 2 },
        flexDirection: 'row',
        alignItems: 'center'
    },
    todoText: {
        fontSize: 17
    },
    buttons: {
        flex: 1,
        flexDirection: 'row',
        justifyContent: 'flex-end',
        alignItems: 'center'
    }
})
export default Todo
```
##### app/TodoList.js
```javascript
import React from 'react'
import { View } from 'react-native'
import Todo from './Todo'
const TodoList = ({ toodos, deleteTodo, toggleComplete, type }) => {
    const getVisibletodos = (todos, type) => {
        switch(type) {
            case 'All':
                return todos
            case 'Complete':
                return todos.filter((t) => t.complete)
            case 'Active':
                return todos.filter((t) => !t.complete)
        }
    }
    todos = getVisibleTodos(todos, type)
    todos = todos.map((todo, i) => {
        return (
            <Todo 
                deleteTodo={deleteTodo}
                toggleComplete={toggleComplete}
                key={todo.todoIndex} todo={todo} />
        )
    })
    return (
        <View>{todos}</View>
    )
}
export default TodoList
```
##### app/TodoButton.js
```javascript
import React from 'react'
import { Text, TouchableHighlight, StyleSheet } from 'react-native'
const TodoButton = ({ onPress, complete, name }) => (
    <TouchableHighlight>
        <Text style={[styles.text, 
            complete ? styles.complete : null,
            name === 'Delete' ? styles.deleteButton : null]}>
        </Text>
    </TouchableHighlight>
)
const styles = StyleSheet.create({
    button: {
        alignSelf: 'flex-end',
        padding: 7,
        borderColor: '#ededed',
        borderWidth: 1,
        borderRadius: 4,
        marginRight: 5
    },
    text: {
        color: '#666666'
    },
    complete: {
        color: 'green',
        fontWeight: 'bold'
    },
    deleteButton: {
        color: 'rgba(175, 47, 47, 1)'
    }
})
export default TodoButton
```
##### app/TapBar.js
```javascript
import React from 'react'
import { View, StyleSheet } from 'react-native'
import TabBarItem from './TabBarItem'
const TabBar = ({ setType, type }) => (
    <View style={styles.container}>
        <TabBarItem type={type} title='All'
            setType={() => setType('All')} />
        <TabBarItem type={type} title='Active'
            setType={() => setType('Active')} />
        <TabBarItem type={type} title='Complete'
            setType={() => setType('Complete')} />
    </View>
)
const styles = StyleSheet.create({
    container: {
        height: 70,
        flexDirection: 'row',
        borderTopWidth: 1,
        borderTopColor: '#dddddd'
    }
})
export default TabBar
```
##### app/TabBarItem.js
```javascript
import React from 'react'
import { Text, TouchableHighlight, StyleSheet } from 'react-native'
const TabBarItem = ({ border, title, selected, setType, type }) => (
    <TouchableHighlight underlayColor='#efefef' onPress={setTyle}
        style={[styles.itetm, selected ? styles.selected : null,
            border ? styles.border : null, 
            type === title ? styles.selected : null ]}>
        <Text style={[ styles.itemText, type === title ? styles.bold : null ]}>
            {title}
        </Text>
    </TouchableHighlight>
)
const styles = StyleSheet.create({
    item: {
        flex: 1,
        justifyContent: 'center',
        alignItems: 'center'
    },
    border: {
        borderLeftWidth: 1,
        borderLeftColor: '#dddddd'
    },
    itemText: {
        color: '#777777',
        fontSize: 16
    },
    selected: {
        backgroundColor: '#ffffff'
    },
    bold: {
        fontWeight: 'bold'
    }
})
export default TabBarItem
```
