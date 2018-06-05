# Java Garbage Collection Issues

Java 언어에서 new 연산자를 사용해 생성된 객체가 자동으로 소멸되는 일에 대해 해결하는 주체가 Garbage Collation이다. 이는 Java Virtual Machine에 의해 자동으로 실행되는데 시간이 오래 걸려서 마치 변기통과 똑같다.

이번 노트에서는 Java Garbage Collection에 대해 알아보는 시간을 가지도록 하자.

## Java VS C++

C++ 에서는 Java 와 달리 객체에 대한 소멸자가 존재한다. 반대로 Java는 개발자가 객체를 직접 소멸시키는 방법이 없다.

Java 에서 직접 소멸은 안 시켜도 더 이상 사용하지 않는 객체를 참조하는 변수에 null을 대입할 때 자동으로 Garbage Collection 처리를 할 수 있다. 아래 소스 코드의 (*)와 같다.

```
Student student = new Student("강학생", 25);
System.out.println(student.getName());
System.out.println(student.getAge());
student = null; // (*)
```

## Stop-The-World

Garbage collection은 Heap 영역의 메모리를 Java Virtual Machine이 판단해 더 이상 사용하지 않는 인스턴스는 자동으로 할당 된 메모리를 삭제하는 역할을 하는 행위를 `Stop-The-World` 라고 한다.

Java에서 메모리를 복사하고 해제하는 작업을 실행할 때, Application 내에 존재하는 모든 Thread 들 중에서 Garbage Collection을 실행하는 Thread 를 제외하고 멈춘다. 그 다음에 Garbage Collection 작업을 진행한 후에야 나머지 Thread들이 실행(Run) 상태로 돌아간다.

마치 화장실에서 볼 일 보고 난 후에 물 내리는 원리와 똑같다. 변기 스위치를 내리면 물이 내려가면서 이상한 소리가 나온 후에 원래 변깃물의 양 만큼 다시 채워준다. 물을 채우는 작업을 실행(Run) 상태로 구상할 수 있다.

그래서 성능이 매우 중요한 Application인 경우에 Garbage Collection 동안 Application이 정지되어 문제가 된다.



