# Spring Basic Issues 정리
우리가 Spring Framework를 이용해서 프로젝트를 진행 해 왔지만, Spring Framework에서 변화해야 할 점을 생각해야 한다. 이 목적을 달성하기 위해서 무엇보다 Spring Framework에 대한 간단한 개념을 공부할 필요가 있다. 여기서는 Spring Framework 중에서 어떤 기능을 하는 것을 떠나 아주 기초적인 지식을 함유할 수 있도록 주요 개념들을 정리할 것이다.

## What is Spring Framework?
- `Spring Framework`는 `Java` 플랫폼을 위한 Open Source Application Framework이다.
- 종속 객체를 생성하고 이를 장난감 블럭처럼 조립하는 도구로 볼 수 있다.

- Java 객체(`POJO`, Plain Old Java Object)를 Java EE에 의존적이지 않게 연결을 해 주는 역할을 하는 Framework이다.

## Spring Framework Structure
![spring_framework_structure](/Application_Computer_Science/2_Spring_Framework/img/spring_framework_structure.png)

- Spring Core
    - Spring Framework의 뿌리 역할을 맡는 IoC(
Inversion of control, 흔히 쓰이는 DI(Dependency Injection) Pattern을 사례.) 기능을 지원한다.
    - BeanFactory를 기반으로 Bean 클래스들을 제어할 수 있는 기능을 지원한다.
- Spring Context
    - Spring Core의 비서 역할을 하는데 지원하는 기능 이외에 추가적인 기능들과 쉬운 개발이 가능하도록 지원한다.
    - Spring Project를 생성할 때 web.xml 파일에서 ContextLoaderListener, DispatcherServlet을 생성하게 되는데 이 두 개념을 root-context, servlet-context으로 칭한다. 이 두 개념이 Spring Core를 올바르게 작동하도록 좌우한다.
- Spring DAO
    - Data Access Object(DAO) 개념은 JDBC에서 공부를 할 때 Query 문을 작성하는 연습부터 많은 노가다를 가진 경험이 있을 것이다.
    - DAO 개발을 쉽고 일관된 방법으로 개발할 수 있도록 지원한다.
    - `mybatis`의 Mapper Interface, `JPA`의 Repository Interface 등이 있다. 
- Spring ORM
    - Object Relation Mapping(ORM) Framework와의 결합을 할 수 있도록 도와준다.
    - 대표적으로 `Hibernate`, `mybatis` Framework 등을 쉽게 통합한다.
- Spring AOP
    - Aspect Oriented Programming(AOP)를 지원한다.
- Spring Web
    - Web Application 개발에 필요한 context와 multipart request 등의 기능을 지원한다.
- Spring Web MVC
    - Spring Framework에서 독립적으로 User Interface에서 Model-View-Controller 구조를 지원한다.
    - Web에서 또한 필요한 Excel, PDF 등의 기능을 지원한다.

## Features Of Spring Framework
- 경량 Container로서 Java Object를 직접 관리
    - 각자의 객체 생성, 소멸을 다루는 Life Cycle를 관리하면서 Framework 자체에서 필요한 객체를 얻어올 수 있음.
- POJO(Plain Old Java Object) 방식의 Framework 제공
    - Spring Framework는 POJO 방식의 Framework로 `J2EE` 프레임워크에 비해 구현에 필요한 특정 인터페이스나 상속 작업의 비중 감소.
> **Plain Old Java Object**
> - EJB(Enterprise Java Beans) 처럼 복잡하고 제한이 많은 기술을 사용하는 것이 아닌 단순 객체를 이용해 비즈니스 로직을 구현하는 목적을 가지고 만든 개념.
> - 진정한 POJO는 객체지향 원리에 충실하면서 환경과 기술에 종속되지 않고 필요에 따라 재활용될 수 있는 방식으로 설계하는 조건을 가져 Spring Framework를 이용하면 장황한 소스들을 최소화할 수 있다.
> - 또한 `JUnit`을 기반으로 자동화된 테스트를 제공하면서, 객체지향적인 설계를 자유롭게 적용하는데 도움을 준다.
- Inversion Of Control(제어의 반전)을 제공
    - 각종 지휘(Controller)를 관리를 사용자가 아니라 Framework가 관리하여 필요에 따라 스프링에서 사용자의 코드를 호출함.
- Dependency Injection(의존성 주입)을 제공
    - 각 계층이나 서비스 간의 의존성이 존재할 경우 Framework가 오작교와 같은 역할을 함.
- Aspect Oriented Programming 지원
    - Transaction, Logging, Security 등 여러 모듈에서 공통적으로 사용할 때 해당 기능을 Configuration 클래스를 이용해서 관리 가능.
