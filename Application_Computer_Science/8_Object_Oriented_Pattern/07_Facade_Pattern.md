# Façade Pattern Issues

![Dok2_Concert](/Application_Computer_Science/8_Object_Oriented_Pattern/img/dok2_concert.jpg)

> Turn up, 늘 분위기는 살리고<br/>
> 서울에서 world wide, 모두 난리도<br/>
> 아니거든. 내가 뜨면 넌 할 일 없어지는 중<br/>
> My bad, homie. Adios<br/>
> **L4L(Looking For Love), Dok2 Lyrics 서론.**

래퍼 Dok2가 처음 데뷔했을 때 부터 억만 장자가 된 비결이 무엇일까? 엄청난 음반 작업으로 볼 수 있지만, 또 다른 이유가 바로 전국 콘서트를 수 십 번 다니면서 공연비로 어마한 수익을 얻은 것도 큰 몫을 하였다. 위의 가사 중에 서울에서 world wide 부분을 읽어보면 Dok2가 자기 신곡이 항상 나올 때마다 전국을 돌아다니면서 콘서트를 한 번만 해도 곧 뜬다는 의미로 볼 수 있다.

객체 지향 프로그래밍에서도 Dok2가 전국을 돌며 콘서트를 돌듯이, Facade Object를 이용해 각각 흩어져 있는 Sub System Object를 직접 가져와서 응용하는 패턴이 바로 Facade Pattern이다.

이번 노트에서는 Façade Pattern에 대하여 다룰 것인데, 생각보다 쉬운 구성으로 되어 있어, 이 개념에 대해 큰 부담 없이 공부해보도록 하자.

## What Is Façade Pattern?

![Facade_Pattern_UML](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Facade_Pattern_UML.png)

Facade Pattern은 소프트웨어 디자인 패턴 중에서 **구조 패턴(Structure Pattern)** 을 구성하는 방법 중 하나이다.

Facade는 프랑스어로 건물의 정면, 외관이라는 뜻이다. 사실 상 프랑스어로 되어 있어서 무언가 간지가 철철 넘치는 패턴으로 생각할 수 있지만, 전혀 그렇지도 않다. 하지만 객체 지향 프로그래밍에서 이를 밥 먹듯이 이용하고 있다.

Facade Pattern의 궁극적인 목적은 **단순하게 간추린 Interface** 를 제공하기 위한 객체를 형성하는 것이다. Sub System 클래스 안에는 많은 코드가 작성되어 있는데 공통으로 쓰이는 행위를 추출할 수 있다면 Sub System에 대해 쉽게 사용할 수 있게 해준다.

실제로 Software Library를 쉽게 이해하기 위해 이 패턴을 주로 사용한다. Library 안에 있는 기능들의 행위를 API(Application Programming Interface)로 묶어야 하는데 Facade Object를 이용한다면 Library 내부 모듈의 의존성을 감소시키기 때문에 시스템 개발의 유연성이 향상된다.

## Example Of Façade Pattern

![Facade_Pattern_Example](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Facade_Pattern_Example.png)

이번에는 가수 콘서트를 이용한 Facade Pattern 예제를 작성해보도록 한다. 여기서 지역 별 콘서트는 수도권, 충청권, 전라권, 영남권 4지역으로 나뉘어서 각 콘서트 관리 System를 package를 이용해 분산 시킨다.

여기서 가수가 중간에 공연할 때 광고하는 경우도 더러 있을텐데, 수도권은 서울, 충청권은 대전, 전라권은 광주, 영남권은 부산에서만 광고할 수 있다.

그 다음에 Facade Object에서는 가수가 각 콘서트를 진행할 지역을 입력 받고, 각 콘서트 별 내부 요소를 결정하고 난 후, 지역 별로 콘서트 진행 상황을 출력해준다.

**Enumeration Class 작성**

```
package net.kang.facade.enumeration;

public enum RegionType {
    SEOUL, INCHEON, DAEJEON, DAEGU, GWANGJU, BUSAN;
}
```

RegionType.java

