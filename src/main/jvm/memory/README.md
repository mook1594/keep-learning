# JVM Memory

메모리를 여러가지고 나누어 가지고 있음
- Method Area
- Heap
- Native Method Stacks

- JVM은 Class Loader를 활용해 컴파일한 바이트코드를 Runtime Data Areas의 Method Area에 실행 가능 상태로 Load
- Execution Engine은 Method Area에 Load 되어 있는 Byte Code 정보를 활용해 자바 프로그램을 실행한다.

### Method Area
- Byte코드는 변경되지 않는 코드
- 모든 Thread 들이 공유 해도 상관없고 공유하는 것이 좋다. 
- 실제 모든 Thread들이 공유하는 메모리 영역 Method Area

### JVM Stacks, Native Method Stacks
- 프로그램을 실행할 때 JVM Stacks, Native Method Stacks 공간을 활용한다.
- Thread는 stack 영역 메모리가 별도로 만들어짐 

### Heap
- heap에서는 자바클래스의 인스턴스와 Array에 대한 메모리를 관리

### 스레드와 메모리영역
- stack은 스레드마다 생기기때문에 메모리 문제가 안생긴다
- heap은 스레드간 공유하고 있으므로 이슈가 발생 할 수 있다.

