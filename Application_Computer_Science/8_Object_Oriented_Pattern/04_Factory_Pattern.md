# Factory Pattern Issues

![paper_doll](/Application_Computer_Science/8_Object_Oriented_Pattern/img/paper_doll.jpg)

초등학생 때 준비물 사러 문방구를 갈 때, 종이 인형 옷 입히기를 본 적이 있을 것이다. 그 안에는 옷을 입을 캐릭터와 모자, 상의, 하의, 기타 장식품 등이 포함되어 있다. 이 놀이를 즐기기 위해 가위로 오리거나 커터 칼로 자르거나 아니면 절취선에 따라 잘라낸 후에, 종이 인형을 세우고 옷 종류 별로 올리면서 다양한 방식으로 나만의 캐릭터를 만들 수 있다.

갑자기 왜 이 이야기가 나왔을까? Factory Pattern에서 클래스의 인스턴스 만드는 것을 서브 클래스에서 결정하는 사례와 유사하기 때문이다. 종이 인형 옷 입히기에는 옷 종류 별로 다양한 옷들이 존재한다. 그래서 옷 입히기를 할 때 옷 종류 별로 정리를 하면 자신이 원하는 옷을 찾을 수 있다. 마찬가지로 구상 클래스에서 필요한 자식 객체를 추상적으로 정리한 객체를 신경 써서 정리한다면, 나중에 구상 클래스의 이용 비중을 더욱 높일 수 있다.

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

팩토리 메소드 패턴(Factory Method Pattern)은 부모 클래스에 알려지지 않은 구상 클래스를 생성하는 패턴이다. ConcreteCreator 클래스가 어떤 객체를 생성하는 결정적인 역할을 한다. 즉 어느 객체를 생성할까의 여부는 자식 클래스가 가지고, 부모 클래스는 구상 클래스의 이름을 감추기 위해 사용하는 방법이다.

#### Factory Method Example

Factory Method를 간략하게 실습하기 위한 클래스 다이어그램은 다음과 같이 구성된다.

![Factory_Method_Structure](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Factory_Method_Structure.png)

방금 전에 언급했던 종이 옷 입히기를 Factory Method Pattern을 적용해 짧게 구현하면 다음과 같다.

```
package net.kang.factory.factory_method.abstract_object;

public abstract class AbstractCostume {
    public abstract void putOn();
    public abstract String getName();
    public abstract String getColor();
}
```
AbstractCostume.java

AbstractCostume 추상 클래스는 의상 객체를 만들 때 의상이 하는 모든 행위를 기재하였다. 여기서 putOn() 메소드는 의상을 입거나 신거나 씌우는 행위, getName(), getColor() 메소드는 의상 객체의 구체적인 이름, 색상을 반환한다.

```
package net.kang.factory.factory_method.costume_object;

public enum CostumeType {
    DRESS, SHIRT, PANTS, SHOES, HAT
}
```
CostumeType.java

CostumeType Enumeration 클래스는 의상의 종류이다. DRESS는 모든 드레스 종류, SHIRT는 위에 있는 상의 종류, PANTS는 아래를 감싸는 하의 종류, SHOES는 발에 들어가면 다 신기는 종류, HAT는 머리 위에 씌우면 되는 모자 종류로 정하자.

```
package net.kang.factory.factory_method.costume_object;

import net.kang.factory.factory_method.abstract_object.AbstractCostume;

public class ShirtObject extends AbstractCostume {
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

각 의상 Object는 구체적인 의상 이름(name)과 색상(color)를 맴버 변수로 정의한다. 의상의 행위는 AbstractCostume 클래스를 상속시켜 모든 행위를 구현한다. DRESS(DressObject), SHIRT(ShirtObject), HAT(HatObject), SHOES(ShoesObject), PANTS(PantsObject) 별로 작성하고, 마지막으로 의상의 종류를 알 수 없다면 CostumeObject로 구현한다.

```
package net.kang.factory.factory_method.client;

import net.kang.factory.factory_method.abstract_object.AbstractCostume;
import net.kang.factory.factory_method.factory_object.TypeCostumeFactory;

import static net.kang.factory.factory_method.costume_object.CostumeType.*;

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

MainClient 클래스에서는 캐릭터 A에게 상의, 바지, 신발을 입히고, 캐릭터 B에게 모자, 드레스, 신발을 입힌다. 실행 결과는 아래와 같다.

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

여기서 가장 중요한 핵심을 결정하면, 인스턴스 생성을 자식 클래스에게 위임을 하는 것이다. MainClient에서는 new 키워드를 1도 안 썼다. 그래서 MainClient는 어떤 종류의 객체가 생성되었는지 알 수 없다. Costume 관련 함수 변경이 일어난다면, AbstractCostume 클래스에서 처리하면 되기 때문에 구상 클래스에서는 자식 클래스의 결합도(Coupling)를 줄일 수 있다.

그러나 Factory Method가 중첩되면 굉장히 복잡해지고, 상속을 사용하지만 부모 클래스를 전혀 확장하지 않았기 때문에 extends를 너무 많이 사용하면 프로그램의 엔트로피가 늘어난다.

엔트로피는 열역학에서 유용하지 않은 에너지의 흐름을 뜻하는 과학 용어이지만, 쉽게 생각하면 프로그램에서 추상 클래스를 너무 많이 적용시켜서 구상 클래스의 역할이 줄어드는 의미로 생각하면 된다.

### Abstract Factory Pattern