Enumeration 클래스는 지역을 구분하기 위하여 서울, 인천, ..., 부산으로 작성하였다. Enumeration은 객체의 Type을 결정할 때 사용하면 더욱 좋은 개념이다. 추후에 Java Enumeration 개념을 잠깐 다뤄보도록 하겠다.

**Sub System Class 작성**

```
package net.kang.facade.metropolitan_region;

public class SeoulConcert {
    private String title;
    private String support;
    private boolean isStarted;

    public SeoulConcert(){
        System.out.println("[서울] 서울 콘서트 계획을 잡습니다.");
        this.title = null;
        this.support = null;
        this.isStarted = false;
    }

    public void addSchedule(String title, String support){
        System.out.printf("[서울] %s 콘서트로 결정했습니다. - %s 후원\n", title, support);
        this.title = title;
        this.support = support;
        this.isStarted = false;
    }

    public void concertStart(){
        if(!this.isStarted) {
            this.isStarted = true;
            System.out.printf("[서울] %s 콘서트가 시작됩니다. - %s 후원\n", this.title, this.support);
        }else {
            System.out.printf("[서울] %s 콘서트가 진행 중입니다. - %s 후원\n", this.title, this.support);
        }
    }

    public void concertEnd(){
        if(this.isStarted){
            this.isStarted = false;
            System.out.printf("[서울] %s 콘서트가 끝났습니다. - %s 후원\n", this.title, this.support);
        }else{
            System.out.printf("[서울] %s 콘서트는 아직 진행 안 했습니다. - %s 후원\n", this.title, this.support);
        }
    }

    public void advertise(String context){
        if(this.isStarted) {
            System.out.printf("[서울] %s 콘서트 중 광고 - %s\n", this.title, context);
        }else{
            System.out.printf("[서울] %s 콘서트 전 광고 - %s\n", this.title, context);
        }
    }
}
```
SeoulConcert.java

Sub System 클래스는 각 패키지 별 다른 행위를 작성하는 클래스이다. 방금 전에 언급했듯이 서울 지역에서는 광고를 하지만, 인천 지역에서는 광고를 못 한다.

Sub System 클래스의 패키지는 같은 곳이 아닌 서로 다른 패키지에 분산되어 있다. 여기서는 수도권부터 영남권까지 지역 별 공연 시스템의 차별화 사례로 작성하여 이들을 한 번에 쓰는 곳이 바로 Facade Object 클래스 내부이다.

**Facade Class 작성**

