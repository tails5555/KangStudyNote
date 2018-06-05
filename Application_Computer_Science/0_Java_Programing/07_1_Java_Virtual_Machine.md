# Java Virtual Machine Issues

Java Compile에서 빠질 수 없는 중요한 주체가 바로 Java Virtual Machine이다. 

## What Is Hot Spot?

Oracle이 Java Language를 인수할 때 Java Virtual Machine을 재구성하여 만든 가상 머신의 이름이 Java HotSpot Performance Engine이다. 이를 줄여서 Hot Spot이라고 한다.

## Comparison of Compile

Compile 방식을 가진 언어는 C언어 계열, Java 언어 등이 있다. C언어에서는 소스 코드(`.c`, `.cpp`)를 기계어 코드로 변환하고 이를 `.exe` 파일에 내장 시킨다. 

반대로 Java 언어에서는 소스 코드(`.java`)를 Byte Code(`.class` 파일에 포함)로 변환(이는 표준 Java 컴파일러 실행 파일인 `javac.exe` 가 담당)하고, JVM에서 이 파일을 읽어 들여 실행(JVM 실행 파일은 `java.exe`)한다.

`.exe` 파일은 CPU에서 직접 실행하지만, `.class` 파일은 운영체제와 CPU와 무관하게 배포되고, JVM이 있는 환경이라면 언제든지 실행될 수 있는 장점이 있다.

그렇지만 `.class` 파일인 Byte Code를 해석하는 시간이 있기 때문에 약간 느릴 수 있다.

## JIT Compile(Just In Time Compile)

Java는 Just In Time Compile 방식을 이용한다.

JVM이 Java 언어를 그냥 읽는 것이 아니다. Virtual Machine이 이해할 수 있는 Machine Code(기계어 코드)로 변환 시켜야 한다.

이는 Byte Code(바이트 코드. class 파일)를 실행하기 전에 기계어 코드로 컴파일하는 방식이다.

Java 언어는 Inter Printer(최근에 Java 9 에서도 Inter Printer 방식을 적용한 Shell의 기능이 탑재되었다.) 방식이 아니다. (Inter Printer 방식은 Byte Code를 직접 해석하는 원리이다.) 기계어 코드로 변환해서 실행하는 Compile 방식으로 실행하는 속도는 후자가 더 빠르게 먹힌다.

JVM 내부에는 JIT 컴파일러가 내장되어 있어 Byte Code를 기계어 코드로 변환하는 역할을 한다.

JIT Compile의 장점은 Dynamic Optimization(동적 최적화)를 제공한다.

이는 컴파일러가 소스코드와 동일한 순서로 기계어 코드를 생성하지 않고 좀 더 빠른 방법으로 재배치하고 이를 생성하는 Compiler Optimization(컴파일러 최적화)를 진행하는 방법과 달리 이 작업을 **실행 시점에서 치루어 기계어 코드를 생성하는 방법이다.**

하지만 단점으로 변환된 기계어 코드를 따로 저장하지 않아서 매번 JIT 컴파일을 진행해야 한다.

## Structure of Hot Spot Java Virtual Machine

![hotspot_java_virtual_machine](/Application_Computer_Science/0_Java_Programing/img/hotspot_java_virtual_machine.png)

- Class Loader : 바이트 코드(.class)가 담긴 파일을 메모리에 불러오는 역할을 한다.

- Runtime Data Area : 데이터를 보관하는 메모리 영역으로 5가지로 나뉜다.

> - Method Area : 이 영역의 대표적인 이름은 Permanent Generation이었지만, Java 8 버전 부터 Meta Place로 바뀌었고, 구조도 변경되었다. 이 영역에는 method의 bytecode, static 멤버 변수, constant 값이 위치한다.
>
> - Heap : Java 객체와 배열이 생성되는 영역으로 Heap Segment를 많이 들어봤을 것이다. Garbage Collection 작업은 이 곳을 주름 잡는다.
>
> - Java Stack : 지역 변수와 매개 변수가 생성되는 영역이다. Java 메소드에서 쓰이는 이 변수들이 생성되는 공간이 할당되는데 이 공간은 메소드가 리턴되면 반납된다. Stack Segment로 친숙하다. 또한 각 Thread 별로 Stack Segment를 소유한다.
>
> - Program Counter Registers : 컴퓨터 구조 시간에 다룬 프로그램 카운터(PC) 레지스터 맞다. 이는 Java Thread에서 실행하는 바이트 코드의 주소가 이 곳에 저장되는데 각 Thread 별로 PC Register를 따로 소유해야 한다.
>
> - Native Stack : Java 표준 라이브러리에도 <small>사실은</small> C언어로 구현된 메소드도 있는데 이를 Native Method로 부른다. 이를 호출할 때 사용될 Stack Segment를 Native Stack이라고 한다.

- Execution Engine : 실행 엔진이다.

## Memory Structure of Thread

![jvm_memory](/Application_Computer_Science/0_Java_Programing/img/jvm_memory.jpg)

Run Time Data Area에는 Thread들이 마천루처럼 쌓여 있는데 Thread가 가지는 영역은 PC Register, Stack, Native Stack이다. 마천루 속에 있는 Thread가 공유하는 메모리 영역은 Heap과 Method Area이다.

또한 Heap, Method Area 이외에 공유되지 않는 객체를 생성하기 위하여 ThreadLocal 클래스를 사용하는 방법도 있다.

## Stack Overflow Attack

함수가 리턴할 때 기계어 코드의 주소가 Stack Segment에도 저장 된다. 또한 지역 변수도 저장이 되어 이를 노리는 해킹이 Stack Overflow Attack이다. 특히 배열에 데이터를 저장하거나 복사할 때 범위를 넘쳐 저장하는 방법으로 접근하는데 Virtual Machine이 이를 검사하여 Overflow가 불가능함과 동시에 IndexOutOfBoundsException을 발생 시켜 접근을 막는다.

최근에도 C++에도 이러한 위험성을 인지하여 이를 최소화하고 있다.

## Thanks To
- 성공회대학교 소프트웨어공학과 **이승진 교수님**

## References
- https://medium.com/@lazysoul/jvm-%EC%9D%B4%EB%9E%80-c142b01571f2 - JVM 개념과 구조 참조하기 좋은 블로그 글.

## Post Script
- 여기에 작성된 내용 이외에도 필요한 개념들을 발견하게 된다면 언제든지 갱신될 수 있습니다.
- 이 강의노트 개념에서 더욱 다뤄주면 좋은 개념들이나 오탈자가 있으시면 KangBakSa Issues에 올려주세요.