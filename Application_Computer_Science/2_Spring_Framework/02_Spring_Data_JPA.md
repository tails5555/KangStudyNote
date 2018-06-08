# Spring Data JPA Issues
이번에는 `Spring Data JPA`에서 짚고 넘어가야 하는 Issue에 대해 공부하기 위한 노트를 만들도록 하겠다.

Spring Data JPA만 쓰는 개발자들은 SQL Query에 대해 확답할 수 없다는 부족함이 생길 수 있기 때문에 JPA만 쓰지 말고 MyBatis를 이용해서 Query 문장을 꼭 연습하도록 하자.

## What Is JPA?

> Java Persistence API

JPA는 기존에 EJB(Enterprise Java Beans)에 제공된 Entity Bean을 대체하는 기술이자, EJB 3.0 스펙의 일부 정의가 되어 있었다. JPA 기술은 Web Module 뿐만 아니라 클라이언트 측에서도 사용이 가능하고 원하는 Persistence Provider 구현체를 선택해서 이용할 수 있다.

JPA의 영역은 3가지로 구성이 된다.
- `javax.persistence` 패키지로 정의된 API 자체
- `JPQL`
    - `Java Persistence Query Language`로 관계형 데이터베이스에 저장된 엔티티에 대한 쿼리들을 작성을 한다. SQL 쿼리와 유사한 면이 있지만, 데이터베이스 테이블에 직접적으로 처리하지 않고 **Entity Object**에 대해 처리한다.
- 객체와 관계의 Meta Data

## The Nawabaries Of JPA
- `javax.persistence.*`
- `JPQL`(Java Persistence Query Language)
- 객체(Object)와 관계(Mapping)의 `Meta Data`
## References
- https://docs.spring.io/spring-data/jpa/docs/2.0.7.RELEASE/reference/html/ - Spring Data JPA 공식 Tutorial
- http://blog.woniper.net/255 - JPA에 대한 정의를 설명한 블로그
- https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_%ED%8D%BC%EC%8B%9C%EC%8A%A4%ED%84%B4%EC%8A%A4_API - JPA Wikipedia
- https://www.tutorialspoint.com/jpa/index.htm - JPA 간략한 Tutorial