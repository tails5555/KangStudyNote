# Spring Security Main Issues
## What Is Spring Security?
원래 Spring Security는 Acegi Security로 2003년에 발표했는데 시간이 흐르면서 Spring Project에 병합되면서 Spring Security로 바뀌게 되었다.

Spring Security은 적정 수준의 튜닝과 짧은 소스 코드만으로 대형 웹 서비스와 비슷한 수준의 보안을 보장한다.

그렇지만 Spring Security에 대한 활용은 생각보다 미비하다. 공식 튜토리얼을 읽어볼 필요도 있고, 최신 버전에 대응하는 면에서는 고려해야 할 과제도 존재한다.

여기서 Spring Security에 대해 다루는 것에서 끝나지 않고 보안에 대한 기초 상식을 함유하는 기회를 가질 필요가 있다.

## Basic Fundaments
Spring Security를 접목할 때 다양한 용어들이 등장하는데 어떤 개념인지 살펴보고 인지할 필요가 있다. 여기서 보안과 관련된 개념들을 잠깐 공부할 수 있는 기회가 있으니 꼭 읽어두도록 하자.

### Authentication
![jumin_card](/Application_Computer_Science/3_Securities_Framework/img/jumin_card.jpg)

> 본인임을 증명할 수 있는 무언가의 정보 혹은 도구

Authentication(인증)은 본인 증명이 필요할 때 신원 확인을 위해서 자신이 가지고 있는 정보를 이용해 확인하는 방법을 뜻한다. 

대표적으로 Web에서는 Log-in(Sign-in), 민증 까기, 지문 인식, 얼굴 인식 방법 등이 있다.

### Authority, Authorization 

![cigar_table](/Application_Computer_Science/3_Securities_Framework/img/cigar_table.jpg)

> - 인증 과정을 거치고 난 뒤에 받는 주체 - Authority
> - 인증 과정을 거치고 난 뒤에 권한을 부여하는 개념 - Authorization

본인 확인이 완료된 이후 요청하는 작업을 할 수 있는 주체를 **Authority(권한, 인가)** 라고 하고, 이러한 주체를 타인들에게 부여하는 과정을 **Authorization(권한 부여)** 라고 한다.

편의점에서 술, 담배를 구매할 때 민증 까고 난 뒤에 사는 과정을 늘 거친다.

가령 편의점에서 알바생이 나이 확인을 위해 민증 까달라고 할 때 확인이 완료되면 담배 살 수 있는 권한(Authority)이 주어진다. 그럼 그 시간 대에 알바생이 방금 담배를 사 간 사람에게 담배를 살 수 있는 권한을 부여(Authorization)한 것이다. 

Authority, Authorization의 차이는 권한이 필요한 사람과 권한을 주는 사람 순서의 차이이다.

### Role
![school_clean](/Application_Computer_Science/3_Securities_Framework/img/school_clean.jpg)

> 사용자 별 권한들에 따라 제공되는 서비스를 나누는 개념

Database에서도 사용자 별 권한을 주어 관리할 수 있는 개념이 있다. Spring Security도 마찬가지로 사용자 별 권한이 여러 가지가 존재하는데 이를 기반으로 각 사용자들이 가지고 있는 권한의 모음을 Role이라고 한다.

우리가 학창 시절에 방과 후 청소 역할을 분담하는 경우가 있다. 청소하는 구역을 한 사람이 분담하는 것이 아니라 여러 곳을 청소할 수 있다. 이처럼 한 사람이 하나의 권한만 받는 것이 아니라 여러 권한을 받음으로서 서비스를 이용하는데 차질이 안 생기도록 하는 개념이다.

### Principal & Credential

![loginscreen](/Application_Computer_Science/3_Securities_Framework/img/loginscreen.jpg)

> 말 그대로 각각 ID, Password 개념이다.

로그인 정보에서 ID, Password의 개념을 모르는 사람은 없을 것이다. Spring Security에서 ID를 Principal로 부르고, Password를 Credential로 부른다.

Spring Security를 통해 인증이 완료된 현재 사용자를 불러올 때 쓰는 함수 중에 Principal, Credential 개념이 있다.

### Granted Authority

### Intercept

### CSRF

## 3 Ways of Authentication

## The Kinds Of Spring Security Framework

## Basic Annotations

## Useful Examples

## References
- [Spring Security 5.0.3 Docs](https://docs.spring.io/spring-security/site/docs/5.0.3.RELEASE/reference/htmlsingle/)
    - 영어로 되어 있지만, Spring Security에 대한 본론을 자세하게 알 필요가 있는 사람들은 심심할 때 읽어보길 바란다.