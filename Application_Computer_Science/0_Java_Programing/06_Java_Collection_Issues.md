# Java Collection Issues
Java에서 Collection 개념은 매우 중요하다.

Collection의 종류는 여러분이 생각한 종류보다 더욱 부지기수하게 존재한다. 

이번 강박사 노트에서는 Collection에서 다 못 다룬 개념에 대해서 복습을 철저히 하는 시간을 가져보도록 하자.

참고로 여기서 다루는 Colection 개념은 알아두면 좋은 개념들로만 구성하여 작성하겠다.

## What Is Collection And Map?

![car_parking](/Application_Computer_Science/0_Java_Programing/img/car_parking.jpg)

> Collection 개념은 한 구역에만 차를 주차할 수 있는 엘리베이터 주차장으로 볼 수 있다.

![kinds_trash](/Application_Computer_Science/0_Java_Programing/img/kinds_trash.jpeg)

> Map 개념은 분리수거함과 같은 개념으로 볼 수 있다.

Array의 단점은 고정적인 크기를 고려해야 한다. 예를 들어 아래와 같이 선언했다면 최소 10개의 int 형 변수를 고정적으로만 저장할 수 있다.

```
int[] arr = new int[10];
arr = {10, 20, 30, 40, 50, 60, 70, 80, 90, 100};
```

하지만 가변적인 데이터의 목록을 어딘가에 모아야 할 필요가 있다. 데이터를 모을 때 순서를 고려해야 하는가, 무작위로 넣어둬도 상관이 없나, 중복된 데이터를 저장할 수 있는가로 나뉘게 된다. 이러한 점을 빌미로 **단일 데이터에 대한 모음**을 구현한 개념을 Collection이라고 한다. 

그렇지만 Collection을 이용할 때 순서를 고려하는 점에서 무조건 자연수(N>0 인 정수, 정수론에서는 0도 가끔 자연수로 취급하는 경우도 있다.)로 취급하는 단점이 있다. 문자열, 객체 등으로 Key 값을 저장할 때 중복된 Value를 저장할 수 있는 개념이 바로 Map이다. 여기서 Collection과 Map의 개념은 별개의 개념으로 생각하고 넘어가야 한다.

## Kinds Of Collection Interface

![Collection_interfaces](/Application_Computer_Science/0_Java_Programing/img/Collection_interfaces.png)

Collection Interface는 3가지로 분류되는데 List, Set, Queue로 나뉘게 된다. 그리고 Map Interface는 별개로 존재한다.

Collection Interface은 데이터의 순서 열람(Iteration)을 반영하기 위해 Iterable Interface로 부터 상속을 받고 이용한다. 반대로 Map Interface에는 순서에 대해 무작위로 저장할 수 있는 Hash Code를 이용해 중복된 Key 값을 배제한다.

각 Interface를 구현한 클래스들 중에서 위의 그림에 있는 Implementation Class들에 대해서만 다루겠다. 이외에도 여러가지가 있는데 이 정도까지만 알아둔다면 큰 약이 된다.

## List

## References
- http://egloos.zum.com/dojeun/v/317868 - Collection 개념에 대해 간략하게 정리된 사이트
- https://blog.naver.com/windziel/60048694876 - Collection 개념에 대하여 상세하게 잘 정리된 사이트