### [GO TO BACK](../README.md)

# 2. 리액트 이해하기

> state가 동작하는 방식과 중요성  
> prop가 작동하는 방식과 중요성  
> 리액트 컴포넌트 스펙 이해하기  
> 리액트 생명주기 메서드 구현하기  

### 2.1 state를 사용해 컴포넌트 데이터 다루기
- 컴포넌트에서 데이터 만들고 다루는 방식으로 state 사용
- state는 컴포넌트가 생성될 때 선언
- setState 함수를 통해서 컴포넌트내에서 갱신 

#### 컴포넌트의 상태 제대로 조작하기
- setState 함수를 이용해서 state를 변경하면 리액트는 컴포넌트를 다시 랜더링 한다.
- 부모 컴포넌트 state를 자식 컴포넌트 props로 받아 사용하면 부모 컴포넌트의 상태가 변경되어 둘다 랜더링된다.

#### 컴포넌트의 state가 중요한 이유
- 컴포넌트의 state는 동적이고 interactive하게 해준다.
- state는 mutable이고, props는 immutable인 차이를 이해.

#### 초기 state 지정하기 
- ES7에서 초기화 지원
```javascript
import React from 'react'

class MyComponent extends React.Component {
    state = {
        year: 2016,
        name: 'Nader Dabit',
        colors: ['blue'] 
    }

    render() {
        return (
            <View>
                <Text>My name is: { this.state.name }</Text>
                <Text>The year is: { this.state.year }</Text>
                <Text>My colors are { htis.state.colors[0] }</Text>
            </View>
        )
    }
}
```
- vanilla Js
```javascript
import React, {Component} from 'react'

class MyComponent extends Component {
    constructor() {
        super()
        this.state = {
            year: 2018,
            name: 'Nader Dabit',
            colors: ['blue']
        }
    }
    render() {
        return (
            <View>
                <Ttext>My name is: { this.state.name }</Text>
                <Ttext>The year is: { this.state.year }</Text>
                <Ttext>My colors are: { this.state.colors[0] }</Text>
            </View>
        )
    }
}
export default MyComponent
```
#### state 갱신하기
```javascript
import React {Component} from 'react'

class MyComponent extends Component {
    constructor() {
        super()
        this.state = {
            year: 2018,
        }
    }
    updateYear() {
        this.setState({
            year: 2019
        })
    }
    render() {
        <View>
            <Text onPress={{() => this.updateYear()}}>
                The year is: { this.state.year }
            </Text>
        </View>
    }
}
```
- setState가 호출될 때마다 리액트는 render호출하여 다시 랜더링 한다.
- setState를 통하지 않고, state를 직접 변경하려고 하면 컴포넌트가 다시 랜더링 되지 않는다.
`this.state.year = 2019`  

### 2.2 props를 사용해 컴포넌트 데이터 다루기
- properties(props): 부모 컴포넌트에서 전달된 속성값, 컴포넌트가 상속 받은 값.
- 컴포넌트가 선언될 때 고정된 값이나 동적인 값일 수 있지만, 상속되고 나면 props는 변경 불가
https://reactjs.org/docs/thinking-in-react.html

##### props vs state
|props|state|
|-----|-----|
|외데이터|내부데이터|
|변경불가능|변경가능|
|부모로부터 상속|컴포넌트 생성|
|부모 컴포넌트가 변경 가능|컴포넌트에서만 갱신|
|props로 전달|props 전달|
|컴포넌트 내부 변경 불가|컴포넌트 내부 변경 가능|

