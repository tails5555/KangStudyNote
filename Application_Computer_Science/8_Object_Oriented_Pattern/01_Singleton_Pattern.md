# Singleton Pattern Issues

Software Design Pattern에서 단골로 이용하는 Pattern이 바로 Singleton Pattern이다. 우리가 Java에서 생성자를 이용해서 객체를 생성하는 경우에는 셀 수 없을 정도의 객체를 형성할 수 있다. 그러나 Singleton은 이러한 개념이 전혀 아니다. 전역 변수 같이 객체를 1번만 생성하고 프로세스가 종료될 때까지 유지하는 자원과 같은 역할을 할 때 필요한 패턴이다.

이번 노트에서는 Singleton Pattern이 어떻게 적용되는지, 그리고 동기화를 보장할 수 있는 방법을 고려해보는 시간을 가져보도록 하자.

## What is Singleton?

![hotel_key](/Application_Computer_Science/8_Object_Oriented_Pattern/img/hotel_key.jpg)

> Singleton 패턴은 찜질방이나 캡슐 호텔 카운터에서 본인의 신발을 넣은 열쇠를 맡기고 옷장 열쇠를 받아가는 원리와 똑같다.<br/>
> 동기화에 대한 문제를 생각한다면 손님이 여러 명이 오는 경우에 예약한 손님에 따른 방 배정 정책을 정하는 면에서 힘든 문제와 비슷하다.

![singleton_pattern_uml](/Application_Computer_Science/8_Object_Oriented_Pattern/img/singleton_pattern_uml.png)

Singleton Pattern은 **생성 패턴(Constructor Pattern)** 을 구성하는 기본적인 방법 중 하나이다.

찜질방 카운터에 있는 본인의 신발 열쇠함은 1개만 존재한다. 본인의 옷장 열쇠 번호를 주고 나갈 때 카운터에서는 본인이 신었던 신발 열쇠 키를 줄 때도(옷장 번호와 신발 번호는 서로 다를 수 있어도) 본인의 신발 열쇠 번호와 일치한다. 

이처럼 Singleton Pattern은 생성자가 여러 차례 호출되어도 실제로 생성되는 객체는 **1개**이다. 즉, 인스턴스를 1개만 만들어서 사용하기 위한 패턴으로 볼 수 있다. 이를 이용한다면 어디든지 그 인스턴스에 접근할 수 있도록 하기 위한 패턴인데 주로 활용하는 사례는 다음과 같다.

> Thread Pool, Cache, 사용자 설정, Log 반환, 레지스트리 설정 etc.<br/>
> 참고로 `Python`에서 객체 만드는 과정 그 자체가 Singleton Pattern을 접목한다.

위에서 적용되는 객체는 1개만 있어도 무방하다. 그러나 2개 이상의 인스턴스를 만든다면 자원 낭비를 야기할 수 있다.

## Main Singleton Example
```
public class Singleton{
    // 인스턴스를 저장하기 위한 변수로 이용된다.
    // 외부에서 불러올 수 없도록 private로 지정해야 한다.
    private static Singleton uniqueInstance;

    // 생성자도 마찬가지로 private로 선언해야 한다.
    private Singleton(){
        ...
    }

    // 클래스의 유일한 인스턴스를 반환하는 메소드. 이는 static으로 적용한다.
    public static Singleton getInstance(){
        // 인스턴스가 존재하지 않으면 생성한다.
        // 하지만 인스턴스가 이미 존재하는 상태이면 패스하고 넘어간다.
        if(uniqueInstance == null){
            uniqueInstance = new Singleton();
        }
        return uniqueInstance;
    }
}
```
이를 적용하기 위해서는 생성자도 마찬가지로 private로 선언해야 한다. 인스턴스 생성은 getInstance() 메소드에서 반환 받아서 사용되는데 uniqueInstance가 존재하는 경우에는 그냥 넘어가고, null이면 생성하면 된다. 이러한 생성 방식을 lazy instantiation(게으른 인스턴스 생성)으로 칭하는데, 인스턴스를 미리 생성하지 않고 필요한 상황일 때 즉시 생성하는 방법이다. 이러한 방법은 객체가 위에서 언급한 자원과 관련된 경우에 대하여 적합하다.