- Persistence(영속성)과 관련된 다양한 서비스 지원
    - `mybatis`, `Hibernate` 등 완성도가 높은 데이터베이스 처리 라이브러리와 연결할 수 있는 인터페이스 제공.
- 확장하는 방법이 용이함
    - 기존 Library를 Spring Framework에 통합할 때 어느 정도 감싸는 정도로 사용이 가능하기 때문에 수많은 Library가 이미 Spring Framework에서 지원하는 추세이고, 이를 별도로 분리하는 점도 용이함.

## Three Leader Of POJO
![Spring_Triangle2](/Application_Computer_Science/2_Spring_Framework/img/Spring_Triangle2.png)

POJO를 기반으로 구성된 `Spring Framework`의 삼대장은 위와 같은 요소를 지닌다.

이 삼대장의 개념들이 어떤 의미를 가지는지 IoC(With DI), AOP, PSA 순으로 살펴보도록 하자. 

### IoC(Inversion Of Control)
Java 언어가 등장하기 시작하면서 Java 기반으로 Application을 개발할 때 Object에 대한 제어권을 개발자가 직접 가졌지만, Servlet, EJB가 등장을 하면서 Object를 관리하는 주체가 개발자가 아닌 컨테이너들이 전담하게 된다. Java의 모든 Object의 생명주기 관리 등의 관리 주체가 바뀐 개념을 IoC라고 칭한다.

개발자와 컨테이너의 역할이 역지사지 된 점을 살펴보면, 개발자는 Object를 이용해서 한 번에 완성을 해 왔지만, Spring Framework 내부에 존재하는 컨테이너가 한 번에 완성하는 것이 아니라 필요한 일부 기능들을 모아서 구축하도록 유도하는 역할을 한다.

IoC 컨테이너는 다음과 같이 분류된다.

![IoC_Graph](/Application_Computer_Science/2_Spring_Framework/img/IoC_Graph.jpg)

- DL(Dependency Lookup)
    - JNDI(Java Naming and Directory Interface) 저장소에 의해 관리되는 Bean을 개발자들이 컨테이너에서 제공하는 API를 이용해 Look-Up을 하는 개념.
    - DNS(Domain Name System) 서버에서 적용하는 사례로 볼 수 있다.
    - Object 간의 Decoupling(즉, Object와의 연관성을 축소한다.)을 해주는 장점을 가진다.
    - JNDI 관련 코드를 일일이 변경할 때 테스팅에서 까다롭고 코드 양이 늘어나고 Casting 작업 등 신경써야 할 부분이 많다.
- DI(Dependency Injection)
    - 각 클래스 사이 의존 관계를 Bean 설정 정보를 바탕으로 컨테이너가 자동적으로 연결하는 것을 말한다. 
    - Look-Up과 관련된 코드를 작성하지 않아도 되고 의존적이지 않은 코드를 작성할 수 있는데 Bean 설정 파일에서 의존 관계의 필요 정보를 추가하면 된다.

### DI(Dependency Injection)

이는 IoC에서 특별히 신경 쓴 개념으로 볼 수 있다. 위에서 언급했듯이 각 클래스 사이 의존 관계를 Bean을 이용해서 주입하는 개념이다.

DI의 종류로는 3가지로 나뉜다.

- Setter Injection
    - 클래스 사이의 의존 관계를 연결시키기 위해 클래스 내부에 있는 setter 메소드를 이용하는 방법.
    - 이는 Spring Container에서 지원하는 DI 방식이다.

```
class CarManager{
    private Car car;
    public void setCar(Car car){
        this.car = car;
    }
}
class Car{
    private String name;
    public void setName(String name){
        this.name=name;
    }
}
```

- Constructor Injection
    - 필요한 의존성을 모두 포함하는 생성자를 만들고 이를 이용해서 의존성을 주입한다.
    - PicoContainer에서 지원하는 DI 방식이다.

```
class CarManager{
    private Car car;
    public CarManager(Car car){
        this.car = car;
    }
}
class Car{
    private String name;
    public Car(String name){
        this.name=name;
    }
}
```

- Method Injection
    - 의존성을 주입하는 함수를 포함한 Interface를 작성해서 이를 상속하여 의존성을 주입하는 방법이다.
    - Avalon에서 지원하는 DI 방식이다.

```
public interface CarManager{
    void injectManager(Car car);
}

class CarManagerImpl implements CarManager{
    private Car car;
    @Override
    public void injectManager(Car car){
        this.car=car;
    }
}

public interface Car{
    public void injectName(String name);
}
class CarImpl implements Car{
    private String name;
    @Override
    public void injectName(String name){
        this.name=name;
    }
}
```

