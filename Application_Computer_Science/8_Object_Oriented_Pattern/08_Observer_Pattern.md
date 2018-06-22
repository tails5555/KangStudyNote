# Observer Pattern Issues

![seohaeline](/Application_Computer_Science/8_Object_Oriented_Pattern/img/seohaeline.png)

지난 주에 소사역부터 원시역까지 서해선의 일부가 개통되었다. 지하철이나 기차에 있는 모든 역들은 각 지역의 인프라 상황에 맞춰서 개통되는 경우가 대다수이다. 예를 들어 1980년대에 강남 지역 개발로 인한 2호선 강남 경유(잠실, 강남, 서울대, 구로공단 etc.) 순환선 개통, 90년대 일산 신도시(3호선 일산선)와 분당 신도시(분당선)에 맞춘 국철 개통, 이번 평창 올림픽을 대비해서 서울역에서 강릉역까지 KTX를 직통으로 개통한 사례 등이 있다.

이처럼 지하철 노선이 신축될 때마다 지역 별 영향도를 반영한 개통이 이뤄지듯이, 객체와 객체 사이에서 다양한 처리를 할 필요가 있다. 이를 위해 상태가 변하면 알려야 하는 기능을 구현해야 한다. 이럴 때 적용할 수 있는 개념이 바로 Observer Pattern이다. 이번 스터디 노트에서는 Observer Pattern을 다뤄보도록 하자. 

## What Is Observer Pattern?

![Observer_Pattern_UML](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Observer_Pattern_UML.png)

Observer Pattern은 소프트웨어 디자인 패턴 중에서 **동작 패턴(Behavioral Patterns)** 을 구성하는 방법 중 하나이다. 여기서 Observer는 영어로 관측자라는 뜻이다. 한 객체의 상태가 바뀌면 이 객체의 의존한 다른 객체들한테 자동으로 갱신해야 하는 1:1 혹은 1:N 의존성을 정의할 때 이용한다.

Observer Pattern을 이용할 때는 가능한 객체 사이의 결합(Coupling)도가 느슨해야 한다. 느슨한 결합을 이용하여 관계를 관리하는 것은 다음과 같은 장점을 가진다.

- Subject는 Observer의 구상 클래스가 무엇인지 굳이 알 필요까진 없다.
- Observer의 추가, 제거가 자유롭다.
- 새로운 Observer를 추가해도 주체를 변경할 필요가 없다.
- Subject와 Observer를 독립적으로 재사용 가능하다.
- 변경이 생겨도 서로에게 영향을 주지 않는다.

Observer Pattern은 JDK에서 가장 많이 사용되는 패턴이다. 쉽게 생각하면 Observer는 학교에서 학생들에게 학교 사항을 알리는 가정 통신문과 같은 역할을 한다.

## Example Of Observer Pattern

![Observer_Pattern_Example](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Observer_Pattern_Example.png)

실생활에서는 이럴 일이 없겠지만, 같은 시간 대에 모든 지하철 역에 손님들이 승차, 하차, 환승하는 수치를 모두 변경 시키는 사례로 작성하였다. 여기서 수치는 이용 객수이다. 위에서 Observer Pattern이 어떻게 적용할 수 있는지 아래 문장들로 살펴보자.

**SubwayStatus Enumeration 작성**

```
package net.kang.observer.enumeration;

public enum SubwayStatus {
    RIDE, QUIT, TRANSFER
}
```
SubwayStatus.java

SubwayStatus는 현재 지하철 승차, 하차, 환승 수를 설정한 값들을 Enumeration으로 정해둔다. 승차면 RIDE, 하차는 QUIT, 환승은 TRANSFER이다.


**Subject Interface 작성**

```
package net.kang.observer.subject_inferface;

import net.kang.observer.enumeration.SubwayStatus;
import net.kang.observer.observer_inferface.SubwayObserver;

public interface LineSubject {
    public void add(SubwayObserver so);
    public void remove(SubwayObserver so);
    public void getStatusInfo(SubwayStatus status);
    public void setStatusChange(SubwayStatus status, int person);
}
```

LineSubject.java

Line 객체는 각 지하철 별 노선 이름(1호선, 2호선, 서해선, 분당선 etc)을 설정할 때 적용할 객체이다.

이 인터페이스의 역할은 노선을 관리하는 최고 직원이 하는 역할과 유사하다.