그렇지만 Multi-Thread를 접목한다면, 유일한 인스턴스를 가질 수 있을까? 찜질방에서 손님이 친한 사람끼리 들어오는 경우를 생각해보자. 그 손님들은 찜질방을 이용하던 중간에 심부름으로 옷장에 있는 지갑을 꺼낼 때 번호가 바뀔 수도 있다는 사실을 감안한다면 순서가 엇갈리게 되어 혼돈을 가져올 수도 있다. 이처럼 Multi-Thread에서 자원과 관련된 설정을 할 때 인스턴스가 2개 이상 생성될 수 있다는 사실도 감안해야 한다. 이번에는 Multi-Thread를 접목한 Singleton Pattern을 구현해보도록 하자.

## Singleton Pattern in Multi-Thread

```
// A1
public class Singleton{
    private static Singleton uniqueInstance;

    private Singleton(){
        ...
    }

    public static synchronized Singleton getInstance(){
        if(uniqueInstance==null){
            uniqueInstance = new Singleton();
        }
        return uniqueInstance;
    }
}

// A2
public class Singleton{
    private static Singleton uniqueInstance = new Singleton();

    private Singleton(){
        ...
    }

    public static synchronized Singleton getInstance(){
        return uniqueInstance;
    }
}
```

Multi-Thread 프로그램에서 인스턴스의 동기화를 보장하기 위해서는 A1과 A2 처럼 synchronized 키워드를 추가하면 된다. 그러면 한 Thread가 메소드 이용을 끝내기 전까지 다른 Thread는 대기 상태로 돌아가게 된다. 

A1에서는 메소드가 동시에 실행하는 일은 다행히도 없지만, 대기하는 Thread의 Queue 별로 진행 완료를 하는 점에서 Application의 속도가 저하하는 문제가 발생된다.

A2에서 정적 초기화 시에 생성하는 방법을 이용하면 JVM(Java Virtual Machine)에서 유일한 인스턴스를 생성해줌으로서 다수의 인스턴스가 생성될지 모른다는 문제를 어느 정도 해결하였다. 그러나 getInstance() 메소드에서 많은 자원을 필요한다면 로딩 시 속도 저하의 문제도 고려해야 한다.

마치 찜질방에서 친한 손님끼리 심부름으로 인한 열쇠 공유하는 일이 발생하지 않고 각자 열쇠 번호를 기억하여 찜질방 업무에 방해를 끼치지 않게 하는 점에서 다행으로 볼 수 있다. 그러나 친한 손님 끼리 번호가 서로 랜덤이면 곤란해진다. 찜질방이면 어느 곳으로 배정해도 상관 없지만, 캡슐 호텔에서는 웬만하면 친분 있는 사람끼리 뭉치는 것을 더욱 선호한다. 그러면 서로 친한 사람끼리 잘 수 있고, 카운터 담당 직원도 관리하기 더욱 쉬워지는 일거양득의 효과를 거둘 수 있다. Thread에서도 대기 Queue에 있는 순서 별로 처리하는 것이 좋기 때문에 이를 더욱 보장하는 기법을 적용한 방법은 아래와 같다.

## Singleton Pattern in DCL(Double-Checking Locking)
```
public class Singleton{
    // volatile 추가
    private volatile static Singleton uniqueInstance;

    private Singleton(){
        ...
    }

    public static Singleton getInstance(){
        if(uniqueInstance == null){ // 인스턴스의 존재 여부 확인
            synchronized(Singleton.class){ // 동기화는 최초에만 진행한다.
                if(uniqueInstance == null) // 만의 하나로 다시 한 번 확인한다.
                    uniqueInstance = new Singleton();
            }
        }
        return uniqueInstance;
    }
}
```

이와 같이 Thread 들이 대기 Queue 순서 별로 생성 되었는지 확인하고, 생성되지 않은 경우에만 동기화를 하여 처음에만 동기화하고 나중에는 동기화하지 않는 방법이 DCL(Double-Checking Locking) 기법이다. 이를 이용한다면 Multi-Thread에서 적용할 수 있는 이득이 많아지지만, Multi-Processor(더욱 많아지는 프로세서 내부에서 발생)가 Shared-Memory(공유 메모리)를 사용하면서 문제가 발생하기 쉬운 단점이 있다. 즉, 안정성이 떨어질 수 있다는 점인데 Multi-Thread 환경에서 안정성을 보장하고 싶다면 이 방법은 지양하는 것이 좋겠다.

## Initalization On Demand Holder Idiom

```
public class Singleton{
    private Singleton(){
        ...
    }
    
    private static class LazyHolder{
        static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance(){
        return LazyHolder.INSTANCE;
    }
}
```

