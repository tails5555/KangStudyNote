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

Database에서도 사용자 별 권한을 주어 관리할 수 있는 개념이 있다. Spring Security도 마찬가지로 사용자 별 권한이 여러 가지가 존재하는데 이를 기반으로 각 사용자들이 가지고 있는 권한의 대표적인 주체를 Role이라고 한다.

우리가 학창 시절에 방과 후 청소 역할을 분담하는 경우가 있다. 청소하는 구역을 한 사람이 분담하는 것이 아니라 여러 곳을 청소할 수 있다. 이처럼 한 사람이 하나의 Role만 받는 것이 아니라 여러 Role을 받음으로서 서비스를 이용하는데 차질이 안 생기도록 하기 위해서는 GrantedAuthority를 이용하여 Role에 대한 관리를 제대로 해야 한다.

### GrantedAuthority

![grantedauthority](/Application_Computer_Science/3_Securities_Framework/img/grantedauthority.jpg)

우리가 Basic Authentication을 통해서 본인 인증이 완료된다면 HTTP에서는 Authentication(인증) 과정이 마치게 되는데 Spring Security 내부에서는 Authorization(권한 부여) 작업을 처리해야 한다. 이 작업은 대부분 AuthenticationProvider에서 처리하는데 개인 정보(아이디, 비밀번호, 이름, 주소 etc)들을 UserDetails에 정리하되 그 이전에 사용자 권한(Role)을 GrantedAuthority를 기반으로 생성하고 Set 객체에 정리하여 주입하는 방법이 대표적이다.

Spring Security에서 Role은 `ROLE_USER`, `ROLE_ADMIN`, `ROLE_STUDENT` 등으로 표현되어야 한다. 이는 GrantedAuthority에서 주어진 권한을 검사하는 RoleVoter 클래스를 가지고 있는데 이 검사자가 `ROLE_` 이란 접두어를 이용해서 검사를 하기 때문이다. 접두어가 `ROLE_`로 시작하지 않으면 컴파일 오류는 걸리지 않는데 예외 처리로 인한 접근 보류(ACCESS_ABSTAIN)로 종결 짓게 된다.

### Principal & Credential

![loginscreen](/Application_Computer_Science/3_Securities_Framework/img/loginscreen.jpg)

> 말 그대로 각각 ID, Password 개념이다.

로그인 정보에서 ID, Password의 개념을 모르는 사람은 없을 것이다. Spring Security에서 ID를 Principal로 부르고, Password를 Credential로 부른다.

Spring Security를 통해 인증이 완료된 현재 사용자를 불러올 때 쓰는 함수 중에 Principal, Credential 개념이 있다.

참고로 ID(username)와 비밀번호(password)로 본인 인증하는 개념을 Basic Authentication(기본 인증)이라고 한다.

### UserDetails

> 사용자의 ID, 비밀번호, GrantedAuthority Collection, 세부 정보 등을 모아 둔 Value Object / Domain Object

사용자의 정보를 저장하기 위해서는 Value Object나 Domain Object 등을 이용해서 각 사용자의 세부 정보까지 받을 필요가 있다. 이를 적용하기 위해 Spring Security의 구현체인 UserDetails 인터페이스를 상속시켜 구현해야 한다.

기본적으로 username(ID), password(비밀번호), GrantedAutority 모음(주어진 권한 모음) 등을 기본적으로 받고, 추가로 세부정보에 대해서 본인 선택에 따라서 추가할 수 있다.

### Intercept

### CSRF

![csrf_explanation](/Application_Computer_Science/3_Securities_Framework/img/csrf_explanation.png)

> 해커의 특정 코드 꼽사리로 인하여 서버에서 논리적인 오류가 발생하는 현상.

CSRF(Cross-Site Request Forgery)는 사이트 간 요청을 위조하는 개념인데 웹 어플리케이션 취약점 중 하나로 사용자가 자신의 의지와 무관하게 공격자가 의도한 행동을 하여 특정 웹 페이지를 보안에 취약하게 하거나 수정, 삭제 등의 작업을 하게 만드는 공격 방법을 뜻한다. 

Web Explore 측에서 설명한다면 해커가 Web Site에 특정 코드를 넣는 과정을 진행하고, 해커가 아닌 사용자가 Web Server로 요청할 때 해커가 그 요청을 따와서 특정 코드를 추가하거나 마음대로 변조하여 Web Server에서 처리하는 과정이다. 특정 코드는 스크립트 코드나 Database Injection Query 문이 해당된다.

Spring Security에서는 CSRF를 방지하기 위해 Configuration 클래스에서 필수로 설정해야 한다.

## 3 Ways of Authentication

## Basic Configuration Class

```
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override 
    public void configure(WebSecurity web) throws Exception { 
        web.ignoring().antMatchers("/res/**", "/img/**", "/js/**", "/css/**");
        web.ignoring().antMatchers(HttpMethod.OPTIONS, "/**");
    }
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            .antMatchers("/admin/**").hasRole("ADMIN")
            .antMatchers("/user/**").hasRole("USER")
            .antMatchers("/guest/**").permitAll()
            .antMatchers("/").permitAll();
        
        http
            .csrf().disable();
    }
}
```

`SecurityConfig.java` 파일에서는 Spring Boot에서 Security를 적용하도록 설정하는 클래스이다. 여기서 설정해야 하는 기능들이 생각보다 많은데 이에 대해서 작성할 때 유의해야 할 사항은 다음과 같다.

**WebSecurity**
- ignoring()
    - antMatchers("URL")
    - antMatchers(HttpMethods, "URL")


**HttpSecurity**
- authorizeRequests()
    - antMatchers("URL")
    - hasRole("ROLE_주체")
    - permitAll()
- csrf().disable()

## Useful Examples
- https://github.com/tails5555/RestSpringSecurityExample
    - Spring Security 5 REST API Example (진행 중)
    - [README.md](https://github.com/tails5555/RestSpringSecurityExample/blob/master/README.md) 파일에 자세한 메카니즘을 수록하여 보기 좋게 정리하겠음.

## References
- [Spring Security 5.0.3 Docs](https://docs.spring.io/spring-security/site/docs/5.0.3.RELEASE/reference/htmlsingle/)
    - 영어로 되어 있지만, Spring Security에 대한 본론을 자세하게 알 필요가 있는 사람들은 심심할 때 읽어보길 바란다.
- http://atin.tistory.com/585 - Spring Security 블로그 글 모음
- http://springmvc.egloos.com/category/Spring%20Security - Spring Security 개념에 대한 정의를 참고하기 좋은 블로그
- https://namu.wiki/w/CSRF - CSRF 개념에 대해 읽어보기 좋은 사이트

## Thanks To
- [서종현](https://github.com/shouwn) - CSRF 개념에 대해 인용할 수 있도록 도움을 줌.