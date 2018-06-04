# Adapter Pattern Issues

![bus_photo](/Application_Computer_Science/8_Object_Oriented_Pattern/img/bus_photo.jpg)

아직 자가용이 없는 사람들은 어딘가로 이동할 때 탑승하는 객체가 바로 버스, 지하철, 택시이다. 대중 교통 가격은 과거에 10원, 20원 하던 시절이 있었지만, 요즘은 지역 별로 1300~1400원(현금, 성인 기준) 정도 한다. 다만, 현금으로 편도 운임을 지불한다면 환승 할인이 적용되지 않는다.

수도권 지역(경기도 지역, 서울 지역, 인천 지역) 교통 체계에서는 어느 버스 카드를 찍든 환승 할인이 적용되도록 구성되어 있다. 그렇지만 타 지방은 버스 카드 회사 별로 잘 안 찍히는 경우가 다반수일 뿐더러 환승 할인이 적용되지 않는 사례도 있다. 어떻게 본다면 환승 할인 혜택을 누릴 수 있게 도와주는 주체가 버스카드로 볼 수 있다. 

그래서 이번 시간에는 지역 별로 다른 버스 카드 인식을 빌미로 Adapter Pattern에 대해 공부해보는 시간을 가져보도록 하자.

## What Is Adapter Pattern?

![adapter_pattern_uml](/Application_Computer_Science/8_Object_Oriented_Pattern/img/adapter_pattern_uml.png)

Adapter Pattern은 소프트웨어 디자인 패턴 중에서 **구조 패턴(Structure Pattern)** 을 구성하는 방법 중 하나이다.

한 클래스의 Interface를 클라이언트 측에서 사용하기 위하여 다른 Interface로 변환 작업을 거쳐야 한다. 이를 이용하기 위해 Adapter를 사용한다면 Interface 호환성 문제 때문에 같이 쓸 수 없던 클래스도 연결해서 적용할 수 있다.

호환되지 않는 Interface를 사용하는 점에서 클라이언트와 구현된 Interface를 분리시킬 수 있으며, 나중에 Interface가 바뀌어도 변경 내역은 Adapter에 캡슐화(Encapsulation) 되기 때문에 Client 측에서는 바꿀 필요가 없어지는 장점이 있다.

어떻게 보면 지역 별 버스 카드 인식과 같은 개념으로 볼 수 있는데 경기도에서 잘 찍히던 :tea: 버스 카드가 타 지방에서 잘 안찍혀서 결국에는 :bee: 버스 카드로 찍어서 그 지역 내에서 환승 체계를 유지해서 교통비 절약에 도움이 되었다. 

이처럼 객체 지향에서도 서로 다른 Interface에 대하여 요구하는 형태의 Interface에 적응시키는 역할을 하는 것이 Adapter 개념이다.

## Example Of Adapter Pattern

이번에는 Adapter Pattern을 실생활의 사례로 접목시킨 소스 코드를 살펴보면서 어떻게 돌아가는지 감을 잡아보도록 하자. 아래와 같은 전제 조건을 따라서 이 패턴을 적용시켜서 완성 해봄으로서 이해하는 기회를 가져보자. 그리고 아래 소스 코드는 같은 Package에 있다고 가정하자.

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



```
public class MainClient {
    public static void main(String[] args){
        MetropolitanBusDevice mbd = new MetropolitanBusDevice();
        SecondaryBusDevice sbd = new SecondaryBusDevice();

        System.out.println("수도권 버스 디바이스에서 Tea 버스 카드로 찍습니다.");
        mbd.checkTea();
        mbd.balance(1250);

        System.out.println("지방 버스 디바이스에서 Bee 버스 카드로 찍습니다.");
        sbd.checkBee();
        sbd.balance(1300);

        TeaBusCard cardAdapter = new BeeBusCardAdapter(sbd);

        System.out.println("지방 버스 디바이스에서 Tea 버스 카드로 찍습니다.");
        cardAdapter.checkTea();
        cardAdapter.balance(1300);
    }
}
```
MainClient.java



## References
- https://ko.wikipedia.org/wiki/%EC%96%B4%EB%8C%91%ED%84%B0_%ED%8C%A8%ED%84%B4 - Adapter Pattern 한글 Wikipedia 참조. 내용이 너무 부실하여 영어 측을 읽어보길 권장한다.
- http://jusungpark.tistory.com/22 - Adapter Pattern 개념 참조