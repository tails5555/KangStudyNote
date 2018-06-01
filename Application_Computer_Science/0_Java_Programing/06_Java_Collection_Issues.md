# Java Collection Issues
Java에서 Collection 개념은 매우 중요하다.

Collection의 종류는 여러분이 생각한 종류보다 더욱 부지기수하게 존재한다. 

이번 강박사 노트에서는 Collection에 대하여 복습을 할 수 있는 기회를 가져보도록 하겠다.

참고로 여기서 다루는 개념은 기본 개념 보다는 **우리가 여태동안 몰랐던 Collection의 개념**을 주로 다룰 것이다. 기본 개념(예를 들어 메소드 이름과 특징 등)에 대해서는 이미 다 알고 있는 전제 하에 작성하겠다.

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
![train_play](/Application_Computer_Science/0_Java_Programing/img/train_play.png)

> List Interface는 데이터의 중복을 떠나서 모든 데이터들이 기차 놀이를 하는 개념으로 볼 수 있다.

List는 데이터를 순서에 맞게 일렬로 구성이 된다. 여기서는 인덱스가 부여되기 때문에 이를 이용한 검색이 가능하면서, 중복을 허용하는 것이 특징이다.

List의 종류는 ArrayList, LinkedList, Vector, Stack 등이 존재한다.

이 사실에 대해서는 자료 구조 시간에 졸지만 않았더라도 이미 다 아는 사실이다. 여기서 더욱 자세하게 어떤 차이점이 있는가를 정리해보도록 하자.

### ArrayList

```
int array_size = 10;
ArrayList<Integer> list1 = new ArrayList<Integer>(array_size);
for(int k=0;k<array_size;k++){
    list1.add(k);
}
list1.add(10); // 여기서는 런타임 오류가 안 나게 된다. 왜 그럴까?

ArrayList<Integer> list2 = new ArrayList<Integer>();
list2.add(10);
list2.add(20); // 일반적으로 우리가 써왔던 ArrayList의 방법은 이렇다.

ArrayList list3 = new ArrayList(); // 기본 사이즈인 10만큼 초기화 된다.
list3.add("가나다라");
list3.add(100);
list3.add(3.14);
list3.add(new Date()); 
// 제네릭을 쓰지 않아도 Object를 넣을 수 있다.
// 그러나 다운 캐스팅에 대한 보장을 할 수가 없다.
```

우리가 ArrayList를 실제로 생성만 해서 계속 추가만 해봤지, 실제로는 데이터의 크기를 고정하고 이용할 수 있는 사실을 잊고 넘어갔었다. 그렇지만 ArrayList에서는 배열의 크기를 초기화한 값만큼 늘려주는 private Method를 함유하고 있다. 이 Method를 `grow()`라고 한다. 

예를 들어 ArrayList의 크기를 10으로 초기화하고 10개의 데이터를 추가하고 난 다음에 새로운 데이터를 또 추가한다면, 크기의 한계를 벗어나기 위해 11로 알아서 1씩 키운다. 

ArrayList의 크기가 동적으로 추가되거나 삭제할 때 그 인덱스 다음에 있는 데이터를 밀거나 당기는 경우의 성능 저하의 발생 여부로 LinkedList보다 느릴 수도 있다는 사실을 생각해봐야 한다. 이 때 내부에서 이용하는 메소드가 바로 `System.arraycopy()`이다. 인스턴스를 생성할 필요가 없는 장점이 있어 메모리 자원 낭비는 막아주지만, 속도에 대해서는 보장을 못 하기 때문에 결국에는 LinkedList보다 느릴 수 밖에 없다.

또한 ArrayList 내부에 배열이란 자체가 존재해서 인덱스를 이용할 때 더욱 효과적인 List로도 뽑힌다.

### LinkedList

```
LinkedList<String> list1 = new LinkedList<String>();
list1.add("10"); // Node를 추가하고
list1.add("30"); // Node를 추가하고
list1.add("50"); // Node를 또 추가한다.
list1.add("70"); // Node를 계속 추가한다.
 
list1.contains("70"); // 이 때 Node를 얼마나 더 순회해야 할까?
list1.get(3); // 이 때 Node를 얼마나 더 순회해야 할까?
```

LinkedList는 ArrayList와 달리 배열이라는 자체가 존재하지 않아서 데이터 탐색에서 모든 데이터를 순회하고 봐야 하기 때문에 시간 복잡도는 O(n)인 점과 비교한다면 ArrayList(배열의 인덱스를 따오면 되니까 O(1))보다 비효율적이다.

그렇지만 LinkedList 만의 매력이 하나 있다면 데이터의 Index에 상관 없이 그 자리 이전 Node와 그 자리 이후 Node를 제대로 정리하면 되는 점에서 시간 복잡도가 O(1)로 나오게끔 구현이 되어 있어서 데이터 삽입, 삭제에서는 ArrayList보다 우월한 편이다. 

