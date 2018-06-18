# Observer Pattern Issues

![seohaeline](/Application_Computer_Science/8_Object_Oriented_Pattern/img/seohaeline.png)

지난 주에 소사역부터 원시역까지 서해선의 일부가 개통되었다. 지하철이나 기차에 있는 모든 역들은 각 지역의 인프라 상황에 맞춰서 개통되는 경우가 대다수이다. 예를 들어 1980년대에 강남 지역 개발로 인한 2호선 강남 경유(잠실, 강남, 서울대, 구로공단 etc.) 순환선 개통, 90년대 일산 신도시(3호선 일산선)와 분당 신도시(분당선)에 맞춘 국철 개통, 이번 평창 올림픽을 대비해서 서울역에서 강릉역까지 KTX를 직통으로 개통한 사례 등이 있다.

이처럼 지하철 노선이 신축될 때마다 지역 별 영향도를 반영한 개통이 이뤄지듯이, 객체와 객체 사이에서 다양한 처리를 할 필요가 있다. 이를 위해 상태가 변하면 알려야 하는 기능을 구현해야 한다. 이럴 때 적용할 수 있는 개념이 바로 Observer Pattern이다. 이번 스터디 노트에서는 Observer Pattern을 다뤄보도록 하자. 

## What Is Observer Pattern?

![Observer_Pattern_UML](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Observer_Pattern_UML.png)

Observer Pattern은 소프트웨어 디자인 패턴 중에서 **동작 패턴(Behavioral Patterns)** 을 구성하는 방법 중 하나이다. 여기서 Observer는 영어로 관측자라는 뜻이다. 

## Example Of Observer Pattern

![Observer_Pattern_Example](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Observer_Pattern_Example.png)

실생활에서는 이럴 일이 없겠지만, 같은 시간 대에 모든 지하철 역에 손님들이 승차, 하차, 환승하는 수치를 모두 변경 시키는 사례로 작성하였다. 자세한 내용은 내일 저녁부터 추가로 작성하겠다.

```
package net.kang.observer.enumeration;

public enum SubwayStatus {
    RIDE, QUIT, TRANSFER
}
```
SubwayStatus.java


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
    private List<SubwayObserver> subjectList;

    public LineSubjectObject(String lineName){
        this.lineName = lineName;
        this.status = null;
        subjectList = new ArrayList<SubwayObserver>();
    }

    @Override
    public void add(SubwayObserver so) {
        this.subjectList.add(so);
    }

    @Override
    public void remove(SubwayObserver so) {
        if(this.subjectList.contains(so)){
            this.subjectList.remove(so);
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
        for(SubwayObserver so : this.subjectList){
            System.out.println(so.statusCount(this.status));
        }
    }

    public void statusChangePrint(int person){
        for(SubwayObserver so : this.subjectList){
            System.out.println(so.statusChange(this.status, person));
        }
    }
}
```
LineSubjectObject.java


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

## RxJava Observable / Observer



## References
- https://ko.wikipedia.org/wiki/%EC%98%B5%EC%84%9C%EB%B2%84_%ED%8C%A8%ED%84%B4 - Observer Pattern 위키 백과 참조
- http://flowarc.tistory.com/entry/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4-%EC%98%B5%EC%A0%80%EB%B2%84-%ED%8C%A8%ED%84%B4Observer-Pattern - Observer Pattern 내용을 자세히 설명한 블로그 글 참조