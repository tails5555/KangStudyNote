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

이번에도 새로운 객체를 각각 추가하면서 eden 영역이 가득 차고 난 후에 survivor 0 영역에서도 필요 없는 객체까지 mark 한다면 아래와 같이 이어진다.

![young_mechanism_04](/Application_Computer_Science/0_Java_Programing/img/young_mechanism_04.png)

또한 마찬가지로 eden 영역에 있는 객체는 survivor 1 영역으로 이동을 하면서 살아남은 횟수를 1씩 더한다. 그리고 survivor 0에서 운 좋게 남은 객체도 survivor 1로 옮기면서 살아남은 횟수를 1씩 더하여 2를 남긴다.

![young_mechanism_05](/Application_Computer_Science/0_Java_Programing/img/young_mechanism_05.png)

그러면 Young Generation 내부에 있는 모든 garbage를 sweep하고 난 후에 compact 작업까지 완료하면 살아남은 객체가 survivor 1에 남게 된다.

![young_mechanism_06](/Application_Computer_Science/0_Java_Programing/img/young_mechanism_06.png)

이번에도 eden 영역이 꽉 차게 되면 삭제할 garbage를 mark하고, 살아남은 객체들에 대해 survivor 0으로 옮기면서 살아남은 횟수를 1씩 더하여 각각 3, 3, 2, 1의 가중치를 남긴다.

![young_mechanism_07](/Application_Computer_Science/0_Java_Programing/img/young_mechanism_07.png)

이제 survivor 0 영역에 남은 객체들이 또 남게 된다.

규칙을 보면 eden 영역이 꽉차게 되면, Young Generation 영역 자체를 Minor Garbage Collection 작업을 거치고, survivor 객체에 남은 객체를 우선으로 옮기고, eden에 남은 객체 중에 이용 빈도가 높은 것을 survivor 0, 1 구역에 번갈아가면서 옮기는 것을 확인하였다.

## Young Generation to Old Generation

Old Generation 영역으로 옮기려면 일정 생존 횟수에 해당되는 객체를 survivor 영역에서 tenured 영역으로 옮긴다. 여기서 살아 남은 객체 횟수가 9번 이상이면, Old Generation 영역으로 옮긴다고 가정한다.

아래에서 eden 영역에 생성된 객체들이 꽉 차는 시점에서 Garbage Collection이 일어나고 survivor 1에 있는 객체 중에 살아남은 횟수가 8번인 객체를 tenured 영역으로 옮기고 생존 횟수를 9로 늘린다. 그리고 survivor 1의 나머지 객체 중에 mark를 안 한 객체는 survivor 0으로 보낸다.

![young_mechanism_08](/Application_Computer_Science/0_Java_Programing/img/young_mechanism_08.png)

특정 횟수 만큼 Minor Garbage Collection에서 생존한 객체는 Old Generation으로 옮겨 지면서 Young Generation 객체들보다 생존성을 보장 받은 은행나무가 되었다. 그렇지만 언젠가 Old Generation 영역에도 꽉 차는 경우는 Old Generation 영역과 Young Generation 영역을 합친 대청소 급 Major Garbage Collection 작업을 한다.

![young_mechanism_09](/Application_Computer_Science/0_Java_Programing/img/young_mechanism_09.png)

**Copying Collector**

Young Generation 영역 내의 Garbage Collection 작업을 담당하는 엔진이 Copying Collector이다. 이는 mark 작업과 copy 작업으로 구성되는데 mark 작업은 참조 여부를 따지고 식별하고, copy 작업은 survivor 영역 중에서 빈 공간을 찾아서 옮기는 것이다.

## Kinds of Garbage Collectors

Garbage Collection 작업을 수행하는 Garbage Collector Engine는 이미 JVM에 포함되어 있다. 그렇지만 이 종류를 잘 택해야 하는 것도 JVM의 Garbage Collection 작업에서 Stop-The-World 사태를 줄여 나갈 수 있거나 Heap Segment 용량 조절이 가능하다.

