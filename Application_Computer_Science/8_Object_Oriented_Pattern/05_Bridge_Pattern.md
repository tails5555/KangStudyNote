# Bridge Pattern Issues

![parkhyosin_singing](/Application_Computer_Science/8_Object_Oriented_Pattern/img/parkhyosin_singing.jpg)

필자는 박효신을 매우 좋아한다. 해줄 수 없는 일, 동경, 눈의 꽃, 추억은 사랑을 닮아(일명 추사닮.) 등 유명한 노래를 내었지만, 장르를 떠나서 다른 가수가 부른 노래를 자기만의 노래 억양으로 리메이크하여 라이브를 한다. 이러한 특성을 가진 몇 안 되는 가수여서 음악을 좋아하는 사람들이 더욱 다가가기 쉬운 매력이 있기 때문에 더욱 좋아하게 되었다.

어떻게 보면 Bridge Pattern은 마치 박효신이 발라드 뿐만 아니라 **댄스, 랩, 락 등을 라이브에서 독자적인 노래 억양**으로 본인의 노래로 만들듯이, 각 객체가 자신이 하는 행위들을 독립적으로 변경이 가능한 개념으로 볼 수 있다.

이번 노트에서는 Bridge Pattern에 대해 다루겠다.

## What Is Bridge Pattern?

![Bridge_Pattern_UML](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Bridge_Pattern_UML.png)

Bridge Pattern은 

Bridge Pattern은 구현부에서 추상부을 분리해 각자 독립적으로 변형할 수 있게 하는 패턴으로 볼 수 있다. 이게 뭔 소리인지...

쉽게 설명한다면 구현부와 기능부 사이에는 무언가를 거쳐야 한다. 마치 박효신이 자기 노래 이외에 다른 가수의 노래를 본인의 억양으로 부르기 위해 그 가수에게 사전 부탁을 하듯이.





Bridge Pattern은 간의 강한 결합도(Coupling)를 줄이기 위해 사용된다.

**객체 지향 뿐만 아니라 다른 지향 소프트웨어 공학론에서도 결합도(Coupling)를 줄이고, 응집도(Cohesion)를 높여야 한다.** 

## Example of Bridge Pattern

![Bridge_Pattern_Example](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Bridge_Pattern_Example.png)

이번에는 평범한 가수를 Bridge Pattern을 이용해서 박효신이랑 거미로 거듭나게 만드는 예제로 설명하겠다.

```
package net.kang.bridge.abstract_interface;

public interface SingLive {
    public void singing(String lyric);
    public void sayHello(String greeting);
}
```

SingLive.java

[이어 작성하겠습니다.]

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
        System.out.printf("<일반 남자 가수 인사> [%s] %s\n", name, greeting);
    }
    @Override
    public void singing(String lyrics){
        System.out.printf("<일반 남자 가수 라이브> [%s] %s\n", name, lyrics);
    }
}
```

GeneralMaleSinger.java

[이어 작성하겠습니다.]

```
package net.kang.bridge.abstract_class;

import net.kang.bridge.abstract_interface.SingLive;

public abstract class AccentSingLive {
    protected SingLive singLive;
    protected AccentSingLive(SingLive singLive){
        this.singLive = singLive;
    }
    public abstract void changeGenre(String genre);
    public abstract void noBgmSinging(String lyrics);
}
```

AccentSingLive.java

[이어 작성하겠습니다.]

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

[이어 작성하겠습니다.]

```
package net.kang.bridge.client;

import net.kang.bridge.singer_object.AccentFemaleSinger;
import net.kang.bridge.singer_object.AccentMaleSinger;
import net.kang.bridge.singer_object.GeneralFemaleSinger;
import net.kang.bridge.singer_object.GeneralMaleSinger;

public class MainClient {
    public static void main(String[] args){
        GeneralMaleSinger maleSinger = new GeneralMaleSinger("가수A");
        GeneralFemaleSinger femaleSinger = new GeneralFemaleSinger("가수B");

        maleSinger.sayHello("안녕하세요? 힙합을 잘 하는 가수A 입니다.");
        maleSinger.singing("췍, 요우, 췍. 암더 코리안, 탑 클래스 힙합 모범 쀄브러스~");
        femaleSinger.sayHello("음악을 좋아하는 가수 B입니다.");
        femaleSinger.sayHello("날 울리지 마요~ 날 사랑해 줘요~");

        AccentMaleSinger accentMaleSinger = new AccentMaleSinger(maleSinger, "억양 살린 가수A");
        AccentFemaleSinger accentFemaleSinger = new AccentFemaleSinger(femaleSinger, "억양 살린 가수B");

        accentMaleSinger.changeGenre("발라드");
        accentFemaleSinger.changeGenre("댄스");
        accentMaleSinger.noBgmSinging("이 몇 분 짜리 노래가~ 별거 아닌 가사가~");
        accentFemaleSinger.noBgmSinging("그대에게 전해주오~ 기다리고 있다고~");
    }
}
```

MainClient.java

[이어 작성하겠습니다.]

실행 결과는 아래와 같다.

```
<일반 남자 가수 인사> [가수A] 안녕하세요? 힙합을 잘 하는 가수A 입니다.
<일반 남자 가수 라이브> [가수A] 췍, 요우, 췍. 암더 코리안, 탑 클래스 힙합 모범 쀄브러스~
<일반 여자 가수 인사> [가수B] 음악을 좋아하는 가수 B입니다.
<일반 여자 가수 인사> [가수B] 날 울리지 마요~ 날 사랑해 줘요~
<독창 남자 가수 장르 변경> [억양 살린 가수A] 발라드
<독창 여자 가수 장르 변경> [억양 살린 가수B] 댄스
<독창 남자 가수 무반주 라이브> [억양 살린 가수A] 이 몇 분 짜리 노래가~ 별거 아닌 가사가~
<독창 여자 가수 무반주 라이브> [억양 살린 가수B] 그대에게 전해주오~ 기다리고 있다고~
```

[이어 작성하겠습니다.]

## References
- https://zetawiki.com/wiki/Bridge_%ED%8C%A8%ED%84%B4 - 예제는 이를 참고해서 재구성했습니다.
- https://ko.wikipedia.org/wiki/%EB%B8%8C%EB%A6%AC%EC%A7%80_%ED%8C%A8%ED%84%B4 - Bridge Pattern 위키 백과 설명.
- http://gakari.tistory.com/entry/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-Bridge-%ED%8C%A8%ED%84%B4 - Bridge Pattern 상세 설명.
- http://lktprogrammer.tistory.com/35 - 여기도 예제가 나와있지만, 간략한 설명으로 되어 있으니 참고용으로 좋음.