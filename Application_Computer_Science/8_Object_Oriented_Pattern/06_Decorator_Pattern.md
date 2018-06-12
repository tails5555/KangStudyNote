# Decorator Pattern

![BooDaeChiGae](/Application_Computer_Science/8_Object_Oriented_Pattern/img/BooDaeChiGae.jpeg)

갑자기 배고프다. 배고플 때에는 한 번 부대찌개 만들어 먹는 도구 하나에 콜라 한 잔 곁들여 먹으면 한 끼 식사로 딱이다. 그렇지만 부대찌개의 중요 재료는 무엇보다도 스팜과 외국산 소시지(흔히 부대 고기라고 한다.), 콩 통조림, 슬라이스 치즈를 딱 올려 먹고 거기 안에 라면 사리를 끓여 먹으면 좋다. 그렇지만 부대찌개에 떡을 넣는다면 국물이 퍼져서 맛 없어서 잘 안 넣어 먹는다.

이처럼 Decorator Pattern은 부대찌개에 본인의 입 맛에 맞게 중간에 다른 재료를 추가해서 끓여 먹듯이, 객체에서도 동적으로 필요한 

## What Is Decorator Pattern?



## Example of Decorator Pattern

이번에는 Decorator Pattern을 부대찌개 주문을 사례로 적용시켜보자. 부대찌개 육수의 종류는 사골 육수, 멸치 육수, 조미료 육수 등이 있고, 기본적으로 제공되는 재료들이 모두 포함되어 있다. 그러나 부대찌개에 무언가 부족하다고 느끼면 치즈, 라면 사리 등을 추가로 넣는다. 이번 예제의 Class Diagram은 아래와 같다.

![Decorator_Pattern_UML](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Decorator_Pattern_UML.png)

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


```
package net.kang.decorator.sub_ingredient;

import net.kang.decorator.main_cooking.BoodaeChigae;

public abstract class ChigaeDecorator extends BoodaeChigae {
    public abstract String getDescription();
}
```

ChigaeDecorator.java



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

SpamSausagePlusBoodaeChigae


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

## Applicative of Decorator Pattern

Java의 입출력 스트림을 담당하는 클래스가 InputStream, OutputStream 클래스이다. 

![Java_InputStream_UML](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Java_InputStream_UML.png)

## Comparison of Another Pattern



## References

- http://gdtbgl93.tistory.com/9 - Decorator Pattern 예제 참고와 간략한 설명 참조.
- http://jusungpark.tistory.com/9 - Decorator Pattern 간략한 설명 참조.
- http://friday.fun25.co.kr/blog/?p=189 - Decorator Pattern 예제를 아이언맨을 비유하여 적용하였음.
- https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0_%ED%8C%A8%ED%84%B4 - Decorator Pattern 위키 백과 참조