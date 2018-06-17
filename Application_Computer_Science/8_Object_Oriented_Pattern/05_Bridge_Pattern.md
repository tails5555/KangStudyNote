# Bridge Pattern Issues

![parkhyosin_singing](/Application_Computer_Science/8_Object_Oriented_Pattern/img/parkhyosin_singing.jpg)

필자는 박효신을 매우 좋아한다. 해줄 수 없는 일, 동경, 눈의 꽃, 추억은 사랑을 닮아(일명 추사닮.) 등 유명한 노래를 내었지만, 장르를 떠나서 다른 가수가 부른 노래를 자기만의 노래 억양으로 리메이크하여 라이브를 한다. 이러한 특성을 가진 몇 안 되는 가수여서 음악을 좋아하는 사람들이 더욱 다가가기 쉬운 매력이 있기 때문에 더욱 좋아하게 되었다.

어떻게 보면 Bridge Pattern은 마치 박효신이 발라드 뿐만 아니라 **댄스, 랩, 락 등을 라이브에서 독자적인 노래 억양**으로 본인의 노래로 만들듯이, 각 객체가 자신이 하는 행위들을 독립적으로 변경이 가능한 개념으로 볼 수 있다.

이번 노트에서는 Bridge Pattern에 대해 다루겠다.

## What Is Bridge Pattern?

![Bridge_Pattern_UML](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Bridge_Pattern_UML.png)

Bridge Pattern은 소프트웨어 디자인 패턴 중에서 **구조 패턴(Structural Pattern)** 을 구성하는 방법 중 하나이다.

Bridge Pattern은 구현부에서 추상부(기능부)을 분리해 각자 독립적으로 변형할 수 있게 하는 패턴으로 볼 수 있다. 이게 뭔 소리인지 처음에는 이해가 안 간다.

Bridge는 말 그대로 영등포와 마포를 이어주는 양화대교와 같은 그 의미, 맞다. 구현부에서 필요한 자식 객체(서브 객체)는 Interface를 상속 시켜 구현을 한다. 그리고 자식 객체의 기능 이외에 추가하고 싶은 개념이 있을 것이다. 이러한 기능을 부가하기 위해 쓰이는 클래스가 바로 추상부 클래스이다.

구현부인 자식(서브) 객체는 결국 Interface로 상속이 되어 있기 때문에 Interface의 기능은 추상부 클래스에서 상속 받아 그대로 적용할 수 있다. 쉽게 설명한다면 구현부와 추상부(기능부) 사이에는 무언가를 거치는 Interface를 박효신이 자기 노래 이외에 다른 가수의 노래를 부르기 위해 **박효신 만의 억양**이 다른 장르의 음악과의 연결 고리가 되는 것과 같은 원리이다.

Bridge Pattern은 또한 객체 간의 강한 결합도(Coupling)를 줄이기 위해 사용된다. 여기서 추상화된 부분을 구현한 구상 클래스가 아무리 변형되어도 클라이언트 쪽에서는 영향을 끼치지 않는 장점이 있는 Pattern이다.

**참고로 객체 지향 뿐만 아니라 다른 지향 소프트웨어 공학론에서도 결합도(Coupling)를 줄이고, 응집도(Cohesion)를 높이는 것이 필수이다.** 

## Example of Bridge Pattern

![Bridge_Pattern_Example](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Bridge_Pattern_Example.png)

이번에는 평범한 가수를 Bridge Pattern을 이용해서 일반 가수를 전부 박효신이랑 거미로 거듭나게 만드는 예제로 설명하겠다.

**Concrete Interface(구현부 Interface) 작성**

```
package net.kang.bridge.abstract_interface;

public interface SingLive {
    public void singing(String lyric);
    public void sayHello(String greeting);
}
```

SingLive.java

이 Interface는 구현부에서 필요하다. 여기서 일반 가수 별로 singing(일반 라이브), sayHello(라이브 인사) 메소드를 따로 구현해줘야 한다. 일반 가수에 대한 메소드를 따로 구현하는 것은 구현부에서 처리하는 부분으로 간주한다. 

**Concrete Implementor(구현부) Class 작성**

```
package net.kang.bridge.singer_object;

import net.kang.bridge.abstract_interface.SingLive;

public class GeneralMaleSinger implements SingLive {
    private String name;
    public GeneralMaleSinger(String name){
        this.name = name;
    }
    @Override
    public void sayHello(String greeting){
        System.out.printf("<남자 가수 인사> [%s] %s\n", name, greeting);
    }
    @Override
    public void singing(String lyrics){
        System.out.printf("<남자 가수 라이브> [%s] %s\n", name, lyrics);
    }
}
```