```
package net.kang.facade.facade_object;

import net.kang.facade.enumeration.RegionType;
import net.kang.facade.gyeongsang_region.BusanConcert;
import net.kang.facade.gyeongsang_region.DaeguConcert;
import net.kang.facade.chungcheong_region.DaejeonConcert;
import net.kang.facade.jeonra_region.GwangjuConcert;
import net.kang.facade.metropolitan_region.IncheonConcert;
import net.kang.facade.metropolitan_region.SeoulConcert;

import java.util.List;

public class SingerFacade {
    private SeoulConcert seoulConcert;
    private IncheonConcert incheonConcert;
    private DaejeonConcert daejeonConcert;
    private DaeguConcert daeguConcert;
    private GwangjuConcert gwangjuConcert;
    private BusanConcert busanConcert;

    public SingerFacade(List<RegionType> regionList){
        for(RegionType regionType : regionList){
            switch(regionType){
                case SEOUL :
                    this.seoulConcert = new SeoulConcert();
                    break;
                case INCHEON :
                    this.incheonConcert = new IncheonConcert();
                    break;
                case DAEJEON :
                    this.daejeonConcert = new DaejeonConcert();
                    break;
                case DAEGU :
                    this.daeguConcert = new DaeguConcert();
                    break;
                case GWANGJU :
                    this.gwangjuConcert = new GwangjuConcert();
                    break;
                case BUSAN :
                    this.busanConcert = new BusanConcert();
                    break;
            }
        }
    }

    public void addScheduleSeoulConcert(String title, String support){
        if(this.seoulConcert != null){
            this.seoulConcert.addSchedule(title, support);
        }else{
            System.out.println("[서울 콘서트 계획은 없습니다.]");
        }
    }

    public void addScheduleIncheonConcert(String title, String support){
        if(this.incheonConcert != null){
            this.incheonConcert.addSchedule(title, support);
        }else{
            System.out.println("[인천 콘서트 계획은 없습니다.]");
        }
    }

    public void addScheduleDaejeonConcert(String title, String support){
        if(this.daejeonConcert != null){
            this.daejeonConcert.addSchedule(title, support);
        }else{
            System.out.println("[대전 콘서트 계획은 없습니다.]");
        }
    }

    public void addScheduleDaeguConcert(String title, String support){
        if(this.daeguConcert != null){
            this.daeguConcert.addSchedule(title, support);
        }else{
            System.out.println("[대구 콘서트 계획은 없습니다.]");
        }
    }

    public void addScheduleGwangjuConcert(String title, String support){
        if(this.gwangjuConcert != null){
            this.gwangjuConcert.addSchedule(title, support);
        }else{
            System.out.println("[광주 콘서트 계획은 없습니다.]");
        }
    }

    public void addScheduleBusanConcert(String title, String support){
        if(this.busanConcert != null){
            this.busanConcert.addSchedule(title, support);
        }else{
            System.out.println("[부산 콘서트 계획은 없습니다.]");
        }
    }

    public void startConcert(String advContext){
        if(this.seoulConcert != null){
            this.seoulConcert.concertStart();
            this.seoulConcert.advertise(advContext);
            this.seoulConcert.concertEnd();
        }

        if(this.incheonConcert != null){
            this.incheonConcert.concertStart();
            this.incheonConcert.concertEnd();
        }

        if(this.daejeonConcert != null){
            this.daejeonConcert.concertStart();
            this.daejeonConcert.concertEnd();
        }

        if(this.daeguConcert != null){
            this.daeguConcert.concertStart();
            this.daeguConcert.concertEnd();
        }

        if(this.gwangjuConcert != null){
            this.gwangjuConcert.concertStart();
            this.gwangjuConcert.advertise(advContext);
            this.gwangjuConcert.concertEnd();
        }

        if(this.busanConcert != null){
            this.busanConcert.concertStart();
            this.busanConcert.advertise(advContext);
            this.busanConcert.concertEnd();
        }
    }
}
```

SingerFacade.java

Facade Class는 각 패키지 별로 분산되어 있는 Sub System Interface를 불러와서 각 객체 별 상호 동작을 하는 역할을 한다.

여기서는 가수가 각 지역 별 콘서트 예정을 잡으면 그 지역 Enumeration에 맞춰서 객체를 생성한다. 그 다음에는 각 지역 별 공연 세부 정보를 설정하고 난 후에 실제로 공연 일정에 맞춰서 움직이는 것으로 끝나는 아주 간략한 클래스이다.

**Client Class 작성**

```
package net.kang.facade.client;

import net.kang.facade.enumeration.RegionType;
import net.kang.facade.facade_object.SingerFacade;

import java.util.Arrays;

public class MainClient {
    public static void main(String[] args){
        System.out.println("[가수 Dok2가 콘서트를 진행합니다.]");

        SingerFacade dok2 = new SingerFacade(Arrays.asList(RegionType.SEOUL, RegionType.DAEJEON, RegionType.GWANGJU, RegionType.BUSAN));
        dok2.addScheduleSeoulConcert("Dok2 With Jay Park", "1llionaire, AOMG");
        dok2.addScheduleDaejeonConcert("Young King Young Boss", "Adidas");
        dok2.addScheduleGwangjuConcert("Dok2 Gwangju Come Back", "Rolex");
        dok2.addScheduleBusanConcert("Hometown Comin' Back", "부산광역시청");

        dok2.startConcert("Dok2 앨범 새로 나왔으니 털ㄴ업~");
        System.out.println();

        System.out.println("[가수 The Quiett가 콘서트를 진행합니다.]");

        SingerFacade theQuiett = new SingerFacade(Arrays.asList(RegionType.SEOUL, RegionType.INCHEON, RegionType.DAEGU, RegionType.GWANGJU));
        theQuiett.addScheduleSeoulConcert("Beverly 1lls With Seoul", "서울특별시청");
        theQuiett.addScheduleIncheonConcert("International Rap Battle", "Adidas");
        theQuiett.addScheduleDaeguConcert("Prima Vista With Quiett", "1llionaire");
        theQuiett.addScheduleGwangjuConcert("The Quiett Come Back", "1llionaire, AMBITION");

        theQuiett.startConcert("신곡 아직 안 나왔어요~");
        System.out.println();
    }
}
```

