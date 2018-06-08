# Factory Pattern Issues

![paper_doll](/Application_Computer_Science/8_Object_Oriented_Pattern/img/paper_doll.jpg)

초등학생 때 준비물 사러 문방구를 갈 때, 종이 인형 옷 입히기를 본 적이 있을 것이다. 그 안에는 옷을 입을 캐릭터와 모자, 상의, 하의, 기타 장식품 등이 포함되어 있다. 이 놀이를 즐기기 위해 가위로 오리거나 커터 칼로 자르거나 아니면 절취선에 따라 잘라낸 후에, 종이 인형을 세우고 옷 종류 별로 올리면서 다양한 방식으로 나만의 캐릭터를 만들 수 있다.

갑자기 왜 이 이야기가 나왔을까? Factory Pattern에서 클래스의 인스턴스 만드는 것을 서브 클래스에서 결정하는 사례와 유사하기 때문이다. 

Singleton Pattern에서는 하나의 인스턴스로 오로지 하나의 객체를 만들지만, 

## What Is Factory Pattern?

Factory Pattern은 소프트웨어 디자인 패턴 중에서 **생성 패턴(Constructor Pattern)** 을 구성하는 방법 중 하나이다. 모든 Factory Pattern에서는 객체 생성을 캡슐화하는 특징이 있다.

객체 지향 디자인 원칙 중에서 **추상화된 것에 의존하도록 만들어야지, 구상 클래스에 의존하면 안 되는** 것을 충족시키는 패턴으로 볼 수 있다.

이를 더욱 상세하게 설명한다면, 추상 클래스(Abstract Class)와 Interface에 맞춰 코딩할 수 있게 도와준다. Application 내부에서 실제로 쓰는 클래스와의 의존성을 줄여줌으로서 **느슨한 결합(Loose Coupling)** 이 이뤄짐으로서 객체 지향 프로그래밍에 충족시킨다.

Spring Framework에서 IoC(제어의 역전) 특성의 대표적인 의존성 주입(Dependency Injection. DI)에 해당되는데 이는 구상 클래스에 대한 의존성을 줄어주는 역할로 볼 수 있다.

**구상 클래스(Concreate Class)**

이 용어가 생각보다 낫설 것이다. 추상 클래스(Abstract Class)는 abstract method가 하나라도 있고, 인스턴스를 생성하지 않는 클래스이다. 반대로, 구상 클래스는 **실제로 쓰일 오퍼레이션(Operation, Java에서는 Method)을 모두 작성하고, 인스턴스를 생성할 목적으로 만드는** 클래스이다. 

## Kinds of Factory Patterns

Factory Pattern의 종류는 크게 Factory Method Pattern과 Abstract Factory Pattern으로 나뉜다.

이 2가지를 직접 구현하면서 어떤 차이점이 있는지 알아보자.

### Factory Method Pattern

![factory_method_uml](/Application_Computer_Science/8_Object_Oriented_Pattern/img/factory_method_uml.png)

### Abstract Factory Pattern

![abstract_factory_uml](/Application_Computer_Science/8_Object_Oriented_Pattern/img/abstract_factory_uml.png)


## Example

### Factory Method Example

Factory Method를 간략하게 실습하기 위한 클래스 다이어그램은 다음과 같다. 방금 전에 언급했던 종이 옷 입히기를 Factory Method Pattern을 이용해서 간략하게 구현하면 다음과 같다.

![AbstractCostume](/Application_Computer_Science/8_Object_Oriented_Pattern/img/AbstractCostume.png)

패키지는 net.kang.factory.factory_method.costume이다.

![CostumeFactory](/Application_Computer_Science/8_Object_Oriented_Pattern/img/CostumeFactory.png)

패키지는 net.kang.factory.factory_method.factory이다.

```
package net.kang.factory.factory_method.costume;

public abstract class AbstractCostume {
    public abstract void putOn();
    public abstract String getName();
    public abstract String getColor();
}
```
AbstractCostume.java


```
package net.kang.factory.factory_method.costume;

public enum CostumeType {
    DRESS, SHIRT, PANTS, SHOES, HAT
}
```
CostumeType.java


```
package net.kang.factory.factory_method.costume;

public class ShirtObject extends AbstractCostume{
    private String name;
    private String color;

    public ShirtObject(String name, String color){
        this.name = name;
        this.color = color;
    }

    @Override
    public String getName(){
        return this.name;
    }

    @Override
    public String getColor(){
        return this.color;
    }

    @Override
    public void putOn(){
        System.out.println(String.format("[상의] [%s] - [%s] 색상을 입히겠습니다.", this.name, this.color));
    }
}
```
ShirtObject.java



```
package net.kang.factory.factory_method.client;

import net.kang.factory.factory_method.costume.AbstractCostume;
import net.kang.factory.factory_method.factory.TypeCostumeFactory;

import static net.kang.factory.factory_method.costume.CostumeType.*;

public class MainClient {
    public static void main(String[] args){
        TypeCostumeFactory typeCostumeFactory = new TypeCostumeFactory();

        System.out.println("캐릭터 A에게 다음과 같은 옷을 입혀보겠습니다.");

        AbstractCostume costume01 = typeCostumeFactory.createCostume(SHIRT, "A사 티셔츠", "파란색");
        AbstractCostume costume02 = typeCostumeFactory.createCostume(PANTS, "B사 바지", "검은색");
        AbstractCostume costume03 = typeCostumeFactory.createCostume(SHOES, "A사 운동화", "베이지색");

        costume01.putOn();
        costume02.putOn();
        costume03.putOn();

        System.out.println("캐릭터 B에게 다음과 같은 옷을 입혀보겠습니다.");

        AbstractCostume costume04 = typeCostumeFactory.createCostume(HAT, "C사 페도라", "연녹색");
        AbstractCostume costume05 = typeCostumeFactory.createCostume(DRESS, "B사 드레스", "연녹색");
        AbstractCostume costume06 = typeCostumeFactory.createCostume(SHOES, "B사 구두", "하얀색");

        costume04.putOn();
        costume05.putOn();
        costume06.putOn();
    }
}
```
MainClient.java

```
캐릭터 A에게 다음과 같은 옷을 입혀보겠습니다.
[상의] [A사 티셔츠] - [파란색] 색상을 입히겠습니다.
[바지] [B사 바지] - [검은색] 색상을 입히겠습니다.
[신발] [A사 운동화] - [베이지색] 색상을 신기겠습니다.
캐릭터 B에게 다음과 같은 옷을 입혀보겠습니다.
[모자] [C사 페도라] - [연녹색] 색상을 씌우겠습니다.
[드레스] [B사 드레스] - [연녹색] 색상을 입히겠습니다.
[신발] [B사 구두] - [하얀색] 색상을 신기겠습니다.
```
MainClient.java를 실행한 결과이다.

### Abstract Factory Example

```
```

```
```

```
```
## Comparison of Another Patterns

## References

- http://whereami80.tistory.com/211 - Factory Pattern 종류 별 차이점 자세히 설명함
- http://friday.fun25.co.kr/blog/?p=280 - Factory Pattern Example 참조

## Post Script
- 여기에 작성된 내용 이외에도 필요한 개념들을 발견하게 된다면 언제든지 갱신될 수 있습니다.
- 이 강의노트 개념에서 더욱 다뤄주면 좋은 개념들이나 오탈자가 있으시면 KangBakSa Issues에 올려주세요.
- 그리고 객체 지향 패턴은 Design_Pattern Repository에 추가로 저장하고 있습니다. 나중에 완성되면, public으로 바꿔 소스 코드와 연동하겠습니다. 