GeneralMaleSinger.java

일반 가수는 남자 가수(GeneralMaleSinger), 여자 가수(GeneralFemaleSinger)로 나뉜다. 여기서는 구현부에서 라이브 인사와 일반 라이브를 하는 행위를 처리한다.

**Abstraction(추상 / 기능부) Class 작성**

```
package net.kang.bridge.abstract_class;

import net.kang.bridge.abstract_interface.SingLive;

public abstract class AccentSingLive {
    protected SingLive singLive;
    protected AccentSingLive(SingLive singLive){
        this.singLive = singLive;
    }
    public abstract void generalLive(String greeting, String lyrics);
    public abstract void changeGenre(String genre);
    public abstract void noBgmSinging(String lyrics);
}
```

AccentSingLive.java

AccentSingLive 클래스는 추상부(기능부)의 클래스이다. Abstract Class에서도 마찬가지로 맴버 변수와 이에 맞춘 생성자를 만들 수 있는데 이는 AccentMaleSinger, AccentFemaleSinger 인스턴스를 생성하는 시점에서 동시에 생성된다. 다만 Abstract Class의 생성자와 맴버 변수는 protected으로 설정하여 상속 받는 클래스만 적용할 수 있게 구상하였다.

**Refined Abstraction(추상 / 기능부) Class 작성**

```
package net.kang.bridge.singer_object;

import net.kang.bridge.abstract_class.AccentSingLive;
import net.kang.bridge.abstract_interface.SingLive;

public class AccentFemaleSinger extends AccentSingLive {
    private String name;
    public AccentFemaleSinger(SingLive singLive, String name){
        super(singLive);
        this.name = name;
    }

    @Override
    public void generalLive(String greeting, String lyrics){
        this.singLive.sayHello(greeting);
        this.singLive.singing(lyrics);
    }

    @Override
    public void changeGenre(String genre){
        System.out.printf("<독창 여자 가수 장르 변경> [%s] %s\n", name, genre);
    }

    @Override
    public void noBgmSinging(String lyrics){
        System.out.printf("<독창 여자 가수 무반주 라이브> [%s] %s\n", name, lyrics);
    }
}
```
AccentFemaleSinger.java

구현부에서 필요한 Interface를 포함한 추상부(기능부)를 상속 받아서 추상부에 필요한 기능을 구현한 억양을 가진 남자 가수, 여자 가수 클래스이다. 억양만 가진 가수는 장르를 갑자기 변경하여 부를 수 있고(changeGenre), 갑자기 무반주로 노래를 부를 수도 있다.(noBgmSining)

**Client Class 작성**

```
package net.kang.bridge.client;

import net.kang.bridge.abstract_class.AccentSingLive;
import net.kang.bridge.singer_object.AccentFemaleSinger;
import net.kang.bridge.singer_object.AccentMaleSinger;
import net.kang.bridge.singer_object.GeneralFemaleSinger;
import net.kang.bridge.singer_object.GeneralMaleSinger;

public class MainClient {
    public static void main(String[] args){
        System.out.println("[일반 가수 라이브]");
        GeneralMaleSinger maleSinger = new GeneralMaleSinger("가수A");
        GeneralFemaleSinger femaleSinger = new GeneralFemaleSinger("가수B");

        maleSinger.sayHello("안녕하세요? 힙합을 잘 하는 가수A 입니다.");
        maleSinger.singing("췍, 요우, 췍. 암더 코리안, 탑 클래스 힙합 모범 쀄브러스~");
        femaleSinger.sayHello("음악을 좋아하는 가수 B입니다.");
        femaleSinger.singing("날 울리지 마요~ 날 사랑해 줘요~");

        System.out.println("[독창 가수 라이브]");
        AccentSingLive accentMaleSinger = new AccentMaleSinger(maleSinger, "갑자기 억양 살린 가수A");
        AccentSingLive accentFemaleSinger = new AccentFemaleSinger(femaleSinger, "갑자기 억양 살린 가수B");

        accentMaleSinger.generalLive("안녕하세요, 가수 A입니다.", "이제 너에게~ 난 아픔이었다는걸~");
        accentFemaleSinger.generalLive("안녕하세요, 가수 B입니다.", "그대 기억이~ 지난 사랑이~");
        accentMaleSinger.changeGenre("힙합");
        accentFemaleSinger.changeGenre("댄스");
        accentMaleSinger.noBgmSinging("Turn Up 늘 분위기는 살리고, 서울에서 World Wide 모두 다 난리도~");
        accentFemaleSinger.noBgmSinging("그대에게 전해주오~ 기다리고 있다고~");
    }
}
```

