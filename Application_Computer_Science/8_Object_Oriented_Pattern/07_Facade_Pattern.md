# Façade Pattern Issues

![Dok2_Concert](/Application_Computer_Science/8_Object_Oriented_Pattern/img/dok2_concert.jpg)

> Turn up, 늘 분위기는 살리고<br/>
> 서울에서 world wide, 모두 난리도<br/>
> 아니거든. 내가 뜨면 넌 할 일 없어지는 중<br/>
> My bad, homie. Adios<br/>
> **L4L(Looking For Love), Dok2 Lyrics 서론.**

래퍼 Dok2가 처음 데뷔했을 때 부터 억만 장자가 된 비결이 무엇일까? 엄청난 음반 작업으로 볼 수 있지만, 또 다른 이유가 바로 전국 콘서트를 수 십 번 다니면서 공연비로 어마한 수익을 얻은 것도 큰 몫을 하였다. 위의 가사 중에 서울에서 world wide 부분을 읽어보면 Dok2가 자기 신곡이 항상 나올 때마다 전국을 돌아다니면서 콘서트를 한 번만 해도 곧 뜬다는 의미로 볼 수 있다.

객체 지향 프로그래밍에서도 Dok2가 전국을 돌며 콘서트를 하듯이, Facade Object를 이용해 각각 흩어져 있는 객체를 직접 가져와서 이용하는 패턴이 바로 Facade Pattern이다.

이번 노트에서는 Façade Pattern에 대하여 다룰 것인데, 생각보다 쉬운 구성으로 되어 있어, 이 개념에 대한 단점을 따져보는 것을 목표를 삼아서 공부해보도록 하자.

## What Is Façade Pattern?

![Facade_Pattern_UML](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Facade_Pattern_UML.png)

## Example Of Façade Pattern

![Facade_Pattern_Example](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Facade_Pattern_Example.png)

**Enumeration Class 작성**

```
package net.kang.facade.enumeration;

public enum RegionType {
    SEOUL, INCHEON, DAEJEON, DAEGU, GWANGJU, BUSAN;
}
```

RegionType.java

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


## Comparison Of Another Patterns


## References

- https://ko.wikipedia.org/wiki/%ED%8D%BC%EC%82%AC%EB%93%9C_%ED%8C%A8%ED%84%B4 - Facade Pattern 위키 백과 참조

- http://leetaehoon.tistory.com/65 - Facade Pattern 개념에 대해 간략한 예제와 정리 참조

