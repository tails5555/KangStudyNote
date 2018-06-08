# Adapter Pattern Issues

![bus_photo](/Application_Computer_Science/8_Object_Oriented_Pattern/img/bus_photo.jpg)

아직 자가용이 없는 사람들은 어딘가로 이동할 때 탑승하는 객체가 바로 버스, 지하철, 택시이다. 대중 교통 가격은 과거에 10원, 20원 하던 시절이 있었지만, 요즘은 지역 별로 1300~1400원(현금, 성인 기준) 정도 한다. 다만, 현금으로 편도 운임을 지불한다면 환승 할인이 적용되지 않는다.

수도권 지역(경기도 지역, 서울 지역, 인천 지역) 교통 체계에서는 어느 버스 카드를 찍든 환승 할인이 적용되도록 구성되어 있다. 그렇지만 타 지방은 버스 카드 회사 별로 잘 안 찍히는 경우가 다반수일 뿐더러 환승 할인이 적용되지 않는 사례도 있다. 어떻게 본다면 환승 할인 혜택을 누릴 수 있게 도와주는 주체가 버스카드로 볼 수 있다. 

그래서 이번 시간에는 지역 별로 다른 버스 카드 인식을 빌미로 Adapter Pattern에 대해 공부해보는 시간을 가져보도록 하자.

## What Is Adapter Pattern?

Adapter Pattern은 소프트웨어 디자인 패턴 중에서 **구조 패턴(Structure Pattern)** 을 구성하는 방법 중 하나이다.

한 클래스의 Interface를 클라이언트 측에서 사용하기 위하여 다른 Interface로 변환 작업을 거쳐야 한다. 이를 이용하기 위해 Adapter를 사용한다면 Interface 호환성 문제 때문에 같이 쓸 수 없던 클래스도 연결해서 적용할 수 있다.

호환되지 않는 Interface를 사용하는 점에서 클라이언트와 구현된 Interface를 분리시킬 수 있으며, 나중에 Interface가 바뀌어도 변경 내역은 Adapter에 캡슐화(Encapsulation) 되기 때문에 Client 측에서는 바꿀 필요가 없어지는 장점이 있다.

어떻게 보면 지역 별 버스 카드 인식과 같은 개념으로 볼 수 있는데 경기도에서 잘 찍히던 :tea: 버스 카드가 타 지방에서 잘 안찍혀서 결국에는 :bee: 버스 카드로 찍어서 그 지역 내에서 환승 체계를 유지해서 교통비 절약에 도움이 되었다. 

이처럼 객체 지향에서도 서로 다른 Interface에 대하여 요구하는 형태의 Interface에 적응시키는 역할을 하는 것이 Adapter 개념이다.

## Example Of Adapter Pattern

Adapter Pattern의 구조는 Object Adapter(단방향 접속자), Class Adapter(양방향 접속자) 2가지로 나뉜다. 여기서는 Object Adapter에 대한 예제만 작성을 하였다.

이번에는 Adapter Pattern을 실생활의 사례로 접목시킨 소스 코드를 살펴보면서 어떻게 돌아가는지 감을 잡아보도록 하자. 아래와 같은 전제 조건을 따라서 이 패턴을 적용시켜서 완성 해봄으로서 이해하는 기회를 가져보자. 그리고 아래 소스 코드는 같은 Package에 있다고 가정하자.

### Object Adapter Example

![adapter_pattern_uml](/Application_Computer_Science/8_Object_Oriented_Pattern/img/adapter_pattern_uml.png)

TeaBusCard, BeeBusCard - 각 지역 별로 사용할 수 있는 버스 카드 Interface

MetropolitanBusDevice, SecondaryBusDevice - 수도권, 타 지방 버스 카드 단말기 클래스로 수도권 버스 카드 단말기는 TeaBusCard를 이용해서 찍을 수 있고, 타 지방 버스 카드 단말기는 BeeBusCard를 이용해서 찍을 수 있다.

BeeBusCardAdapter - TeaBusCard를 이용하는 고객을 위하여 BeeBusCard를 이용하는 단말기에 일종의 Adapter를 추가시켜 TeaBusCard로 체킹하고, BeeBusCard 체계로 인식 시킨다.<br/>

```
public interface TeaBusCard {
    public void checkTea();
    public void balance(int paid);
}
```
TeaBusCard.java

```
public interface BeeBusCard {
    public void checkBee();
    public void balance(int paid);
}
```
BeeBusCard.java

