#### [GO TO BACK](../../../README.md)
# JVM

### 왜 JVM을 이해해야하나?
- 다중 Thread 서버에서 매 요청마다 인스턴스를 생성하고 GC를 하는데 비용이 발생
- 이런 GC 비용을 줄이기 위해 싱글 인스턴스 생성 후 재사용 (Spring Beans)

### 자바코드 컴파일 부터 실행까지
- Class Loader System을 활용하여 Java 파일을 컴파일하여 ByteCode로 변환한다.
- 변환된 ByteCode는 Runtime Data Areas의 Method Area에 실행 가능한 상태로 Load 한다.
- Execution Engine은 Method Area에 Load되어 있는 ByteCode 정보를 활용해 자바프로그램을 실행한다.
- Execution Engine은 ByteCode를 한 라인씩 실행한다. (interpreting)
- 프로그램을 실행할때는 JVM Stacks, Native Method Stacks 공간을 활용한다.
- JVM은 Heap에서 자바 프로그램을 실행할 때 자바 클래스의 인스턴스와 Array에 대한 메모리 관리를 한다.
- JVM Heap과 Method Area는 Thread가 메모리를 공유한다.

### Method Area
- 모든 Thread들이 공유하는 메모리 영역
- Class, Method, Field 정보를 갖음
- type, 상수, name, static variables, reference class 등등

### JVM Stacks, Native Method Stacks
- 각 Thread마다 서로 다른 메모리가 할당된다.
- Thread마다 서로 다른 메모리를 사용하기 때문에 다중Thread가 method를 동시 사용해도 문제가 발생하지 않음.

### JVM Stacks
- stack frame은 local variable영역과 operand stack 부분이 있다.
- local variable에서 변수를 유지하다가 실행에 필요한 것들을 operand stack에 올려두고 사용후 다시 local vaiable로 저장한다.
- 각 method실행을 위한 stack frame이 생성되며 method 실행이 끝나면 stack frame이 제거된다.

### Heap
- 모든 Thread가 공유하는 메모리 영역
- JVM에서 메모리 할당, 해제(GC)가 발생하는 부
