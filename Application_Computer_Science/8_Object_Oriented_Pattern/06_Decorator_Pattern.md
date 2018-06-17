# Decorator Pattern Issues

![BooDaeChiGae](/Application_Computer_Science/8_Object_Oriented_Pattern/img/BooDaeChiGae.jpeg)

갑자기 배고프다. 배고플 때에는 한 번 부대찌개 만들어 먹는 도구 하나에 콜라 한 잔 곁들여 먹으면 한 끼 식사로 딱이다. 그렇지만 부대찌개의 중요 재료는 무엇보다도 스팜과 외국산 소시지(흔히 부대 고기라고 한다.), 콩 통조림, 슬라이스 치즈를 딱 올려 먹고 거기 안에 라면 사리를 끓여 먹으면 좋다. 그렇지만 부대찌개에 떡을 넣는다면 국물이 퍼져서 맛 없어서 잘 안 넣어 먹는다.

이처럼 Decorator Pattern은 부대찌개에 본인의 입 맛에 맞게 중간에 다른 재료를 추가해서 끓여 먹듯이, 구현 객체에서 자기가 필요한 행위를 동적으로 추가해서 이용하는 방법이다.

이번 노트에서는 Decorator Pattern을 다뤄보도록 하겠다.

## What Is Decorator Pattern?

![Decorator_Pattern_UML](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Decorator_Pattern_UML.png)

Decorator Pattern은 소프트웨어 디자인 패턴 중에서 **구조 패턴(Structural Pattern)** 을 구성하는 방법 중 하나이다.

우리가 객체를 중간에 이용할 때 추가적인 기능에 대해 동적으로 첨가할 필요가 있다.

이게 무슨 뜻일까? 방금 전에 원래 정해진 부대찌개 레시피에 추가적인 재료를 더 넣듯이, 객체에서 추가 요구사항을 통해 필요한 행위를 자식 클래스에 확장하는 것으로 기능을 유연하게 확장할 수 있다.

만일 Decorator Pattern을 이용하지 않더라면 위의 Class Diagram에 있는 Component 추상 클래스의 메소드들을 ConcreteComponent 클래스에서 확장 구현을 우선 하는 것만으로 행위 등록이 끝이 난다. 

그렇다면 Component 추상 클래스에다가 필요한 행위를 계속 넣으면 끝인데 왜 Decorator를 추가하는 것이 좋을까? 바로 **다른 객체에 영향을 주지 않고 요건을 추가하기 위한** 이유, **동일한 기능을 반복하기 위한** 이유이다.

Decorator Pattern은 상속 관계를 유연하게 확장하기 위해 적용하는 Pattern이지만, 이것도 너무 많으면 둘러 쌓인 Decorator가 많아져서 디버깅이 어려워지는 단점이 있으니 적당히 쓰는 것이 좋겠다.

## Example of Decorator Pattern

이번에는 Decorator Pattern을 부대찌개 주문을 사례로 적용시켜보자. 부대찌개 육수의 종류는 사골 육수, 멸치 육수, 조미료 육수 등이 있고, 기본적으로 제공되는 재료들이 모두 포함되어 있다. 그러나 부대찌개에 무언가 부족하다고 느끼면 치즈, 라면 사리 등을 추가로 넣는다. 이번 예제의 Class Diagram은 아래와 같다.

![Decorator_Pattern_Example](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Decorator_Pattern_Example.png)

**MenuPrice Enumeration 클래스 작성**

```
package net.kang.decorator.enumeration;

public enum MenuPrice {
    VEGETABLE_BROTH(6000), FISH_BROTH(7000), BONE_BROTH(8000),
    CHEESE(500), RAMEN(800), RED_BEAN(1000), SPAM_SAUSAGE(2000);

    private final int value;
    private MenuPrice(int value){
        this.value = value;
    }
    public int getValue(){
        return this.value;
    }
}
```
MenuPrice.java

MenuPrice는 각 메뉴 별로 책정 가격을 Enumeration 클래스로 구현한 것이다.

각 요소 안에 있는 값은 해당 주문이 들어올 때 책정되는 가격이다.

VEGETABLE_BROTH는 야채 육수, FISH_BROTH는 멸치 육수, BONE_BROTH는 사골 육수, CHEESE는 치즈, RAMEN은 라면, RED_BEAN은 통조림 콩, SPAM_SAUSAGE는 부대고기이다.

**Component Abstract Class 작성**