TeeBusCard, BeeBusCard Interface의 공통적인 역할은 버스 카드 체킹과 잔액 조회 기능만 제공한다고 가정하자. 다만 버스 카드 체킹을 하는 메소드가 각각 checkTea(), checkBee()로 다르게 인식을 한다는 점을 감안해야 한다.<br/><br/>

```
public class MetropolitanBusDevice implements TeaBusCard {
    @Override
    public void checkTea(){
        System.out.println("Tea 카드 - 체크되었습니다.");
    }
    @Override
    public void balance(int paid){
        System.out.println(String.format("Tea 카드 - %d원이 차감되었습니다.", paid));
    }
}
```
MetropolitanBusDevice.java

```
public class SecondaryBusDevice implements BeeBusCard{
    @Override
    public void checkBee(){
        System.out.println("Bee 카드 - 체크되었습니다.");
    }
    @Override
    public void balance(int paid){
        System.out.println(String.format("Bee 카드 - %d원이 차감되었습니다.", paid));
    }
}
```
SecondaryBusDevice.java

수도권 지역 버스 카드 단말기, 타 지방 버스 카드 단말기는 각각 TeaBusCard, BeeBusCard Interface를 상속 받아서 각 단말기 내부에서 버스 카드 Interface에서 요구하는 기능을 구현하였다.<br/><br/>

```
public class BeeBusCardAdapter implements TeaBusCard{
    BeeBusCard beeBusCard;

    public BeeBusCardAdapter(BeeBusCard beeBusCard){
        this.beeBusCard = beeBusCard;
    }

    @Override
    public void checkTea(){
        System.out.println("[Tea 카드 체계에서 Bee 카드 체계로 바뀝니다.]");
        this.beeBusCard.checkBee();
    }

    @Override
    public void balance(int paid){
        System.out.println("[Tea 카드 체계에서 Bee 카드 체계로 바뀝니다.]");
        this.beeBusCard.balance(paid);
    }
}
```
BeeBusCardAdapter.java

BeeBusCardAdapter 클래스는 수도권에서 타 지방으로 이동할 때 그 곳에서 TeaBusCard 밖에 없는 상황이 다가옴을 대비해 Adapter를 이용해서 TeaBusCard 체계를 BeeBusCard 체계로 변동시킨 후 버스 카드 인식에 대한 불편함을 해소한다.<br/><br/>

```
public class MainClient {
    public static void main(String[] args){
        MetropolitanBusDevice mbd = new MetropolitanBusDevice();
        SecondaryBusDevice sbd = new SecondaryBusDevice();

        // Case 01. 수도권 버스에서 TeaBusCard를 이용하는 경우
        System.out.println("수도권 버스 디바이스에서 Tea 버스 카드로 찍습니다.");
        mbd.checkTea();
        mbd.balance(1250);

        // Case 02. 타 지방 버스에서 BeeBusCard를 이용하는 경우
        System.out.println("지방 버스 디바이스에서 Bee 버스 카드로 찍습니다.");
        sbd.checkBee();
        sbd.balance(1300);

        // Case 03. 타 지방 버스에서 TeaBusCard를 이용할 때 BeeBusCard 체계로 변동시켜 사용해야 하는 경우
        TeaBusCard cardAdapter = new BeeBusCardAdapter(sbd);

        System.out.println("지방 버스 디바이스에서 Tea 버스 카드로 찍습니다.");
        cardAdapter.checkTea();
        cardAdapter.balance(1300);
    }
}
```
MainClient.java

MainClient에 대하여 실행 결과는 아래와 같이 나온다.
```
수도권 버스 디바이스에서 Tea 버스 카드로 찍습니다.
Tea 카드 - 체크되었습니다.
Tea 카드 - 1250원이 차감되었습니다.
지방 버스 디바이스에서 Bee 버스 카드로 찍습니다.
Bee 카드 - 체크되었습니다.
Bee 카드 - 1300원이 차감되었습니다.
지방 버스 디바이스에서 Tea 버스 카드로 찍습니다.
[Tea 카드 체계에서 Bee 카드 체계로 바뀝니다.]
Bee 카드 - 체크되었습니다.
[Tea 카드 체계에서 Bee 카드 체계로 바뀝니다.]
Bee 카드 - 1300원이 차감되었습니다.
```
<br/>

이처럼 기능이 서로 다른 Interface에 대한 호환성을 보장할 수 있는 최선의 방법이 Object Adapter를 적용한 방법이다. 위의 Class Diagram에 따른 최종 정리는 다음과 같다.

- Target Interface(타깃 Interface. 여기서는 A에 해당된다.) - `TeaBusCard` Interface
- Adapter(어댑터, A Interface에서 B Interface를 사용하는 Object로 연결하는 도구) - `BeeBusCardAdapter` 객체
- Adaptee Object(B Interface를 사용하는 Object) - `SecondaryBusDevice` 객체