### Aspect Oriented Programming
![korea_map](/Application_Computer_Science/2_Spring_Framework/img/korea_map.jpg)

> 지도의 기호들처럼 업무 프로세스에서는 부가적인 기능들이 퍼져 이용되고 있다.

AOP(Aspect Oriented Programming)의 핵심은 Aspect이다. Aspect는 직역하면 관점이라는 뜻이 있는데 Spring에서는 **부가적인 업무**에 대한 대응을 제공해야 하는 의미로 생각하는 것이 옳다. 더욱 쉽게 설명하자면 생활비를 계산하는데 날마다 통장 정리를 하러 은행가는 일이 귀찮기 때문에 최근에 모바일 어플리케이션을 이용한 현금 관리를 하는 사례와 같은 맥락으로 받을 수 있다.

왜 AOP를 사용하는 것일까? 생활비 관리는 인생에 있어서 귀찮은 작업으로 볼 수 있는데 꼭 해야 하는 작업이다. Spring에서 부가적인 프로세스 중에 대표적으로 Log, Transaction, Security, Caching 등의 작업이 있는데 이를 **내부 처리 작업** 이라고 한다.

업무 프로세서에서 **주 업무**와 **부가 업무**로 나뉠 때, 부가 업무에 대한 강한 응집력을 가지면, 주 업무에 대한 서비스 추상화가 어려워져서 소스 관리와 개발 업무 진행이 복잡해진다.

![AOP_Example01](/Application_Computer_Science/2_Spring_Framework/img/AOP_Example01.png)

이는 AOP를 적용하지 않고 각 과정에서 요구하는 부가 업무에 대해서 일일이 직접 설정해서 하는 과정이다. 이처럼 적용을 하게 되면 생각보다 오바이트가 나올 것이다.

![AOP_Example02](/Application_Computer_Science/2_Spring_Framework/img/AOP_Example02.png)

하지만 Annotation을 이용해서 각 과정 별 그 때마다 필요한 부가 업무를 과정에서 알아서 추출해서 사용한다면 주 업무와 부가 업무에서 쓰이는 객체에 대한 결합도를 낮춤으로서 더욱 가독성이 좋은 소스 코드로 축약이 가능하다. 

**간략하게 짚어 보는 AOP 용어**
- Aspect
    - 여러 클래스에 걸친 관심사의 모듈화이다.
    - Spring AOP에서는 @AspectJ 어노테이션이 붙은 정규 클래스 이용 방법과 Schema 기반의 접근이 가능한 정규 클래스를 이용한 방법이 있다.
- Join Point
    - Method의 실행, 예외 처리 등 프로그램이 실행되는 시점이다.
    - Spring AOP에서는 Method의 실행 지점이다.
- Point Cut
    - Join Point의 부분 집합이다.
    - Advice는 Point Cut에 응집된 Join Point에서 표현식이 부가되어 연결할 수 있고 매칭된 Join Point에서 실행되는 개념이다.
- Advice
    - 특정 Join Point에 삽입 되어 Aspect가 동작하는 코드다.
    - around, before, after 등의 여러 타입의 Advice가 존재한다.
- Weaving
    - 다른 Application 타입이나 Advice Object를 생성하는 객체와의 관점을 연결하는 과정이다.
    - 컴파일 시점, 로딩 시점, 런타임 시점에서 수행이 가능하다.
- AOP Proxy
    - Aspect Contract(관점 계약. Advice Method etc.)가 생성한 객체.
    - AOP Proxy는 JDK Dynamic Proxy이나 CGLIB Proxy로 된다.


### AOP Proxy
[계속 작성하겠습니다.]

### PSA(Portable Service Abstractions)

환경과 세부 기술의 변화에 관계 없이 일관된 방식으로 기술에 접근할 수 있게 하는 개념이다. 

여기서 POJO에서 강조한 내용 중에 이를 이용한 코드는 특정 환경이나 구현 방식에 종속적이지 않아야 하는 것을 전제하는 점이다. 

기존 Enterprise 개발이 주는 복잡함을 최소화하는데 있어서 필수 요소로 볼 수 있다.

PSA와 연관된 객체 지향 패턴은 Adapter Pattern이 있다.

**PSA를 접목 시킨 Library or Framework**
- JUnit
- MyBatis
- JDBC 계열
- Spring Data 계열
- Social(Facebook, Linkedin, Twitter)

## References
- http://ooz.co.kr/170 - Spring Basic 개념을 설명한 페이지.
- http://sosohantalk.tistory.com/1 - Spring Framework Basic Structure
- http://jinpyo900.tistory.com/55 - Spring Basic 개념을 간단하게 정리한 페이지 
- https://blog.outsider.ne.kr/843 - AOP 개념에 대해 자세히 쓰인 페이지