Singleton Pattern을 고전적으로 생성하는 방법에서 LazyHolder Inner Class를 추가하는 방법이다. 정적 초기화 시 인스턴스를 생성하고 getInstance 메소드에서는 해당 클래스에서 생성된 인스턴스를 반환하는 방법이다. 지금은 이 방법을 보편화하여 쓰고 있다.

Inner Class를 굳이 왜 쓰는 걸까? Java 버전이 무엇이든 떠나서 Idiom은 Java Virtual Machine의 Class Loader의 메커니즘과 Class의 Load 시점을 이용할 때 Inner Class를 생성한다면 Thread 간의 동기화 문제를 할 수 있기 때문이다.

## Enumeration Initialization
```
public enum Singleton{
    INSTANCE;
    public static Singleton getInstance(){
        return INSTANCE;
    }
}
```

Enumeration이 Singleton Pattern으로 사용할 수 있는데 **Multi-Thread로부터 안전**한 점, **단 한 번의 인스턴스 생성**을 보장하는 점, **Enumeration Value는 Java 전역에서 접근이 가능한 점**이 있고 구현이 간편하다는 것도 장점이다.

Enumeration 개념에 대해서 모른다면 한 번 읽어보길 권장한다.

## Singleton Pattern in Spring Framework

Java에서 Singleton을 구현하는 방법에 대해서는 위에 나와 있으니 더욱 자세한 설명은 생략하겠다.

Spring Framework에서는 DAO(Data Access Object)를 Singleton Class로 설정한 사례를 살펴보자.

```
public class MusicDAO {
    private static MusicDAO instance;
    private MusicDAO(ConnectionMaker connectionMaker){
        this.connectionMaker = connectionMaker;
    }
    public static synchronized MusicDAO getInstance(){
        if(instance == null) instance = new MusicDAO();
        return instance;
    }
}
```

Singleton 관련 소스 코드가 추가된 점에서 코드가 상당히 지저분하다. 그리고 private 생성자는 외부에서 호출이 불가능해서  DaoFactory에 MusicDAO Bean을 생성하여 ConnectionMaker Object를 넣는 것이 불가능하다. Singleton Pattern 구현 방식은 다음과 같은 문제가 발생하여 Spring Framework에서 Singleton Pattern을 회피하는 것이 좋다.


> - private 생성자를 가지고 있어서 상속할 수 없다.
>    - Singleton Pattern의 생성자는 private으로 제한을 두기 때문에 **상속이 불가능하여** 객체 지향의 이점인 다형성을 못 이용한다.
>
> - Singleton은 테스트를 하기 꺼려한다.
>    - Singleton Pattern 제조 방식은 제한적이기 때문에 Mock Object 등으로 대체하기 힘들다. Singleton 초기화 과정에서 생성자를 이용해 사용할 Object를 동적으로 주입하기 힘들기 때문에 JUnit Testing 작업할 때 아예 불가능하거나 한다 쳐도 완벽하진 않을 것이다.
>
> - 다중 Server 환경에서 Singleton이 1개만 만들어지는 것을 보장하지 못 한다.
>    - Java 언어를 이용한 Singleton Pattern 기법은 Server 환경에서 Singleton이 꼭 보장된다고 볼 수 없다. 여러 JVM에 분산되어 설치 되는 경우도 각각 독립적으로 Object가 생긴다.
>
> - Singleton 사용은 Global 상태를 만들어 객체 지향 프로그래밍에 어긋난다.
>    - Singleton Pattern은 사용하는 Client가 따로 정해지지 않았다. 즉 이 이야기는 Global 상태(전역 상태)로 자연스럽게 사용된다면 아무 객체나 자유롭게 접근하고, 수정하고, 💩싸는 짓거리는 객체 지향 프로그래밍에서 권장하지 않는다.
>
> 토비의 Spring 3.1 Vol.1 Singleton Pattern 언급 中.

## References
- https://ko.wikipedia.org/wiki/%EC%8B%B1%EA%B8%80%ED%84%B4_%ED%8C%A8%ED%84%B4 - Singleton 위키 백과 개념 참조
- http://friday.fun25.co.kr/blog/?p=312 - Singleton 패턴을 참고한 문서

## Post Script
- 여기에 작성된 내용 이외에도 필요한 개념들을 발견하게 된다면 언제든지 갱신될 수 있습니다.
- 이 강의노트 개념에서 더욱 다뤄주면 좋은 개념들이나 오탈자가 있으시면 KangBakSa Issues에 올려주세요.