```
package net.kang.decorator.main_cooking;

public abstract class BoodaeChigae {
    protected String description;

    public void setDescription(String description){
        this.description = description;
    }

    public String getDescription(){
        return this.description;
    }

    public abstract int cost();
}
```

BoodaeChigae.java

부대찌개를 만들기 위한 상속 기능을 제공하는 Component 클래스인 BoodaeChigae(부대찌개. 원래 부대찌개는 영어로 번역하면 길어지기 때문에 이해하기 쉽게 한글 영어화로 썼음.) 클래스이다. 부대찌개 주문 요소를 반영하는 description 변수와 주문 요소 별 총 가격을 반환하는 cost() 추상 메소드를 작성하였다.

**Concrete Component Class 작성**

```
package net.kang.decorator.main_cooking;

import net.kang.decorator.enumeration.MenuPrice;

public class BoneBrothBoodaeChigae extends BoodaeChigae {
    private int person;

    public BoneBrothBoodaeChigae(int person){
        this.person = person;
        super.setDescription(String.format("사골 육수 부대찌개 %d인분", person));
        System.out.printf("[주문] 사골 육수 부대찌개 %d인분 주문했습니다.\n", person);
    }

    @Override
    public int cost(){
        return MenuPrice.BONE_BROTH.getValue() * this.person;
    }
}
```

BoneBrothBoodaeChigae.java

처음에 손님이 사골 육수 부대찌개를 주문할 때 생성되는 객체인데, 여기서 person은 n 인분을 반영하기 위해 작성한 이 객체의 멤버 변수이다. 가격은 사골 육수 부대찌개 정가인 8000원에 인원에 맞춰 곱한 값으로 반환한다.

**Decorator Abstract Class 작성**

```
package net.kang.decorator.sub_ingredient;

import net.kang.decorator.main_cooking.BoodaeChigae;

public abstract class ChigaeDecorator extends BoodaeChigae {
    public abstract String getDescription();
}
```

ChigaeDecorator.java

부대찌개에 추가로 넣기 위한 재료를 반영하기 위한 Decorator 클래스인 ChigaeDecorator 클래스이다. 여기서 getDescription() 메소드는 현재 손님이 주문한 사항들을 그 때마다 반영하여 불러오기 위해 쓰인다.

**Concrete Decorator Class 작성**

```
package net.kang.decorator.sub_ingredient;

import net.kang.decorator.enumeration.MenuPrice;
import net.kang.decorator.main_cooking.BoodaeChigae;

public class SpamSausagePlusBoodaeChigae extends ChigaeDecorator {
    private BoodaeChigae boodaeChigae;
    private int quantity;

    public SpamSausagePlusBoodaeChigae(BoodaeChigae boodaeChigae, int quantity){
        super();
        this.boodaeChigae = boodaeChigae;
        this.quantity = quantity;
    }

    @Override
    public String getDescription(){
        return String.format("%s, 부대고기 %d인분 추가", boodaeChigae.getDescription(), this.quantity);
    }

    @Override
    public int cost(){
        return boodaeChigae.cost() + MenuPrice.SPAM_SAUSAGE.getValue() * this.quantity;
    }
}
```

SpamSausagePlusBoodaeChigae.java

원래 주문하는 부대 찌개에 육수 종류를 바꾸지 않는 이상, 찌개 그대로 부대 고기를 추가하기 위해 쓰는 ConcreteDecorator 클래스이다. 원래 부대찌개에 재료를 추가하는 이 객체의 getDescription() 메소드를 불러오면 현재 작성된 영수증에 추가로 반영되는 원리로 감이 잡힐 것이다.

**Client Class 작성**