### Serial Garbage Collector

CPU Core 수가 1개일 때 사용된다. Minor Garbage Collection, Major Garbage Collection 작업이 Single Thread로 실행되고, compact 작업까지 실행된다.

하나의 운영체제 프로세스 내부에 Thread가 여러 개 생성이 가능한 점을 고려하여 서버 1대에 여러 개 JVM Process가 실행되면 각각 Serial Garbage Collector를 사용한다.

또한 다른 프로세스들도 같이 실행되는 서버에서 CPU 코어의 독점을 막을 수 있다.

그렇지만 Stop-The-World 발생 시간이 1~2초 걸리는 단점이 있어도 Heap 영역의 크기가 100MB 이하로 작은 편이다.

### Parallel Garbage Collector

Minor Garbage Collection만 Multi Thread로 실행한다. CPU 코어들을 전부 사용하여 JVM 프로세스의 성능을 최대로 높이면서 CPU 코어의 독점을 허용할 수 있다.

또한 Old Generation까지 포함한 Garbage Collection을 Multi-Thread로 처리할 수 있는 Parallel Old Garbage Collection이 있다.

Parallel Garbage Collection 진행 중에는 Stop-The-World 발생 시간이 Serial Garbage Collection보단 짧은 편이다. 그러나 메모리 용량이 많이 드는 편이다.

**Parallel Garbage Collector**

Young Generation 영역만 Multi-Thread에서 처리 가능하다.

Old Generation 영역의 Garbage Collection에서 sweep과 compact 작업은 Multi-Thread로 실행될 수 없다. 그러나 mark는 Multi-Thread로 실행될 순 있다.

**Parallel Old Garbage Collector**

Old Generation 영역도 Multi-Thread에서 처리 가능하다.

그렇지만 sweep 단계보다 더욱 복잡한 summary 단계를 거친다.

### Concurrent Mark Sweep Collector

Garbage Collection 작업 중 Stop-The-World 시간을 최대로 줄이기 위해 선택하는데 이 작업 동안 Application Thread도 같이 실행될 수 있다.

![cms_collector](/Application_Computer_Science/0_Java_Programing/img/cms_collector.png)

Concurrent Mark Sweep GC는 Initial Mark에서 짧은 Stop-The-World 발생을 시키고 Application Thread와 동시에 실행할 수 있게 Remark를 한다. 이러한 점에서 Garbage Collection이 비효율적인 편이다.

compact 작업도 포함되지 않아서 단편화 발생은 덤이다. 게다가 Major Garbage Collection이 발생하면 Application Thread들이 모두 멈춘 상태로 compact 작업이 이 쯤에 실행되고, 정지 기간도 길어진다. 그리고 Heap 크기가 4GB 이하인 메모리만 가능하다.

### G1 Garbage Collector

Java 7에서 제공되는 Garbage Collector이다. CMS Garbage Collector를 대체하기 위해 만들었고, Heap 크기가 늘어나는 점을 노린 Garbage Collection 작업을 위해 개발하였다.

CMS Garbage 작업과 달리 compact 작업을 포함한다. 그리고 새로운 영역을 통해 메모리에 집중적으로 접근하는 어플리케이션(Memory-Oriented Application)에 더 큰 Throughput을 제공한다.

## Thanks To
- 성공회대학교 소프트웨어공학과 **이승진 교수님**

## References
- https://d2.naver.com/helloworld/1329 - Garbage Collection 개념을 잘 설명한 Naver D2 포스트 참조

## Post Script
- 여기에 작성된 내용 이외에도 필요한 개념들을 발견하게 된다면 언제든지 갱신될 수 있습니다.
- 이 강의노트 개념에서 더욱 다뤄주면 좋은 개념들이나 오탈자가 있으시면 KangBakSa Issues에 올려주세요.