- add(SubwayObserver so) 는 지하철 노선에 있는 역의 관찰자 객체를 추가한다.
- remove(SubwayObserver so) 는 지하철 노선에 있는 역의 관찰자 객체를 삭제한다.
- getStatusInfo(SubwayStatus status) 는 노선에 포함된 역들의 파견 관찰자가 승차, 하차, 환승 인원들을 총 정리한 정보를 서면으로 통보한다.
- setStatusChange(SubwayStatus status) 는 노선에 포함된 역들에 승차, 하차, 환승 역 별로 인원들을 한 노선의 평균 값으로 받아서 설정한다.

**Subject Object 작성**

```
package net.kang.observer.subject_object;

import net.kang.observer.enumeration.SubwayStatus;
import net.kang.observer.observer_inferface.SubwayObserver;
import net.kang.observer.subject_inferface.LineSubject;

import java.util.ArrayList;
import java.util.List;

public class LineSubjectObject implements LineSubject {
    private String lineName;
    private SubwayStatus status;
    private List<SubwayObserver> subwayList;

    public LineSubjectObject(String lineName){
        this.lineName = lineName;
        this.status = null;
        subwayList = new ArrayList<SubwayObserver>();
    }

    @Override
    public void add(SubwayObserver so) {
        this.subwayList.add(so);
    }

    @Override
    public void remove(SubwayObserver so) {
        if(this.subwayList.contains(so)){
            this.subwayList.remove(so);
        }
    }

    @Override
    public void getStatusInfo(SubwayStatus status) {
        this.status = status;
        this.statusInfoPrint();
    }

    @Override
    public void setStatusChange(SubwayStatus status, int person){
        this.status = status;
        this.statusChangePrint(person);
    }

    public void statusInfoPrint(){
        for(SubwayObserver so : this.subwayList){
            System.out.println(so.statusCount(this.status));
        }
    }

    public void statusChangePrint(int person){
        for(SubwayObserver so : this.subwayList){
            System.out.println(so.statusChange(this.status, person));
        }
    }
}
```
LineSubjectObject.java

위의 interface를 토대로 작성한 Subject Object는 다음과 같이 작성된다.

여기서 lineName은 노선 이름, status는 노선의 현재 상태(승차, 하차, 환승), subwayList는 노선 별 포함 된 역 목록이다.

노선을 담당하는 최고 직원이 각 역들을 담당하는 일부 직원들과 노선 관리자 중 파견 나간 관측자에게 변경 사항을 전달하는 역할을 한다.

**Observer Sub Interface 작성**

```
package net.kang.observer.observer_inferface;

public interface SubwayInterface {
    public void setSubwayName(String subwayName);
    public void setHasTransfer(boolean hasTransfer);
    public void setRideCount(int personCount);
    public void setTransferCount(int personCount);
    public void setQuitCount(int personCount);
    public String getSubwayName();
    public boolean getHasTransfer();
    public int getRideCount();
    public int getTransferCount();
    public int getQuitCount();
}
```
SubwayInterface.java

일반적으로 지하철 역을 이용할 때 같은 역이라고 생각할 수 있지만, 실제로 역도 한 노선의 일부를 담당하는 관리역, 평범한 보통역, 직원들이 아예 배치되지 않거나 배치 되어 있어도 말단 직원들이 담당하는 간이역이 존재한다. 관리역, 보통역, 간이역의 공통점은 역 이름, 환승 여부, 승차 인원, 환승 인원, 하차 인원을 조율하는 역할을 한다.

여기에 있는 업무는 각 역에 있는 직원들이 치루지만 이러한 일들은 역들에 있는 파견 관측자에게 전달 받고 치러져야 한다.

**Observer Main Interface 작성**

```
package net.kang.observer.observer_inferface;

import net.kang.observer.enumeration.SubwayStatus;

public interface SubwayObserver {
    public String getSubwayInfo();
    public String statusChange(SubwayStatus status, int personCount);
    public String statusCount(SubwayStatus status);
}
```
SubwayObserver.java

이는 각 역에 존재하는 파견 관찰자가 하는 역할들을 적었는데, getSubwayInfo는 이 역의 정보를 담당 역 직원에게 전달 받고 파견 관찰자에게 알리고 정보를 출력할 때 사용되고, statusChange는 역의 상태 변경이 들어오면 파견 관찰자가 역 직원들에게 변경을 알리고, statusCount는 파견 관찰자가 노선 관리자에게 승, 하차, 환승 인원들의 정보를 보고할 때 이용한다.


**Observer Object 작성**

