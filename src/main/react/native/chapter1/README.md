### [GO TO BACK](../README.md)

# 1. 리액트 네이티브로 시작하기

> 리액트 네이티브 소개  
> 리액트 네이티브의 강점들  
> 컴포넌트 만들기  
> 시작 프로젝트 만들기  

### 1.1 리액트와 리액트 네이티브 소개
- 리액트 자바스크립트 라이브러리를 사용해 모바일 앱을 만듬.

##### 장점
- 페이스북 지원
- 오픈소스
- Airbnb, 테슬라, 인스타그램, 우버, 아마존, MS

##### 왜 RN?
- 코르도바(Cordova), 아이오닉(Ionic)은 HTML, CSS를 사용해 만든 웹뷰를 패키징에서 네이티브 앱을 만듬
- 리액트 네이티브는 자바스크립트를 컴파일해 해당 네이트브한 코드로 변환. 플랫폼별 API와 컴포넌트를 사용할 수 있다.
- 네이티브 컴포넌트와 API를 바로 랜딩
- 속도 성능면에서 우월


#### 리액트의 기본 클래스
- 컴포넌트(Component): 조립 블록
- stateful, stateless 두가지 컴포넌트가 존재
##### stateful 컴포넌트 
```javascript
class HelloWorld extends React.Component {
    constuctor() {
        super()
        this.state = { name: 'Chris' }
    }

    render() {
        return (
            <SomeComponent />
        )
    }
}
```
##### stateless 컴포넌트 
```javascript
const HelloWorld = () => (
    <SomeComponent />
)
```

##### stateful VS stateless
- stateless: 생명주기 메서드에 연결되지 않고, 자신만 고유한 상태를 유지하지 않는다.  
그래서 화면에 보여지는 모든 데이터를 props로 받아야한다.

##### 기본 리액트 네이티브 클래스
```javascript
import React from 'react'
import { View, Text, StyleSheet } from 'react-native'

class HelloWorld extends React.Component {
    constructor() {
        this.state = {
            name: 'React Native in Action'
        }
    }
    componentDidMount() {
        console.log('mounted..')
    }
    render() {
        return (
            <View style={styles.container}>
                <Text>{this.state.name}</Text>
            </View>
        )
    }
    const styles = StyleSheet.create({
        container: {
            marginTop: 100,
            flex: 1
        }
    })
}
```
- 마운팅: 컴포넌트가 생성되면서 컴포넌트 생명주기가 시작되고 해당 메서드 들이 동작한다.
- 동적데이터: state(상태)로 지정하거나 props(속성)으로 전달 받아야한다.
- 상태변경: `this.setState({ name: 'Some Other Name' })` 으로 변경가능
- 랜더: 상태변경의 결과로 render 메서드가 호출됨. RN 요소를 반환하거나 null, false
- componentDidMount: 생명주기에서 최종 메서드. API, AJAX로 state를 재지정하려면 여기서 호출.

#### 리액트 생명 주기
- [2장](../chapter2/README.md)

### 1.2 리액트 네이티브의 작동 방식
#### JSX
- XML처럼 보이는 자바스크립트의 구문 확장
- 컴포넌트를 만들때 JSX가 없어도 구현가능 하지만 가독력, 유지보수가 좋아짐
#### 스레드 처리
- 모든 자바스크립트 동작은 별도 스레드로 처리됨
- 네이티브 안쪽 변화가 필요할 때는 일괄처리해서 네이티브로 전달
- 위 작업은 RN의 이벤트 루프의 마지막 단계에서 수행됨.
- 그래서 비즈니스 로직을 자바스크립트의 스레드 레벨에서 처리함.
#### 리액트
#### 단방향 데이터 흐름
#### 디핑(코드비교)
#### 컴포넌트 구성 