MainClient.java

클라이언트 측에서는 패키지 내의 행위들을 이용하게 만드는 Facade Object로 접근을 한다. 여기서 가수 Dok2와 The Quiett Object를 생성하고 난 후에 콘서트 지역 설정 이후에 콘서트 정보를 설정하고 난 후에 실제로 콘서트 일정을 따르게 한다.

Facade Object을 이용하게 된다면 Client 측에서 Sub System을 직접 불러올 필요 없이 이 내부에서 Sub System의 행위를 처리하면 된다.

실행 결과는 다음과 같다.

```
[가수 Dok2가 콘서트를 진행합니다.]
[서울] 서울 콘서트 계획을 잡습니다.
[대전] 대전 콘서트 계획을 잡습니다.
[광주] 광주 콘서트 계획을 잡습니다.
[부산] 부산 콘서트 계획을 잡습니다.
[서울] Dok2 With Jay Park 콘서트로 결정했습니다. - 1llionaire, AOMG 후원
[대전] Young King Young Boss 콘서트로 결정했습니다. - Adidas 후원
[광주] Dok2 Gwangju Come Back 콘서트로 결정했습니다. - Rolex 후원
[부산] Hometown Comin' Back 콘서트로 결정했습니다. - 부산광역시청 후원
[서울] Dok2 With Jay Park 콘서트가 시작됩니다. - 1llionaire, AOMG 후원
[서울] Dok2 With Jay Park 콘서트 중 광고 - Dok2 앨범 새로 나왔으니 털ㄴ업~
[서울] Dok2 With Jay Park 콘서트가 끝났습니다. - 1llionaire, AOMG 후원
[대전] Young King Young Boss 콘서트가 시작됩니다. - Adidas 후원
[대전] Young King Young Boss 콘서트가 끝났습니다. - Adidas 후원
[광주] Dok2 Gwangju Come Back 콘서트가 시작됩니다. - Rolex 후원
[광주] Dok2 Gwangju Come Back 콘서트 중 광고 - Dok2 앨범 새로 나왔으니 털ㄴ업~
[광주] Dok2 Gwangju Come Back 콘서트가 끝났습니다. - Rolex 후원
[부산] Hometown Comin' Back 콘서트가 시작됩니다. - 부산광역시청 후원
[부산] Hometown Comin' Back 콘서트 중 광고 - Dok2 앨범 새로 나왔으니 털ㄴ업~
[부산] Hometown Comin' Back 콘서트가 끝났습니다. - 부산광역시청 후원

[가수 The Quiett가 콘서트를 진행합니다.]
[서울] 서울 콘서트 계획을 잡습니다.
[인천] 인천 콘서트 계획을 잡습니다.
[대구] 대구 콘서트 계획을 잡습니다.
[광주] 광주 콘서트 계획을 잡습니다.
[서울] Beverly 1lls With Seoul 콘서트로 결정했습니다. - 서울특별시청 후원
[인천] International Rap Battle 콘서트로 결정했습니다. - Adidas 후원
[대구] Prima Vista With Quiett 콘서트로 결정했습니다. - 1llionaire 후원
[광주] The Quiett Come Back 콘서트로 결정했습니다. - 1llionaire, AMBITION 후원
[서울] Beverly 1lls With Seoul 콘서트가 시작됩니다. - 서울특별시청 후원
[서울] Beverly 1lls With Seoul 콘서트 중 광고 - 신곡 아직 안 나왔어요~
[서울] Beverly 1lls With Seoul 콘서트가 끝났습니다. - 서울특별시청 후원
[인천] International Rap Battle 콘서트가 시작됩니다. - Adidas 후원
[인천] International Rap Battle 콘서트가 끝났습니다. - Adidas 후원
[대구] Prima Vista With Quiett 콘서트가 시작됩니다. - 1llionaire 후원
[대구] Prima Vista With Quiett 콘서트가 끝났습니다. - 1llionaire 후원
[광주] The Quiett Come Back 콘서트가 시작됩니다. - 1llionaire, AMBITION 후원
[광주] The Quiett Come Back 콘서트 중 광고 - 신곡 아직 안 나왔어요~
[광주] The Quiett Come Back 콘서트가 끝났습니다. - 1llionaire, AMBITION 후원
```