우리가 자료구조 시간에 LinkedList를 구현할 때 Index 별로 순회를 하고 추가, 삭제하는 것과는 달리 `Node Vertex`가 존재하여 이에 따른 작업을 진행하기 때문에 이전 Node와 이후 Node를 따지는 것만 기억해두면 된다.

또한 LinkedList는 Stack, Queue, Deque에서 데이터 삽입, 삭제 시에 Head와 Tail쪽에서 이뤄질 때 가장 적합한 자료구조가 바로 LinkedList이다. ArrayList와 비교한다면 다른 자료구조와의 확장성이 뛰어나다. 자세한 내용은 후술하겠다.

> **ArrayList VS LinkedList**
> 
> ArrayList와 LinkedList 둘 다 Serializable, Cloneable, Iterator Interface를 따와서 구현이 되어 있는 공통점이 있어서 이에 대해서 신경쓰진 않아도 된다.
> 
> 하지만 부지기수한 데이터에 따른다면 ArrayList와 LinkedList 중에서 어느 것을 써야 더욱 효과적일까? 
> 
> 아직까지 짜장면과 짬뽕 중 무엇을 먹을 것인가의 Issue와 같을 수도 있는데 데이터 변동 사항이 적은 경우에 탐색을 보장하는 측에서는 ArrayList를 사용하는 것이 효과적이고, 데이터 변동 사항이 비록 많지만 탐색을 하는 빈도가 적은 경우에 LinkedList를 쓰는 것이 좋은 방법이다.
> 
> 평소에는 ArrayList를 더욱 많이 쓰는 편이지만, 데이터가 많아지면 일일히 배열에 저장하기 때문에 공간 복잡도가 O(N)으로 커지는 단점이 있다. 즉 데이터 양이 적으면 ArrayList를 쓰는 것이 효율적이다.
> 
> 그렇지만 데이터 변동을 처음부터 끝까지 데이터를 탐색하면서 함께 작업하는 경우에는 LinkedList를 사용하는 것이 좋다.
> 
> 더욱 자세한 스펙은 아래 링크를 참고하면 좋겠다. 
> 
> http://javaconceptoftheday.com/arraylist-vs-linkedlist-java/

### Vector

```
Vector<Integer> vector = new Vector<Integer>(5);
vector.add(10);
vector.add(20);
...
// 50까지 추가했다면 ArrayList처럼 배열 크기를 알아서 늘리겠지...
vector.add(60);
vector.add(70);
...
// 여기까지만 두면 ArrayList와 별반 차이가 없는데??
```
> Java 1 버전부터 List 개념 조차 없었을 때 태어난 조상 격 개념.

Vector도 ArrayList와 마찬가지로 가변 길이 배열에 대응하기 위한 개념으로 볼 수 있다. ArrayList처럼 초기화했던 배열의 길이보다 커진다면 알아서 길이를 늘리는 개념과 다를 바가 전혀 없다. 도대체 ArrayList와는 무슨 차이가 존재하는 것일까?

Vector는 ArrayList와 달리 동기화를 보장하는 면에서 차이점을 볼 수 있다. ArrayList는 Singled-Thread에서 효율적으로 작동할 수 있게 구성한 자료구조이지만, Vector는 Multi-Thread에서 여러 데이터들이 각 다른 Thread 별로 요청이 들어온다면 그 순서에 맞춰 처리하는 특성을 가지고 있다.

이러한 이유는 ArrayList의 크기를 이미 범람하고 난 후에 데이터를 추가할 때 1씩 증가할 때 계속 들어오는 데이터에 대해 `grow()` 작업을 하는 면에서 비효율적이지만, Vector는 반대로 처음에 초기화한 배열의 크기 만큼 Capacity(용량)를 더욱 크게 잡는 면에서 동기화를 그나마 보장할 수 있기 때문이다.

이외의 차이점은 크게 없지만, 동기화를 보장하는 작업과 함께 이뤄지는 면에서 ArrayList보다는 Vector를 사용하는 것이 더욱 좋다.

### Stack

```
// Case 1번과 2번 동일한 방법이다.
// Case 1. Stack 클래스 이용
Stack<Integer> stack = new Stack<Integer>();
stack.push(10);
stack.push(20);
stack.push(30);

stack.peek(); // 30 반환

stack.pop(); // 30 반환 및 삭제
stack.pop(); // 20 반환 및 삭제

// Case 2. LinkedList 클래스 이용
LinkedList<Integer> stackList = new LinkedList<Integer>();
stackList.addLast(10);
stackList.addLast(20);
stackList.addLast(30);

stackList.peekLast();

stackList.removeLast();
stackList.removeLast();
```

