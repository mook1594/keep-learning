# JVM의 다양한 Garbage Collector

### GC 알고리즘 - Copy & Scavenge 
- 객체는 처음 생성되면 Eden(Young) 영역으로 들어간다.
- Minor GC가 일어나면 Eden, From에 있는 객체 중 살아있는 객체를 To
    영역으로 복사하고 나머지는 해체한다.
- 이 과정을 반복하다가 From, To에서 오래된 객체들은 Old 영역으로 옮겨짐
- 속도가 빠르다

### GC 알고리즘 - Mark & Compact
- Old 영역에서 일어나는 Major GC는 Full GC 라고도 함
- JVM에서 Full GC가 일어나면 Thread가 멈추는 Stop the world 현상 발생
- Full GC는 전체 객체들의 참조를 확인하면서 사용되지 않는 객체를 표시하여 삭제
- 메모리 영역에 대한 compact가 필요하여 속도가 매우 느리다

### JVM을 튜닝한다는 의미?
- Old영역으로 넘어가는 객체의 수를 최소화 (Full GC가 느리니까)
- Full GC 실행 시간을 줄이는 노력 (Full GC가 느리니까)

##### Old 영역으로 넘어가는 객체의 수 최소화하기
- Eden 영역에서 객체가 만들어지고 Survivor 영역을 오가다가 끝까지 살아남은 객체는 Old로 이
- 간혹 Eden에서 몸집이 커져 Old로 바로 넘어가는 객체도 있긴함
- Old의 GC가 Young GC 보다 상대적으로 시간이 오래 걸리기 때문에 그 수를 줄임
- Young에 있는걸 Old로 아예 못가게 할 순 없지만 Young영역 크기를 조절하여 큰 효과를 볼 수 있다.

##### Full GC 시간 줄이기
- Full GC는 Minor GC보다 상대적으로 길다. 그래서 타임아웃이 발생할 수 있음
- 그렇다고 Full GC 실행 시간을 줄이기 위해서 Old 영역 크기를 줄이면 OutOfMemory가 발생하거나 Full GC 횟수가 늘어날 수 있음
- Old 영역 크기를 늘리면 GC횟수는 줄지만 실행시간이 늘어나므로 잘 조절해야한다.
- 모니터링 하여 잘 조절한다.
```properties
-Djava.awt.headless=true -server –Xms2048m -Xmx2048m -XX:NewSize=768m -XX:MaxNewSize=768m -XX:NewRatio=2 -XX:PermSize=128m -XX:MaxPermSize=256m -XX:USeParNewGC
```

### 경험속에서 드는 생각
- 쿼리튜닝, JVM 튜닝 등 튜닝을 하려면 모니터링이 필요함
- 모니터링을 효과적을 잘 하려면 그에 맞게 처리 및 관리 할 수 있는 것이 필요함

### The Serial GC
- Serial GC는 가장 단순한 GC
- 싱글 쓰레드 환경을 위해 설계, 작은 Heap
- Full GC가 일어나는 동안 애플리케이션 전체가 대기해야하는 현상 발생
- 서버에는 비적합

### The Parallel GC Threads
- Java8의 디폴드 GC, 병렬로 GC
- CPU의 성능과 코어 개수가 많아 순간적으로 트래픽이 몰려도 일시 중단을 견딤
- CPU 오버헤드에 대해 최적화 할 수 있어 애플리케이션에 가장 적합
```
-XX:+UseParallelGC: Minor GC에서 활성화 가능
-XX:+UseParallelOldGC: Major GC에서 활성화 가능
```

### The Concurrent Mark & Sweep GC
- CMS GC, Class Loader로 부터 객체 참조가 발생하는 Root 부터 객체 참조 상태를 관리한다
- ....

### The G1(Garbage First) GC
- JDK 7u4 부터 도입, 4GB이상의 큰 자원을 제공, 장기적으로 CMS를 대체하기 위한 설계
- Generational GC 알고리즘과 다르게 동작
- 백그라운드의 멀티 쓰레드를 활용해 1MB ~ 32MB 까지 Heap을 나눈다.
- 객체를 각 영역에 할당하고 GC를 실행, 해당영역이 꽉차면 다른 영역에 할당하고 GC 실행
- 지연시간을 줄인 어느것보다도 가장 빠르다
- 4GB의 큰힙은 논쟁거리 (MSA 쪼개고 있는 상황에서 4GB라니)


### JVM 튜닝 꼭 해야할까?
- JVM 튜닝은 가장 마지막에 하는것. (최후의 수단)
- JVM보다 내부 애플리케이션 이슈를 해결먼저 하자
- 메모리를 이용하는 클래스를 구현하면서 HashMap을 잘못 사용하는 경우
- HashMap의 put(), get()을 사용할 때는 동기화 기법을 통해 `Thread Safe`하게 코드 작성하거나 `ConcurrentHashMap`를 사용하는걸 추천

### GC 모니터링
- GC 로그 위한 JVM Option
```properties
XX:-PrintGC -XX:-PrintGCDetails -XX:-PrintGCTimeStamps -XX:-TraceClassUnloading -XX:-TraceClassLoading
```
- 스레드 덤프 획득 (Out Of Memory 에러 발생)
```properties
-XX:-HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=./java_pid<pid>.hprof
```
```properties
$ jps -v
8352 GradleMain -Dorg.gradle.appname=gradle
8372 GradleDaemon -XX:MaxPermSize=256m -XX:+HeapDumpOnOutOfMemoryError -Xmx1024m -Dfile.encoding=UTF-8 -Duser.country=US -Duser.language=en -Duser.variant
8409 Jps -Dapplication.home=/Library/Java/JavaVirtualMachines/jdk1.8.0_102.jdk/Contents/Home -Xms8m
...
$ jstack :PID
...
$ kill -3 :PID
...
```
- 현재 메모리 확인
```shell script
$JAVA_HOME/bin/jstat
```
- MAT(Memory Analyzer) 플러그인 활용



[참고 블로그](https://www.holaxprogramming.com/2017/10/09/java-jvm-performance/) 