```
package net.kang.decorator.client;

import net.kang.decorator.main_cooking.BoneBrothBoodaeChigae;
import net.kang.decorator.main_cooking.BoodaeChigae;
import net.kang.decorator.main_cooking.FishBrothBoodaeChigae;
import net.kang.decorator.main_cooking.VegetableBrothBoodaeChigae;
import net.kang.decorator.sub_ingredient.CheesePlusBoodaeChigae;
import net.kang.decorator.sub_ingredient.RamenPlusBoodaeChigae;
import net.kang.decorator.sub_ingredient.RedBeanPlusBoodaeChigae;
import net.kang.decorator.sub_ingredient.SpamSausagePlusBoodaeChigae;

public class MainClient {
    public static void main(String[] args){
        System.out.println("[멸치 육수 부대찌개 주문]");
        BoodaeChigae fishBrothBoodaeChigae = new FishBrothBoodaeChigae(3);
        System.out.printf("- 주문 속성 : %s\n", fishBrothBoodaeChigae.getDescription());
        System.out.printf("- 후불 가격 : %d원\n", fishBrothBoodaeChigae.cost());
        System.out.println();

        System.out.println("[주문 완료 후 멸치 육수 치즈 추가]");
        fishBrothBoodaeChigae = new CheesePlusBoodaeChigae(fishBrothBoodaeChigae, 2);
        System.out.printf("- 주문 속성 : %s\n", fishBrothBoodaeChigae.getDescription());
        System.out.printf("- 후불 가격 : %d원\n", fishBrothBoodaeChigae.cost());
        System.out.println();

        System.out.println("[주문 완료 후 멸치 육수 라면 사리 추가]");
        fishBrothBoodaeChigae = new RamenPlusBoodaeChigae(fishBrothBoodaeChigae, 1);
        System.out.printf("- 주문 속성 : %s\n", fishBrothBoodaeChigae.getDescription());
        System.out.printf("- 후불 가격 : %d원\n", fishBrothBoodaeChigae.cost());
        System.out.println();

        System.out.println("[사골 육수 부대찌개 주문]");
        BoodaeChigae boneBrothBoodaeChigae = new BoneBrothBoodaeChigae(5);
        System.out.printf("- 주문 속성 : %s\n", boneBrothBoodaeChigae.getDescription());
        System.out.printf("- 후불 가격 : %d원\n", boneBrothBoodaeChigae.cost());
        System.out.println();

        System.out.println("[주문 완료 후 사골 육수 스팸 / 소시지 추가]");
        boneBrothBoodaeChigae = new SpamSausagePlusBoodaeChigae(boneBrothBoodaeChigae, 3);
        System.out.printf("- 주문 속성 : %s\n", boneBrothBoodaeChigae.getDescription());
        System.out.printf("- 후불 가격 : %d원\n", boneBrothBoodaeChigae.cost());
        System.out.println();

        System.out.println("[야채 육수 부대찌개 주문]");
        BoodaeChigae vegetableBrothBoodaeChigae = new VegetableBrothBoodaeChigae(7);
        System.out.printf("- 주문 속성 : %s\n", vegetableBrothBoodaeChigae.getDescription());
        System.out.printf("- 후불 가격 : %d원\n", vegetableBrothBoodaeChigae.cost());
        System.out.println();

        System.out.println("[주문 완료 후 통조림 콩 추가]");
        vegetableBrothBoodaeChigae = new RedBeanPlusBoodaeChigae(vegetableBrothBoodaeChigae, 2);
        System.out.printf("- 주문 속성 : %s\n", vegetableBrothBoodaeChigae.getDescription());
        System.out.printf("- 후불 가격 : %d원\n", vegetableBrothBoodaeChigae.cost());
        System.out.println();

        System.out.println("[주문 완료 후 라면 사리 추가]");
        vegetableBrothBoodaeChigae = new RamenPlusBoodaeChigae(vegetableBrothBoodaeChigae, 2);
        System.out.printf("- 주문 속성 : %s\n", vegetableBrothBoodaeChigae.getDescription());
        System.out.printf("- 후불 가격 : %d원\n", vegetableBrothBoodaeChigae.cost());
    }
}
```

MainClient.java

위를 전부 구현하고, 이제는 MainClient에서 부대 찌개 객체를 생성하고, 추가하고 싶은 재료 객체를 이용하여 추가 사항을 반영하고, 주문 속성과 후불 가격을 반환하는 과정이다. 실행 결과는 다음과 같다.

```
[멸치 육수 부대찌개 주문]
[주문] 멸치 육수 부대찌개 3인분 주문했습니다.
- 주문 속성 : 멸치 육수 부대찌개 3인분
- 후불 가격 : 21000원

[주문 완료 후 멸치 육수 치즈 추가]
- 주문 속성 : 멸치 육수 부대찌개 3인분, 치즈 2장 추가
- 후불 가격 : 22000원

[주문 완료 후 멸치 육수 라면 사리 추가]
- 주문 속성 : 멸치 육수 부대찌개 3인분, 치즈 2장 추가, 라면 사리 1장 추가
- 후불 가격 : 22800원

[사골 육수 부대찌개 주문]
[주문] 사골 육수 부대찌개 5인분 주문했습니다.
- 주문 속성 : 사골 육수 부대찌개 5인분
- 후불 가격 : 40000원

[주문 완료 후 사골 육수 스팸 / 소시지 추가]
- 주문 속성 : 사골 육수 부대찌개 5인분, 부대고기 3인분 추가
- 후불 가격 : 46000원

[야채 육수 부대찌개 주문]
[주문] 야채 육수 부대찌개 7인분 주문했습니다.
- 주문 속성 : 야채 육수 부대찌개 7인분
- 후불 가격 : 42000원

[주문 완료 후 통조림 콩 추가]
- 주문 속성 : 야채 육수 부대찌개 7인분, 통조림 콩 2인분 추가
- 후불 가격 : 44000원

[주문 완료 후 라면 사리 추가]
- 주문 속성 : 야채 육수 부대찌개 7인분, 통조림 콩 2인분 추가, 라면 사리 2장 추가
- 후불 가격 : 45600원
```

