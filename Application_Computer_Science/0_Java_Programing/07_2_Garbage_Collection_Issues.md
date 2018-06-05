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


## Weak Generational Hypothesis

Java에서 객체 특성 중 더욱 자세히 들여다 보면 **짧은 시간 안에 Garbage가 되고, 오래된 객체의 멤버 변수가 젊은 객체를 참조하는 경우는 드물다.** 이 특징들을 다룬 가설이 Weak Generational이다. 각 특성 별로 짧막하게 설명하면 다음과 같다.

- 오래된 객체(Old Generation)
    static 멤버 변수에 의해 참조하는 객체는 오래 남아있다. 반대로 지역 변수가 참조하는 객체들은 금방 Garbage Collection 처리 된다.

- 짧은 시간 안에 Garbage 처리된다.<br>
    > 토끼를 잡는 개도 언젠가 보신탕으로 끓여 먹는다.(토사구팽)
    >
    > 그럼 지역 변수에서 필요한 객체를 쓰고 난 뒤에도 언젠가 제명 처리 되지 않을까?

    메소드 내에서 잠깐 지역 변수에서 쓰인 토사구팽 객체들이 많다. 이 메소드개 리턴될 때 지역 변수가 없어지면 이를 의해서만 참조된 객체는 Garbage Collection 처리한다.

- 오래된 객체의 멤버 변수가 젊은 객체(Young Generation)를 참조하는 경우는 드물다.
    오래된 객체의 맴버 변수가 참조하는 객체는 오래된 객체일 확률이 높아 일찍 제거되진 않는다. 일찍 제거되는 객체는 대부분 지역 변수에 대입하여 사용된다.

## System.gc()

Garbage Collection 작업을 즉시 실행하는 메소드이다.

이 메소드에 대해서는 Garbage Collection이 자연스럽게 처리할 수 있도록 하기 위해 아예 모르는 것이 오히려 약이 된다.

## Mechanism of Garbage Collection

![garbage_collection_mechanism](/Application_Computer_Science/0_Java_Programing/img/garbage_collection_mechanism.png)

<small>노란색으로 체크한 부분이 더 이상 참조 되지 않는 객체를 뜻한다.</small>

Garbage Collection 과정의 종류는 mark(찍고), sweep(없애고), compact(땡기고)으로 나뉜다. 마치 뿌요뿌요에서 같은 색깔이 4개 연속 나오면 깜빡이고, 없애면서 보너스 점수를 받고, 위에 있는 다른 색깔 블록이 땡겨 내려오는 원리로 볼 수 있다.

`mark` 과정은 더 이상 변수에 의해 참조되지 않는 객체인 garbage를 찾는 작업이다. 모든 변수를 뒤져서 일단 체크만 하는 과정이다.

`sweep` 과정은 garbage를 일단 삭제만 하는 작업이다. 여기서 빈 공간이 흩어져 있기 때문에 새 객체를 생성할 때 적당한 공간을 찾아야 하는 단편화 문제가 야기된다.

`compact` 과정은 사실 필수까진 아니다. 삭제되지 않은 객체들을 한 곳으로 몰아서 빈 영역을 확보하여 단편화의 문제를 해결하는 작업이다. 이 작업이 이뤄지면 메모리 낭비를 줄일 수 있다.

## Young Generation / Old Generation

> Heap 영역에 현존하는 객체들은 사람보다 오래 사는 나무와 하루 살이가 모여 있는 곳이다.

생성된 Java 객체가 시간이 지나면 어느 순간에 Garbage로 둔갑된다. 이를 청소하기 위해 Young Generation과 Old Generation으로 나뉜다.

객체를 처음 생성하면 Young Generation에 배치된다. 그렇지만 Young Generation에 있는 객체들은 하루살이처럼 없어지기 때문에 자주 Garbage Collection 처리를 해야 한다. 이러한 작업을 Minor Garbage Collection으로 부른다.

그렇지만 일정 시간이 지나면 Old Generation으로 배치되어 오랫동안 생존하고, 청소 빈도도 낮다.

하지만 만의 하나로 Old Generation과 Young Generation을 대청소 하는 경우가 있는데 이를 Major Garbage Collection으로 부른다.

## Card Table

Old Generation 객체의 맴버 변수에 의해 참조 되는 객체는 Garbage 객체가 아니다. Old Generation도 이보다 더 Old Generation 한 객체를 가리키는 확률이 높기 때문이다.

그래서 Young Generation 객체들을 자주 Garbage Collection을 하는데 굳이 Old Generation 객체들을 가리키는 경우는 극소이기 때문에 만의 하나 가리키는 경우에는 **Card Table** 기록을 한다.

이 방법은 자주 청소하는 Young Generation 객체를 mark 할 때 Old Generation 영역의 맴버 변수를 전부 보지 않고 Card Table를 읽어 보는 것으로 땡 친다.

그렇지만 그 때 그 때 마다 기록하는 번거로움이 있어도 Minor Garbage Collection을 진행하는 점에서 더욱 도움된다.

## Structure Of Heap Segment

![Heap_Structure](/Application_Computer_Science/0_Java_Programing/img/Heap_Structure.png)

Young Generation과 Old Generation은 new 연산자로 생성된 객체 혹은 배열들이 들어 있다.

Permanent Generation은 클래스와 메소드에 대한 Meta Data들이 있는데 최근에는 메모리 관리 기능도 개선되었으며, 오히려 이 영역은 Heap Segment에서 배제되고 있다.

## Garbage Collection Mechanism In Young Generation

Young Generation에는 eden 영역, 2개의 survivor 영역으로 나뉜다. 물론 처음 시작할 때 eden, survivor 각각 빈 상태다. 생성되는 객체는 eden 영역에 들어온다.

![young_mechanism_01](/Application_Computer_Science/0_Java_Programing/img/young_mechanism_01.png)

처음에는 Garbage Collection을 진행하지 않는다. 그렇지만 갑자기 eden 영역에 객체들이 꽉 차게 되면 일부 필요 없는 Garbage에 대해서 mark, sweep 과정을 치루고 난 후에 survivor 영역 중 0번으로 옮긴다. 여기서 노란색으로 칠한 부분이 필요 없는 객체를 뜻한다.

![young_mechanism_02](/Application_Computer_Science/0_Java_Programing/img/young_mechanism_02.png)

여기서 survivor 0번으로 옮기는 compact 작업이 진행되고, 각 객체들이 살아남은 횟수를 1씩 더해 기록한다.

![young_mechanism_03](/Application_Computer_Science/0_Java_Programing/img/young_mechanism_03.png)