```
package net.kang.observer.observer_object;

import net.kang.observer.enumeration.SubwayStatus;
import net.kang.observer.observer_inferface.SubwayInterface;
import net.kang.observer.observer_inferface.SubwayObserver;
import net.kang.observer.subject_inferface.LineSubject;

// 보통역 구현 객체. 보통역이 2번째인데 여기서는 간이배치역도 보통역에 해당된다고 가정하자.
public class GeneralSubwayObject implements SubwayObserver, SubwayInterface {
    private boolean hasTransfer;
    private String subwayName;
    private int rideCount;
    private int transferCount;
    private int quitCount;

    public GeneralSubwayObject(LineSubject lineSubject, String subwayName, boolean hasTransfer){
        this.setSubwayName(subwayName);
        this.setHasTransfer(hasTransfer);
        this.setRideCount(0);
        if(this.getHasTransfer()){
            this.setTransferCount(0);
        }else{ // 만일 환승역이 아니면, 이 변수는 음수로 체킹하여 안 이용한다.
            this.setTransferCount(Integer.MIN_VALUE);
        }
        this.setQuitCount(0);
        lineSubject.add(this);
    }

    @Override
    public String getSubwayInfo() {
        return String.format("[보통역] %s%s", this.getSubwayName(), (this.getHasTransfer()) ? "[환승]" : "");
    }

    @Override
    public String statusChange(SubwayStatus status, int personCount) {
        switch(status){
            case RIDE :
                this.setRideCount(this.getRideCount() + personCount);
                return String.format("%s - %d명 승차합니다.", this.getSubwayInfo(), personCount);
            case TRANSFER :
                if(this.getHasTransfer()) {
                    this.setTransferCount(this.getTransferCount() + personCount);
                    return String.format("%s - %d명 환승합니다.", this.getSubwayInfo(), personCount);
                }
                else {
                    return String.format("%s - 환승역이 아닙니다.", this.getSubwayInfo());
                }
            case QUIT :
                this.setQuitCount(this.getQuitCount() + personCount);
                return String.format("%s - %d명 하차합니다.", this.getSubwayInfo(), personCount);
        }
        return null;
    }

    @Override
    public String statusCount(SubwayStatus status) {
        switch(status){
            case RIDE :
                return String.format("%s - 현재까지 %d명 승차했습니다.", this.getSubwayInfo(), this.getRideCount());
            case TRANSFER :
                if(this.getHasTransfer()) {
                    return String.format("%s - 현재까지 %d명 환승했습니다.", this.getSubwayInfo(), this.getTransferCount());
                }else{
                    return String.format("%s - 환승역이 아닙니다.", this.getSubwayInfo());
                }
            case QUIT :
                return String.format("%s - 현재까지 %d명 하차했습니다.", this.getSubwayInfo(), this.getQuitCount());
        }
        return null;
    }

    @Override
    public void setSubwayName(String subwayName){
        this.subwayName = subwayName;
    }

    @Override
    public void setHasTransfer(boolean hasTransfer){
        this.hasTransfer = hasTransfer;
    }

    @Override
    public void setRideCount(int personCount) {
        this.rideCount = personCount;
    }

    @Override
    public void setTransferCount(int personCount) {
        this.transferCount = personCount;
    }

    @Override
    public void setQuitCount(int personCount) {
        this.quitCount = personCount;
    }

    @Override
    public String getSubwayName(){
        return this.subwayName;
    }

    @Override
    public boolean getHasTransfer(){
        return this.hasTransfer;
    }

    @Override
    public int getRideCount() {
        return this.rideCount;
    }

    @Override
    public int getQuitCount() {
        return this.quitCount;
    }

    @Override
    public int getTransferCount() {
        return this.transferCount;
    }
}
```
GeneralSubwayObject.java

여기서는 일반 지하철역 객체를 작성하였지만, 배치 지하철역, 관리 지하철역(노선의 일부를 나뉘어서 담당하는 대규모 역) 객체도 위에서 작성한 인터페이스를 토대로 반영하였다.

getSubwayInfo, statusChange, statusCount 메소드는 각 역에 있는 파견 관찰자가 진행하는 함수이지만, 위에서 언급했듯이 역을 담당하는 직원들에게 각 역의 통계를 측정하거나 얻고 난 후에 임무를 치러야 한다. 여기서 SubwayObserver Interface를 이용하여 Subject 객체와 Observer 객체의 결합도를 약하게 두는 것을 확인할 수 있다.

MainClient 측에서 노선 하나를 설치하고 각 역들을 노선에 포함 시키고 상태 변화를 주어 확인해보자.

**Main Client 작성**

