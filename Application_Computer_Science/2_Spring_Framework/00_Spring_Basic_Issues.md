# Spring Basic Issues 정리
우리가 Spring Framework를 이용해서 프로젝트를 진행 해 왔지만, Spring Framework에서 변화해야 할 점도 찾아볼 필요를 찾아야 된다. 이러한 목적을 도달하기 앞서 Spring의 기본 개념을 공부하는 시간을 가져보도록 하자.

## What is Spring Framework?
- `Spring Framework`는 `Java` 플랫폼을 위한 Open Source Application Framework이다.
- 
- 종속 객체를 생성하고 이를 장난감 블럭처럼 조립하는 도구로 볼 수 있다.

- Java 객체(`POJO`, Plain Old Java Object)를 Java EE에 의존적이지 않게 연결을 해 주는 역할을 하는 Framework이다.

## Spring Framework Structure
![spring_framework_structure](/Application_Computer_Science/2_Spring_Framework/img/spring_framework_structure.png)

- Spring Core
    - Spring Framework의 뿌리 역할을 맡는 IoC(
Inversion of control, 흔히 쓰이는 DI(Dependency Injection) Pattern을 사례.) 기능을 지원하는 영역을 담당한다.
    - BeanFactory를 기반으로 Bean 클래스들을 제어할 수 있는 기능을 지원한다.
- Spring Context
    - Spring Core의 비서 역할을 하는데 지원하는 기능 이외에 추가적인 기능들과 쉬운 개발이 가능하도록 지원하는 역할을 한다.
    - Spring Project를 생성할 때 web.xml 파일에서 ContextLoaderListener, DispatcherServlet을 생성하게 되는데 이 두 개념을 root-context, servlet-context으로 칭한다. 이 두 개념이 Spring Core를 올바르게 작동하도록 좌우한다.
- Spring DAO
    - Data Access Object(DAO) 개념은 JDBC에서 공부를 할 때 Query 문을 작성하는 연습부터 많은 노가다를 가진 경험이 있을 것이다.
    - DAO 개발을 쉽고 일관된 방법으로 개발할 수 있도록 지원을 한다.
    - `mybatis`의 Mapper Interface, `JPA`의 Repository Interface 등이 있다. 
- Spring ORM
    - Object Relation Mapping(ORM) Framework와의 결합을 할 수 있도록 도와준다.
    - 대표적으로 `Hibernate`, `mybatis` Framework 등을 쉽게 통합한다.
- Spring AOP
    - Aspect Oriented Programing(AOP)를 지원한다.
- Spring Web
    - Web Application 개발에 필요한 context와 multipart request 등의 기능을 지원한다.
- Spring Web MVC
    - Spring Framework에서 독립적으로 User Interface에서 Model-View-Controller 구조를 지원한다.
    - Web에서 또한 필요한 Excel, PDF 등의 기능을 지원한다.

## Features Of Spring Framework

## References
- http://ooz.co.kr/170 - Spring Basic 개념을 설명한 페이지.
- http://sosohantalk.tistory.com/1 - Spring Framework Basic Structure
- http://jinpyo900.tistory.com/55 - Spring Basic 개념을 간단하게 설명한 페이지 