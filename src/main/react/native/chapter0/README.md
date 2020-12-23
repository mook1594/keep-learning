### [GO TO BACK](../README.md)

# 0. 프로젝트 시작
https://reactnative.dev/docs/getting-started
 
### 환경세팅
https://reactnative.dev/docs/environment-setup

### Create React Native App CLI
```shell script
npm install -g create-react-native-app
```
```shell script
npx create-react-native-app myProject
```

### React Native CLI
```shell script
npm install -g react-native-cli
```
```shell script
npx react-native init myProject
```

### 프로젝트 폴더 구성
#### android
- 안드로이드 플랫폼용 코드와 의존성 라이브러리
- 사용자 정의 브리지 구현
- 환경 설정 플러그인 설치

#### ios
- IOS 플랫폼용 코드와 의존성 라이브러리
- 플러그인 설치

#### node_modules
- npm(node package manager) 모듈 사용해 의존성 라이브러리 관리
- package.json이 라이브러리를 식별하고 버전관리

#### .flowconfig
- flow는 자바스크립트의 타입 검사기

#### .gitignore
- 버전관리가 필요하지 않은 파일 경로를 저장하는 파일

#### .watchmanconfig
- Watchman은 RN에서 사용하는 파일 감시 도구

#### index.js
- 앱을 실행하면 실행이 시작하는 파일.
- App.js를 가져오고 AppRegistry.registerComponent를 호출하고 앱을 실행

#### App.js
- 메인이 되는 파일
- 필요하다면 index.js파일 임포트로 사용할 수 있음

#### package.json
- npm 모듈 환경설정. 