```
package net.kang.observer.client;

import net.kang.observer.observer_inferface.SubwayObserver;
import net.kang.observer.observer_object.BatchSubwayObject;
import net.kang.observer.observer_object.GeneralSubwayObject;
import net.kang.observer.observer_object.ManageSubwayObject;
import net.kang.observer.subject_object.LineSubjectObject;

import static net.kang.observer.enumeration.SubwayStatus.QUIT;
import static net.kang.observer.enumeration.SubwayStatus.RIDE;
import static net.kang.observer.enumeration.SubwayStatus.TRANSFER;

public class MainClient {
    public static void main(String[] args){
        LineSubjectObject metro2 = new LineSubjectObject("2호선");

        SubwayObserver m201 = new ManageSubwayObject(metro2,"시청역", true);
        SubwayObserver m208 = new GeneralSubwayObject(metro2, "왕십리역", true);
        SubwayObserver m211 = new GeneralSubwayObject(metro2, "성수역", false);
        SubwayObserver m214 = new BatchSubwayObject(metro2, "강변역", false);
        SubwayObserver m216 = new GeneralSubwayObject(metro2, "잠실역", true);
        SubwayObserver m218 = new BatchSubwayObject(metro2, "종합운동장역", true);
        SubwayObserver m220 = new BatchSubwayObject(metro2, "선릉역", true);
        SubwayObserver m222 = new GeneralSubwayObject(metro2, "강남역", true);

        System.out.println("[2호선 1000명 승차]");
        metro2.setStatusChange(RIDE, 1000);
        System.out.println();

        System.out.println("[2호선 3000명 하차]");
        metro2.setStatusChange(QUIT, 3000);
        System.out.println();

        System.out.println("[2호선 3000명 승차]");
        metro2.setStatusChange(RIDE, 3000);
        System.out.println();

        System.out.println("[2호선 2000명 환승]");
        metro2.setStatusChange(TRANSFER, 2000);
        System.out.println();

        System.out.println("[2호선 승차 인원 통계]");
        metro2.getStatusInfo(RIDE);
        System.out.println();

        System.out.println("[2호선 하차 인원 통계]");
        metro2.getStatusInfo(QUIT);
        System.out.println();

        System.out.println("[2호선 환승 인원 통계]");
        metro2.getStatusInfo(TRANSFER);
        System.out.println();
    }
}
```
MainClient.java

Client 측에서 Subject를 생성하여 승차, 하차, 환승 상태로 각각 변경 시키고 이에 해당되는 인원들을 집계하여 정리한 결과는 다음과 같다. 실행 결과는 아래와 같이 출력된다.