### 1.3 리액트 네이티브 강점
#### 개발자 가용성
#### 개발자 생산성
#### 성능
#### 단방향 데이터 흐름
- 최상위 컴포넌트에서 아래 모든 방향으로 단방향 흐름이 만들어진다.
#### 트랜스파일링
- 트랜스파일러가 특정 프로그램 언어로 변환해주는 것
- 내장된 바벨(Babel)을 사용하여 최신 자바스크립트 사용가능하게 해줌
#### 생산성과 효율성
#### 커뮤니티
#### 오픈소스
#### 빈번한 업데이트
#### 크로스 플랫폼 모바일 앱을 만드는 대안

### 1.4 리액트 네이티브 약점
#### 덜 성숙된 플랫폼
#### 리액트를 모른다면 리액트부터 숙지해야
#### API에 추상화된 계층을 이용하여 새버전에 빠르게 대응이 어려움

### 1.5 기본 컴포넌트 만들어 사용하기
#### 컴포넌트 개요
- 네이티브 컴포넌트: RN 내장 컴포넌트 지원
- 프레임워크를 사용하여 직접 컴포넌트를 만들 수 있음
- JSX vs HTML  

|컴포넌트 유형|리액트 네이티브 JSX|HTML|
|---------|---------------|----|
|Text|\<Text>Hello Worl\</Text>|\<span>Hello World\</span>|
|View|\<View>\<Text>Hello World2\</Text>\</View>|\<div>\<span>Hello World2\</span>\</div>|
|Touchable highlight|\<TouchableHighlight>\<Text>Hello World2\</Text>\</TouchableHighlight>|\<button>\<span>Hello World2\</span>\</button>|

#### 네이티브 컴포넌트
- 버튼 컴포넌트 만들기
```javascript
import { Text, TouchableHighlight } from 'react-native'
const Button = () => (
    <TouchableHighlight>
        <Text>Hello world</Text>
    </TouchableHighlight>
)
export default Button
```
- 버튼 컴포넌트 가져와 사용
```javascript
import React from 'react'
import { Text, View } from 'react-native'
import Button from './components/Button'
const Home = () => (
    <View>
        <Text>Welcome to the Hello World Button!</Text>
        <Button />
    </View>
)
```

#### 컴포넌트를 만드는 방식
##### createClass 구문 (ES5, JSX)
```javascript
const React = require('react')
const ReactNative = require('react-native')
const { View, Text } = ReactNative

const MyComponent = React.createClass({
    render() {
        return (
            <View>
                <Text>Hello World</Text>
            </View>
        )
    }
})
```
##### class 구문 (ES2015, JSX)
```javascript
import React from 'react'
import { View, Text } from 'react-native'

class MyComponent extends React.Component {
    render() {
        return (
            <View>
                <Text>Hello World</Text>
            </View>
        )
    }
}
```
##### stateless(재사용) 컴포넌트 (JSX)
```javascript
import React from 'react'
import { View, Text } from 'react-native'

const MyComponent = () => (
    <View>
        <Text>Hello World</Text>
    </View>
)
```

#### 외부로 export 가능한 컴포넌트
```javascript
import React, { Component } from 'react'
import {
    Text,
    View
} from 'react-native'
/*
var React = from 'react'
const Component = React.Component
import ReactNative from 'react-native'
const Text = ReactNative.Text
const View = ReactNative.View
*/

class Home extends Component {
    render() {
        return (
            <View>
                <Text>Hello from Home</Text>
            </View>
        )
    }
}
export default Home
// module.export = 'Home'
```

#### 컴포넌트 조립하기
```javascript
import React, { Component } from 'react'
import { Text, View } from 'react-native'

class Home extends Component {
    render() {
        return (
            <View>
                <Header />
                <Footer />
            </View>
        )
    }
}

class Header extends Component {
    render() {
        return <View>
                <Text>HEADER</Text>
            </View>
    }
}
const Footer = () => (
    <View>
        <Text>Footer</Text>
    </View>
)
```