MainClient.java

Client 측에서는 2가지 시나리오로 작성했는데 일반 가수 라이브 시점과 독창 가수 라이브 시점으로 나뉘었다.

일반 가수 라이브 시점에서는 구현부에 있는 기능들을 실행하여 일반 라이브를 진행한다. 가수A, 가수B가 각각 인사하고 본인만의 라이브를 진행한다.

그렇지만 독창 가수 라이브 시점에서는 추상부(기능부)에 있는 기능들을 모두 실행하여 각 가수 별 독창 라이브를 진행한다. 독창 가수 라이브를 이용하여 이를 구현한 객체(AccentMaleSinger, AccentFemaleSinger)를 각각 업 캐스팅하여 실행한다.

실행 결과는 아래와 같다.

```
[일반 가수 라이브]
<남자 가수 인사> [가수A] 안녕하세요? 힙합을 잘 하는 가수A 입니다.
<남자 가수 라이브> [가수A] 췍, 요우, 췍. 암더 코리안, 탑 클래스 힙합 모범 쀄브러스~
<여자 가수 인사> [가수B] 음악을 좋아하는 가수 B입니다.
<여자 가수 라이브> [가수B] 날 울리지 마요~ 날 사랑해 줘요~
[독창 가수 라이브]
<남자 가수 인사> [가수A] 안녕하세요, 가수 A입니다.
<남자 가수 라이브> [가수A] 이제 너에게~ 난 아픔이었다는걸~
<여자 가수 인사> [가수B] 안녕하세요, 가수 B입니다.
<여자 가수 라이브> [가수B] 그대 기억이~ 지난 사랑이~
<독창 남자 가수 장르 변경> [갑자기 억양 살린 가수A] 힙합
<독창 여자 가수 장르 변경> [갑자기 억양 살린 가수B] 댄스
<독창 남자 가수 무반주 라이브> [갑자기 억양 살린 가수A] Turn Up 늘 분위기는 살리고, 서울에서 World Wide 모두 다 난리도~
<독창 여자 가수 무반주 라이브> [갑자기 억양 살린 가수B] 그대에게 전해주오~ 기다리고 있다고~

```

## Applicative of Bridge Pattern

실제로 Bridge Pattern은 여러 플랫폼에서 사용하는 그래픽스와 운영체제 처리 시스템에서 유용하게 쓰이고 있다.

그리고 객체 지향 프로그래밍 구상에서 Interface와 실제 구현 부를 서로 다른 방식으로 변경하는 경우에도 유용하게 쓰인다.

그렇지만 이를 너무 많이 활용한다면 디자인이 꽤 복잡해진다. 그러니 핵심적인 기능을 구현할 때 유용하게 사용하도록 하자. 

## Comparison of Another Patterns

Adapter Pattern - 지난 시간에 잠깐 다뤘는데, 서로 같은 종류의 객체 A, B를 서로 호환하기 위하여 Adapter(A->B Adapter), Adaptee(B 객체)를 이용한 개념이다. 그렇지만 Bridge Pattern은 호환하는 목적이 아니라 구현부와 기능부를 따로 독립시켜 관리할 수 있게 도와주는 패턴이다.

[이외의 패턴은 계속 공부하면서 추가하겠습니다.]

## References
- https://zetawiki.com/wiki/Bridge_%ED%8C%A8%ED%84%B4 - 예제는 이를 참고해서 재구성했습니다.
- https://ko.wikipedia.org/wiki/%EB%B8%8C%EB%A6%AC%EC%A7%80_%ED%8C%A8%ED%84%B4 - Bridge Pattern 위키 백과 설명.
- http://leetaehoon.tistory.com/59 - Bridge Pattern 상세 설명.
- http://lktprogrammer.tistory.com/35 - 여기도 예제가 나와있지만, 간략한 설명으로 되어 있으니 참고용으로 좋음.

## Post Script
- 여기에 작성된 내용 이외에도 필요한 개념들을 발견하게 된다면 언제든지 갱신될 수 있습니다.
- 이 강의노트 개념에서 더욱 다뤄주면 좋은 개념들이나 오탈자가 있으시면 KangBakSa Issues에 올려주세요.
- 그리고 객체 지향 패턴은 Design_Pattern Repository에 추가로 저장하고 있습니다. 나중에 완성되면, public으로 바꿔 소스 코드와 연동하겠습니다. 