```
[2호선 1000명 승차]
[관리역] 시청역[환승] - 1000명 승차합니다.
[보통역] 왕십리역[환승] - 1000명 승차합니다.
[보통역] 성수역 - 1000명 승차합니다.
[간이역] 강변역 - 1000명 승차합니다.
[보통역] 잠실역[환승] - 1000명 승차합니다.
[간이역] 종합운동장역[환승] - 1000명 승차합니다.
[간이역] 선릉역[환승] - 1000명 승차합니다.
[보통역] 강남역[환승] - 1000명 승차합니다.

[2호선 3000명 하차]
[관리역] 시청역[환승] - 3000명 하차합니다.
[보통역] 왕십리역[환승] - 3000명 하차합니다.
[보통역] 성수역 - 3000명 하차합니다.
[간이역] 강변역 - 3000명 하차합니다.
[보통역] 잠실역[환승] - 3000명 하차합니다.
[간이역] 종합운동장역[환승] - 3000명 하차합니다.
[간이역] 선릉역[환승] - 3000명 하차합니다.
[보통역] 강남역[환승] - 3000명 하차합니다.

[2호선 3000명 승차]
[관리역] 시청역[환승] - 3000명 승차합니다.
[보통역] 왕십리역[환승] - 3000명 승차합니다.
[보통역] 성수역 - 3000명 승차합니다.
[간이역] 강변역 - 3000명 승차합니다.
[보통역] 잠실역[환승] - 3000명 승차합니다.
[간이역] 종합운동장역[환승] - 3000명 승차합니다.
[간이역] 선릉역[환승] - 3000명 승차합니다.
[보통역] 강남역[환승] - 3000명 승차합니다.

[2호선 2000명 환승]
[관리역] 시청역[환승] - 2000명 환승합니다.
[보통역] 왕십리역[환승] - 2000명 환승합니다.
[보통역] 성수역 - 환승역이 아닙니다.
[간이역] 강변역 - 환승역이 아닙니다.
[보통역] 잠실역[환승] - 2000명 환승합니다.
[간이역] 종합운동장역[환승] - 2000명 환승합니다.
[간이역] 선릉역[환승] - 2000명 환승합니다.
[보통역] 강남역[환승] - 2000명 환승합니다.

[2호선 승차 인원 통계]
[관리역] 시청역[환승] - 현재까지 4000명 승차했습니다.
[보통역] 왕십리역[환승] - 현재까지 4000명 승차했습니다.
[보통역] 성수역 - 현재까지 4000명 승차했습니다.
[간이역] 강변역 - 현재까지 4000명 승차했습니다.
[보통역] 잠실역[환승] - 현재까지 4000명 승차했습니다.
[간이역] 종합운동장역[환승] - 현재까지 4000명 승차했습니다.
[간이역] 선릉역[환승] - 현재까지 4000명 승차했습니다.
[보통역] 강남역[환승] - 현재까지 4000명 승차했습니다.

[2호선 하차 인원 통계]
[관리역] 시청역[환승] - 현재까지 3000명 하차했습니다.
[보통역] 왕십리역[환승] - 현재까지 3000명 하차했습니다.
[보통역] 성수역 - 현재까지 3000명 하차했습니다.
[간이역] 강변역 - 현재까지 3000명 하차했습니다.
[보통역] 잠실역[환승] - 현재까지 3000명 하차했습니다.
[간이역] 종합운동장역[환승] - 현재까지 3000명 하차했습니다.
[간이역] 선릉역[환승] - 현재까지 3000명 하차했습니다.
[보통역] 강남역[환승] - 현재까지 3000명 하차했습니다.

[2호선 환승 인원 통계]
[관리역] 시청역[환승] - 현재까지 2000명 환승했습니다.
[보통역] 왕십리역[환승] - 현재까지 2000명 환승했습니다.
[보통역] 성수역 - 환승역이 아닙니다.
[간이역] 강변역 - 환승역이 아닙니다.
[보통역] 잠실역[환승] - 현재까지 2000명 환승했습니다.
[간이역] 종합운동장역[환승] - 현재까지 2000명 환승했습니다.
[간이역] 선릉역[환승] - 현재까지 2000명 환승했습니다.
[보통역] 강남역[환승] - 현재까지 2000명 환승했습니다.
```

## Java 8 Observable / Observer

Java 8에서는 Observer Pattern을 간략히 이용할 수 있게 Observable 클래스, Observer 인터페이스가 추가되었다.

Observable 클래스는 Subject 객체를 위한 수퍼 클래스, Observer 클래스는 Observer 객체를 구현하기 위한 인터페이스이다.

Observer Pattern의 기능 중에서 카운팅, 일괄 삭제, setChange() 메소드 등이 포함되어 있어 Observer 객체 내부에서는 update 메소드를 추가로 구현하여 이용하는 방법이다.

그렇지만 Subject 클래스를 구현할 때 상속 받아 구현해야 하는 문제가 있다. 이러한 문제점 때문에 아직까지는 Observer Pattern을 직접 구현하여 사용하는 방법을 이용하는 수 밖이다.

여기서 소스 코드를 작성하면 본론이 길어지기 때문에 Java 8 Observable, Observer를 이용한 예제는 아래를 참고하길 바란다.

https://github.com/tails5555/Design_Pattern/tree/master/src/net/kang/java8_observer

## References
- https://ko.wikipedia.org/wiki/%EC%98%B5%EC%84%9C%EB%B2%84_%ED%8C%A8%ED%84%B4 - Observer Pattern 위키 백과 참조
- http://flowarc.tistory.com/entry/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-%EC%98%B5%EC%A0%80%EB%B2%84-%ED%8C%A8%ED%84%B4Observer-Pattern - Observer Pattern 내용을 자세히 설명한 블로그 글 참조
- http://friday.fun25.co.kr/blog/?p=157 - Observer Pattern 예제를 참조한 글.

## Post Script
- 여기에 작성된 내용 이외에도 필요한 개념들을 발견하게 된다면 언제든지 갱신될 수 있습니다.
- 이 강의노트 개념에서 더욱 다뤄주면 좋은 개념들이나 오탈자가 있으시면 KangBakSa Issues에 올려주세요.
- 그리고 객체 지향 패턴은 Design_Pattern Repository에 추가로 저장하고 있습니다. 나중에 완성되면, public으로 바꿔 소스 코드와 연동하겠습니다. 