![abstract_factory_uml](/Application_Computer_Science/8_Object_Oriented_Pattern/img/abstract_factory_uml.png)

추상 팩토리 패턴(Abstract Factory Pattern)은 서로 연관되거나 의존하는 객체를 구상 클래스를 지정하지 않고도 생성할 수 있는 개념이다.

짧게 설명하면, 방금 전에는 Abstract Class를 이용했지만, 이번에는 이를 Interface와 객체 별 Factory 클래스로 대체한다는 뜻이다.

방금 전에 작성한 Abstract Class(추상 클래스)를 이번에는 Interface와 객체 별 Factory 클래스로 바꿔서 적용시켜보자.

### Abstract Factory Example

Abstract Factory 를 간략하게 실습하기 위한 클래스 다이어그램은 다음과 같이 구성된다.

![Abstract_Factory_Structure](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Abstract_Factory_Structure.png)

방금 전의 사례로 Abstract Factory Pattern을 적용해 짧게 구현하면 다음과 같다.

```
package net.kang.factory.abstract_factory.abstract_object;

public abstract class AbstractCostume {
    public abstract void putOn();
    public abstract String getName();
    public abstract String getColor();
}

```
AbstractCostume.java

```
package net.kang.factory.abstract_factory.costume_object;

import net.kang.factory.abstract_factory.abstract_object.AbstractCostume;

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

AbstractCostume, ShirtObject 클래스는 방금 전에 있는 그대로 사용할 것이다.

```
package net.kang.factory.abstract_factory.costume;

import net.kang.factory.abstract_factory.abstract_object.AbstractCostume;

public interface AbstractCostumeFactory {
    public AbstractCostume createCostume(String name, String color);
}
```
AbstractCostumeFactory.java

AbstractCostumeFactory는 Interface로 적용시켜서, 모든 의상 객체 별로 Factory를 만들 때 이를 상속 시켜서 구현하게 만들었다.

```
package net.kang.factory.abstract_factory.costume;

import net.kang.factory.abstract_factory.abstract_object.AbstractCostume;
import net.kang.factory.abstract_factory.costume_object.ShirtObject;

public class ShirtFactory implements AbstractCostumeFactory {
    @Override
    public AbstractCostume createCostume(String name, String color){
        return new ShirtObject(name, color);
    }
}
```
ShirtFactory.java

ShirtObject 객체의 Factory는 ShirtFactory로 구성하였다. createCostume 메소드를 이용하여 상의 객체를 반환 시킨다.

```
package net.kang.factory.abstract_factory.factory_object;

import net.kang.factory.abstract_factory.abstract_object.AbstractCostume;
import net.kang.factory.abstract_factory.costume.AbstractCostumeFactory;

public class CostumeFactory {
    static public AbstractCostume getCostume(AbstractCostumeFactory abstractCostumeFactory, String name, String color){
        return abstractCostumeFactory.createCostume(name, color);
    }
}
```
CostumeFactory.java


```
package net.kang.factory.abstract_factory.client;

import net.kang.factory.abstract_factory.abstract_object.AbstractCostume;
import net.kang.factory.abstract_factory.costume.DressFactory;
import net.kang.factory.abstract_factory.costume.HatFactory;
import net.kang.factory.abstract_factory.costume.PantsFactory;
import net.kang.factory.abstract_factory.costume.ShirtFactory;
import net.kang.factory.abstract_factory.costume.ShoesFactory;
import net.kang.factory.abstract_factory.factory_object.CostumeFactory;

public class MainClient {
    public static void main(String[] args){
        AbstractCostume dress = CostumeFactory.getCostume(new DressFactory(), "웨딩드레스", "하얀색");
        AbstractCostume shirt = CostumeFactory.getCostume(new ShirtFactory(), "맨투맨", "줄무늬");
        AbstractCostume pants = CostumeFactory.getCostume(new PantsFactory(), "반바지", "파란색");
        AbstractCostume shoes = CostumeFactory.getCostume(new ShoesFactory(), "슬리퍼", "검은색");
        AbstractCostume hat = CostumeFactory.getCostume(new HatFactory(), "일리네어 스냅백", "하얀색");

        dress.putOn();
        shirt.putOn();
        pants.putOn();
        shoes.putOn();
        hat.putOn();
    }
}
```
MainClient.java



## Comparison of Another Patterns

## References

- http://whereami80.tistory.com/211 - Factory Pattern 종류 별 차이점 자세히 설명함
- http://friday.fun25.co.kr/blog/?p=280 - Factory Pattern Example 참조
- https://ko.wikipedia.org/wiki/%ED%8C%A9%ED%86%A0%EB%A6%AC_%EB%A9%94%EC%84%9C%EB%93%9C_%ED%8C%A8%ED%84%B4 - Factory Method Pattern 위키 백과 참조
- https://ko.wikipedia.org/wiki/%EC%B6%94%EC%83%81_%ED%8C%A9%ED%86%A0%EB%A6%AC_%ED%8C%A8%ED%84%B4 - Abstract Factory Pattern 위키 백과 참조


## Post Script
- 여기에 작성된 내용 이외에도 필요한 개념들을 발견하게 된다면 언제든지 갱신될 수 있습니다.
- 이 강의노트 개념에서 더욱 다뤄주면 좋은 개념들이나 오탈자가 있으시면 KangBakSa Issues에 올려주세요.
- 그리고 객체 지향 패턴은 Design_Pattern Repository에 추가로 저장하고 있습니다. 나중에 완성되면, public으로 바꿔 소스 코드와 연동하겠습니다. 