이원적인 순서(A to B, B to C... Then A to C)를 생각하면 구상 방법에 대해 크게 어렵지 않을 것이다. 그러면 어떤 방법으로 돌아가는지에 대해 이 소스 코드 Case 03의 순서를 살펴보도록 한다.

> 1. Client 측에서 Target Interface를 이용하여 Adapter를 생성하되 생성자 매개 변수에 Adaptee Object인 `SecondaryBusDevice` 객체 인스턴스를 넣어준다.
> 2. 이제 Adapter(`BeeBusCardAdapter`)를 이용해서 실행할 수 있는 메소드는 checkTea(), balance(int) 를 실행할 수 있다.
> 3. 이제 Adapter Interface를 이용해서 위 메소드에 대한 요청을 Adaptee(`SecondaryBusDevice`)에 대한 1개 이상의 메소드를 호출로 반환하게 된다.
> 4. Client 측에서는 호출 결과를 받지만 중간에 어댑터가 있는지 여부를 확인할 수 없다.<br/> 
> 혹시나 이를 분석하는 사람이 있을까? 본인이 수도권에서 잘 찍히는 버스 카드 단말기를 타 지방 버스 카드 단말기에서 찍을 때 삐빅 소리가 정상적으로 나는 원리를 짐작할 수 있을지...??

Object Adapter에 대한 원리에 감이 잡혔을 때, Adapter 클래스가 1개만 존재해도 많은 Adaptee 클래스와 동작을 할 수 있게 보장할 수 있는 장점이 있다. 그렇지만 Adaptee에 정의된 행위 재정의는 어렵다.

### Class Adapter Example

![adapter_pattern_uml2](/Application_Computer_Science/8_Object_Oriented_Pattern/img/adapter_pattern_uml2.png)

> 은 Java Programming에서 구현할 수 있는 방법이 없다.
>
> 왜냐면 Java Class 정책에서 다중 상속(Multi Inheritance) 기능을 제공하지 않기 때문이다.

Class Adapter는 Adapter를 만들 때 Target Class, Adaptee Class를 모두 만들어 사용한다. 그렇지만 다중 상속이 불가능한 프로그래밍에서는 적용이 불가능하다. 

하지만 Class Adapter를 이용한다면 Object Adapter와 달리 Adaptee에 정의된 행위를 재정의할 수 있다. 또한 객체만 만들면 되기 때문에 어떠한 인스턴스도 필요 없다.

## Comparison of Another Patterns

- Bridge Pattern - Adapter Pattern과 유사하지만, 실상 사용 목적이 다르다. Adapter Pattern은 존재한느 객체의 인터페이스를 변경하는 것이고, Bridge Pattern은 구현과 추상 개념을 분리해 서로에게 영향을 주지 않고 확장이 가능하게 하는 목적을 가진다.

- Decorator Pattern - 다른 Interface의 변경이 없더라도 객체에 새로운 행위를 추가할 수 있도록 한다. 순수한 Adapter Pattern에서는 재귀적 합성이 불가능하지만, Decorator Pattern에서 이를 가능하게 한다.

- Proxy Pattern - 다른 객체에 대한 대리인의 역할을 수행하지만, Interface를 변경하는 책임이 없다. 마치 지난 번에 짱구 만화 내용을 토대로 말썽 부리는 날이 하루도 빠짐 없는 건 여전하지, 자신이 변하려고 노력하는 모습은 옴니버스 만화 구성 상 빈도가 적은 걸 연상하면 쉽다.

## References
- https://ko.wikipedia.org/wiki/%EC%96%B4%EB%8C%91%ED%84%B0_%ED%8C%A8%ED%84%B4 - Adapter Pattern 한글 Wikipedia 참조. 내용이 너무 부실하여 영어 측을 읽어보길 권장한다.
- http://jusungpark.tistory.com/22 - Adapter Pattern 개념 참조
- http://www.jidum.com/jidums/view.do?jidumId=991 - Adapter Pattern 내용 정리 참조

## Post Script
- 여기에 작성된 내용 이외에도 필요한 개념들을 발견하게 된다면 언제든지 갱신될 수 있습니다.
- 이 강의노트 개념에서 더욱 다뤄주면 좋은 개념들이나 오탈자가 있으시면 KangBakSa Issues에 올려주세요.
- 그리고 객체 지향 패턴은 Design_Pattern Repository에 추가로 저장하고 있습니다. 나중에 완성되면, public으로 바꿔 소스 코드와 연동하겠습니다. 