## Applicative of Decorator Pattern

Java의 입출력 스트림을 담당하는 클래스가 InputStream, OutputStream 클래스이다. 이 구조는 Decorator Pattern을 접목시켰다. Java에서는 왜 이러한 방안으로 입출력 스트림을 반영했을까? 

![Java_InputStream_UML](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Java_InputStream_UML.png)

InputStream / OutputStream은 객체를 만들 수 있는 클래스가 아닌 추상 클래스로 되어 있다. 여기서 입/출력을 할 수 있는 수단이 File, String, ByteArray 등이 있지만, Filter 방식에서 또 나뉘게 된다. 

File, String, ByteArray 등은 외부 자원과 연결하는 Node 계열 Stream이고, 기존 Stream에 추가되어 I/O 기능 추가 목적인 경우는 Filter 계열 Stream을 이용한다. 

과거에 진행했던 프로젝트 중에서 데이터베이스에 있는 값을 엑셀로 전달하던 도중 이미지 파일이 있을 것이다. 실제로 엑셀 파일에는 데이터베이스에 있는 데이터를 통한 실제 보고서 작성을 위해 사용하고, 이미지는 엑셀에 추가할 경우에는 이미지를 잠깐 엑셀에 넣어주기 위하여 사용한다. 그래서 실제 프로젝트에서는 이미지를 불러올 때 현재 Stream을 이용하지 않더라도 Filter 계열 Stream을 이용해 잠깐 불러와서 엑셀에 붙어주는 방안으로 작성하였다.

이처럼 데이터베이스에 있는 값들을 불러와서 주 파일에 작성하고, 데이터베이스에 있는 이미지 파일을 부 파일로 적용하여 사진 있는 보고서 방식으로 저장할 수 있게 도와주는 패턴이 바로 Decorator Pattern이다.

## Comparison of Another Pattern

- Proxy Pattern - Object를 접근하기 위해 쓰인다. 클라이언트 측의 직접 접근을 막는 것이 아닌 대리자를 이용한다. 그러나 Decorator 처럼 새로운 기능을 추가하진 않는다.
- Adapter Pattern - Interface를 감싸는 Adapter 클래스를 이용한 접근을 도와준다. 여기서는 새로운 기능을 이용하지 않고, Interfacce 별로 본연의 일을 구현한다.
- Facade Pattern - 1개 이상의 Interface를 감싸고, 중앙 집중된 Interface 접근을 제공한다. 그러나 Interface는 간단하게 구성되어 있고, 무언갈 추가하는 개념이라고 보기 힘들다.

## References

- http://gdtbgl93.tistory.com/9 - Decorator Pattern 예제 참고와 간략한 설명 참조.
- http://jusungpark.tistory.com/9 - Decorator Pattern 간략한 설명 참조.
- http://friday.fun25.co.kr/blog/?p=189 - Decorator Pattern 예제를 아이언맨을 비유하여 적용하였음.
- https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0_%ED%8C%A8%ED%84%B4 - Decorator Pattern 위키 백과 참조
- http://unabated.tistory.com/entry/%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0-%ED%8C%A8%ED%84%B4Decorator-Pattern - Java IO가 이를 사용한 이유가 나와 있음.

## Post Script
- 여기에 작성된 내용 이외에도 필요한 개념들을 발견하게 된다면 언제든지 갱신될 수 있습니다.
- 이 강의노트 개념에서 더욱 다뤄주면 좋은 개념들이나 오탈자가 있으시면 KangBakSa Issues에 올려주세요.
- 그리고 객체 지향 패턴은 Design_Pattern Repository에 추가로 저장하고 있습니다. 나중에 완성되면, public으로 바꿔 소스 코드와 연동하겠습니다. 