Stack은 자료구조 시간에 `LIFO(Last In First Out)` 개념을 까먹지 않았다면 이미 다 아는 사실이다. Stack 개념은 백트래킹, DFS, 재귀함수 등에서 많이 쓰이는 개념이지만, Stack의 길이는 확실히 정해지지 않았다는 것을 알 수 있다. 그래서 Java Collection에서 Stack은 LinkedList를 기반으로 구현했다는 사실을 알 수 있다.

추가로 peek() 함수는 Stack 내부에 현재 맨 마지막에 있는 데이터를 읽어 들이기만 한다. 이외의 push(), pop() 함수는 똑같다.

> 참고로 1번 방법과 2번 방법은 유사하지만, 성능 면에서는 어떤 차이가 있는지 아시는 분은 Issues에 제보 해주시면 감사하겠습니다. 적극 반영 하겠습니다. :)

## Set

![diagram](/Application_Computer_Science/0_Java_Programing/img/diagram.jpg)

> 수학의 집합을 데이터로 표현한 것이 바로 Set의 개념이다.

Set는 List와 달리 순서를 정하면서 데이터를 추가하는 것은 둘 째로 치고, **데이터의 중복을 방지**하기 위한 자료 구조로 볼 수 있다.

Set의 종류로는 HashSet, TreeSet, LinkedHashSet 등이 있다.

Set의 이용 사례로 그래프 정점 별로 방문 처리 체크를 할 때 이용될 수 있다.

### HashSet

```
HashSet<String> persons = new HashSet<String>(); // *
persons.add("그사람");
persons.add("저사람");
persons.add("이사람");

Iterator<String> iterator = persons.iterator();
while(iterator.hasNext()) {
    String temp = iterator.next();
    System.out.println(temp); 
    // 어떤 것들이 먼저 나올지 예측할 수 없다.
}
```

HashSet는 HashCode를 기반으로 중복되지 않은 데이터를 저장할 때 사용한다. HashSet에 데이터 중복 확인을 위해서 객체 내부에 hashCode(), equals() 메소드가 포함되어 있어야 한다. 또한 null 값도 추가할 수 있다. 또한 Iterator를 이용해서 그 안에 있는 값들로 모든 데이터를 순회할 수 있지만, 순서에 대해서는 HashCode를 알지라도 예측이 불가능하다.

HashSet의 데이터 삽입, 삭제 시간 복잡도는 HashCode를 기반으로 삽입, 삭제가 진행되기 때문에 O(1)이 나오게 된다. 또한 이 내부에 있는 데이터 중의 일부를 수정할 수 없다.

그리고 *처럼 체크 된 부분에서 HashSet의 용량은 16, 적중률이 75%로 초기화가 되는데 적중률은 HashSet의 용량의 3/4 만큼 데이터가 저장 된다면 HashCode의 계산이 다시 진행 된다는 뜻이다.

### TreeSet

![RBTree](/Application_Computer_Science/0_Java_Programing/img/RBTree.png)

```
TreeSet<String> persons = new TreeSet<String>();
persons.add("이사람");
persons.add("저사람");
persons.add("그사람");
persons.add("거사람");
System.out.println(persons); // [그사람, 거사람, 이사람, 저사람]
```

TreeSet는 Red-Black Tree을 이용해 데이터의 중복을 방지하는 것과 동시에 HashCode 오름차순으로 추출할 수 있는 Sorted Sort의 일부이다. Red-Black Tree에서 데이터 삽입, 삭제의 시간 복잡도는 Red-Black Tree 법칙에 따른 순서 조정으로 인하여 Node 수가 적더라도 O(log n)이 나온다. 그렇지만 HashCode에 따라서 데이터를 정렬하기 때문에 오름차순으로 정리 된 데이터로 집합을 구성할 필요가 있다면 TreeSet가 효율적이다.

또한 TreeSet도 마찬가지로 Iterator를 가져올 수 있는데 HashCode를 기준으로 오름차순으로 정렬되어 반환한다.

### LinkedHashSet

## Queue

### LinkedList With Queue

### ArrayDeque

### PriorityQueue

## Map

### HashMap

### HashLinkedMap

### HashTable

### TreeMap

## Iterator

## Collection Default Methods

## Map Default Methods

## References
- http://egloos.zum.com/dojeun/v/317868 - Collection 개념에 대해 간략하게 정리된 사이트
- https://blog.naver.com/windziel/60048694876 - Collection 개념에 대하여 상세하게 잘 정리된 사이트
- https://brunch.co.kr/@springboot/57 - Collection 개념에 대하여 미처 알지 못한 개념들을 다룰 때 좋은 사이트