##### 정적 props
```javascript
class MyComponent extends Component {
    render() {
        return (
            <BookDisplay book={ "React Native in Action" } />
        )
    }
}
class BookDisplay extends Component {
    render() {
        return (
            <View>
                <Text>{ this.props.book }</Text> 
            </View>
        )
    }
}
```
##### 동적 props
```javascript
class MyComponent extends Component {
    render() {
        let book = 'React Native in Action'
        return (
            <BookDisplay book={ book } />
        )
    }
}
class BookDisplay extends Component {
    render() {
        return (
            <View>
                <Text>{ this.props.book }</Text>
            </View>
        )
    }
}
```
##### 동적 props(state 상용)
```javascript
class MyComponent extends Component {
    constructor() {
        super()
        this.state = {
            book: 'React Native in Action'
        }
    }
    render() {
        return (
            <BookDisplay book={this.state.book} />
        )
    }
}
class BookDisplay extends Component {
    render() {
        return (
            <View>
                <Text>{ this.props.book }</Text>
            </View>
        )
    }
}
```
- props는 변경불가 하므로 MyComponent의 state를 변경해야하는데, BookDisplay의 book props에 새로운 값을 제공하게 되어 부모, 자식 다시 랜더링된다.
##### 동적 props 갱신
```javascript
class MyComponent extends Component {
    constructor() {
        super()
        this.state = {
            book: 'React Native in Action'
        }
    }
    updateBook() {
        this.setState({
            book: 'Express in Action'
        })
    }
    render() {
        return (
            <BookDisplay 
                    updateBook={() => this.updateBook()} 
                    book={ this.state.book } />
        )
    }
}
class BookDisplay extends Component {
    render() {
        return (
            <View onPress={ this.props.updateBook }>
                { this.props.book }
            </View>
        )
    }
}
```
##### props와 state 구조 분해 할당
```javascript
class MyComponent extends Component {
    constructor() {
        super()
        this.state = {
            book: 'React Native in Action'
        }
    }
    updateBook() {
        this.setState({ book: 'Express in Action' })
    }
    render() {
        const { book } = this.state
        return (
            <BookDisplay
                updateBook={ () => this.updateBook() }
                book={ book } />
        )
    }
}
class BookDisplay extends Component {
    render() {
        const { book, updateBook } = props
        return (
            <View>
                <Text onPress={ updateBook }>
                    { book }
                </Text>
            </View>
        )
    }
}
```
##### stateless 컴포넌트에서의 props
```javascript
const BookDisplay = (props) => {
    const { book, updateBook } = props
    return (
        <View>
            <Text onPress={ updateBook }>
                { book }
            </Text>
        </View>
    )
}
```
##### stateless 컴포넌트에서 props 구조 분해 할당하기 
```javascript
const BookDisplay = ({ updatetBook, book }) => {
    return (
        <View>
            <Text onPress={ updatetBook }>
                { book }
            </Text>
        </View>
    )
}
```
##### 배열과 개체를 속성(props)으로 전달하기
```javascript
class MyComponent extends Component {
    constructor() {
        super()
        this.state = {
            leapYear: true,
            info: {
                type: 'programming'
            }
        }
    }
    render() {
        return (
            <BookDisplay
                leapYear={ this.state.leapYear }
                info={ this.state.info }
                topics={['React', 'React Native', 'JavaScript']} />
        )
    }
    const BookDisplay = (props) => (
        let leapYear
        let { topics } = props
    )
    const { info } = props
    topics = topics.map((topic, i) => {
        return <Text>{ topic }</Text>
    })
    if(props.leapYear) {
        leapYear = <Text>This is a leapYear!</Text>
    }
    return (
        <View>
            { leapYear }
            <Text>Book type: { info.type }</Text>
            { topics }
        </View>
    )
}
```

### 2.3 리액트 컴포넌트 스팩
##### render 메서드
```javascript
render() {
    return (
        <View>
            <Text>Hello</Text>
        </View>
    )
}
render() {
    return <View><Text>Hello</Text></View>
}
render() {
    return <SomeComponent />
}
render() {
    if(something === true) {
        return <SomeComponent />
    } else return
        <SomeOtherComponent />
}
```
##### constructor 메서드
```javascript
constructor() {
    super()
    this.state = {
        someOtherNumber: 19,
        someOtherBoolean: true,
        fullName: props.first +  ' ' + props.last,
    }
    this.name = 'Hello world'
    this.type = 'class'
    this.loaded = false
}
```