## Principle Of Least Knowledge

최소 지식 원칙의 목적은 **객체 사이의 상호작용은 최소한으로 줄이는 것** 이다.

Facade Object의 목적이 Sub System을 일부 이용한 최소의 Interface로 제공하는 점에서 이 원칙이 적용된다.

여러 클래스들이 서로 복잡하게 의존한다면 시스템의 한 부분을 변경할 때 다른 부분도 고쳐야 하는 상황이 발생할 수 있다.

예를 들어 무전기와 스마트폰의 통신 작동 여부를 확인한다고 생각해보자. 무전기를 이용하기 위해 각 무전기 별 주파수와 통신 일련 코드를 각각 맞춰서 테스팅을 해야 하니 메뉴얼이 복잡하다. 그렇지만 스마트폰은 오로지 상대방 전화번호만 입력하여 전화만 걸면 끝이다. 이런 차이이다.

그래서 서로 의존을 하지 않고 다른 객체에 영향력을 행사하는 방법으로는 다음과 같다.

- 객체 자체만 호출한다.
- 메소드에 매개변수로 전달된 객체를 호출한다.
- 그 메소드에서 생성하거나 객체를 만든 객체를 호출한다.
- 그 객체에 속하는 구성요소를 호출한다.

```
// A1
public int getChairmanGrade(){
    Student student = department.getChairman();
    return student.getGrade();
}

// A2
public int getChairmanGrade(){
    return department.getChairmanGrade();
}
```

A1과 A2를 비교한다면 크게 차이가 없어 보인다. 그렇지만 최소 지식 원칙을 지킨 메소드는 바로 A2이다. A1에서는 학과 별 학생 회장을 학생 객체로 다이렉트로 넣었지만, A2에서는 학과 별 학생 회장 정보를 아예 맴버 변수로 설정하였다. 이처럼 작성하면 의존하는 클래스의 수를 줄일 수 있지만, **Wrapper Class**를 더 만들어야 하는 단점을 겪어야 한다.

## Comparison Of Another Patterns

- Adapter Pattern - Facade Pattern은 Sub System에서 쉽고 단순한 Interface를 이용할 때 쓰기 때문에 1:N 접목을 제공한다. 그러나 Adapter Pattern은 A객체와 B객체 간 Interface의 차이를 극복하는 목적만 가지기 때문에 1:1 접목만 제공을 하고, 이의 **적응**을 도와주는 역할만 한다.

- Factory Pattern - Factory Pattern이 하는 행위 중에 객체 종류 별로 생성하는 점이 유사할 수 있지만, Facade Pattern은 자체 내부에서 생성을 하고 대신에 이용하는 목적으로만 두고, Factory Pattern은 객체 종류 별로 생성하는 목적으로 두는 것이다.

## References

- https://ko.wikipedia.org/wiki/%ED%8D%BC%EC%82%AC%EB%93%9C_%ED%8C%A8%ED%84%B4 - Facade Pattern 위키 백과 참조

- http://leetaehoon.tistory.com/65 - Facade Pattern 개념에 대해 간략한 예제와 정리 참조

- https://m.blog.naver.com/PostView.nhn?blogId=premiummina&logNo=220630049767 - 최소 지식 원칙 개념 참조

## Post Script
- 여기에 작성된 내용 이외에도 필요한 개념들을 발견하게 된다면 언제든지 갱신될 수 있습니다.
- 이 강의노트 개념에서 더욱 다뤄주면 좋은 개념들이나 오탈자가 있으시면 KangBakSa Issues에 올려주세요.
- 그리고 객체 지향 패턴은 Design_Pattern Repository에 추가로 저장하고 있습니다. 나중에 완성되면, public으로 바꿔 소스 코드와 연동하겠습니다. 