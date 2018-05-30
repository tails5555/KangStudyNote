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

`Spring Security`에서 쓰이는 Authentication 개념의 사례는 다음과 같다.

- 실제 사용자 정보가 담긴 데이터베이스와 접근할 수 있는 `AuthenticationProvider`에서 활용된다. 이를 적용하는 방법은 후술하겠다.
- Controller에서 인증을 거친 사용자의 인증 주체를 가져올 때 사용할 수 있다.
    - 물론 사용자 이름을 불러올 때에는 Principal을 이용하지만, Controller 내부에서 로그아웃을 할 때 인증 주체를 불러와서 로그아웃을 진행하는 것도 아래처럼 치러질 수 있다.

```
@DeleteMapping("logout")
public ResponseEntity<String> logout(HttpServletRequest request, HttpServletResponse response, Principal principal){
    String logoutMessage = String.format("User Logout is Successed -> %s", principal.getName());
    Authentication auth = SecurityContextHolder.getContext().getAuthentication();
    if (auth != null){
        new SecurityContextLogoutHandler().logout(request, response, auth);
    }
    return new ResponseEntity<String>(logoutMessage, HttpStatus.OK);
}
```

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

> 사용자 역할 별 권한들에 따라 제공되는 서비스를 나누는 개념

Database에서도 사용자 별로 역할을 주어 데이터를 관리하는 역할을 분담하는 경우가 있다. Spring Security도 마찬가지로 사용자 별 역할이 여러 가지가 존재하는데 이를 기반으로 각 사용자들이 가지고 있는 역할의 대표적인 주체를 Role이라고 한다.

우리가 학창 시절에 방과 후 청소 역할을 분담하는 경우가 있다. 청소하는 구역을 한 사람이 계속 담당하는 것이 아니라 여러 곳을 청소할 수 있다. 이처럼 한 사람이 하나의 Role만 받는 것이 아니라 여러 Role을 받음으로서 서비스를 이용하는데 차질이 안 생기도록 하기 위해서는 `GrantedAuthority`를 이용하여 Role에 대한 관리를 철저히 진행해야 한다.

### GrantedAuthority

![grantedauthority](/Application_Computer_Science/3_Securities_Framework/img/grantedauthority.jpg)

우리가 Basic Authentication(사용자 아이디와 비밀번호를 이용한 인증)을 통해서 본인 인증이 완료된다면 HTTP에서는 Authentication(인증) 과정이 마치게 되는데 이 인증 결과를 가져와서 사용자 관리 서버 내부에서는 Authorization(권한 / 역할 부여) 작업을 처리해야 한다. 이 작업은 대부분 `AuthenticationProvider`에서 처리하는데 개인 정보(아이디, 비밀번호, 이름, 주소 etc)들을 `UserDetails`에 정리하고, 사용자 권한(Role)을 `GrantedAuthority`를 이용해 생성하고 Collection 상속을 받은 객체에 정리하여 주입하는 방법이 대표적이다.

Spring Security에서 Role은 `ROLE_USER`, `ROLE_ADMIN`, `ROLE_STUDENT` 등으로 표현되어야 한다. 이는 GrantedAuthority에서 주어진 권한을 검사하는 RoleVoter 클래스를 가지고 있는데 이 검사자가 `ROLE_` 이란 접두어를 이용해서 검사를 하기 때문이다. 접두어가 `ROLE_`로 시작하지 않으면 컴파일 오류는 걸리지 않는데 예외 처리로 인한 접근 보류(ACCESS_ABSTAIN)로 종결 짓게 된다.

### Principal & Credential

![loginscreen](/Application_Computer_Science/3_Securities_Framework/img/loginscreen.jpg)

> 말 그대로 각각 ID, Password 개념이다.

로그인 정보에서 ID, Password의 개념을 모르는 사람은 없을 것이다. Spring Security에서 ID를 Principal로 부르고, Password를 Credential로 부른다.

Spring Security를 통해 인증이 완료된 현재 사용자를 불러올 때 쓰는 객체 중에 Principal, Credential 기능이 있다.

참고로 ID(username)와 비밀번호(password)로 본인 인증하는 개념을 Basic Authentication(기본 인증)이라고 한다.

Web Proxy Tool을 사용하면 언제든 보안 문제가 발생할 수 있기 때문에 이를 방지하고 싶다면 username(ID)와 password를 암호화해서 데이터베이스에 저장하는 방법도 있는데 이 때 Java RSA를 이용한 암호화를 진행해주면 된다.

### UserDetails

> 사용자의 ID, 비밀번호, GrantedAuthority Collection, 세부 정보 등을 모아 둔 Value Object / Domain Object

사용자의 정보를 저장하기 위해서는 Value Object나 Domain Object 등을 이용해서 각 사용자의 세부 정보까지 받을 필요가 있다. 이를 적용하기 위해 Spring Security의 구현체인 UserDetails 인터페이스를 상속시켜 구현해야 한다.

기본적으로 username(ID), password(비밀번호), GrantedAutority 모음(주어진 권한 / 역할 모음) 등을 기본적으로 받고, 추가로 세부정보에 대해서 본인 선택에 따라서 추가할 수 있다.

### Intercept

![crossing](/Application_Computer_Science/3_Securities_Framework/img/crossing.jpg)

> 사용자가 지닌 권한 / 역할 별로 Resource를 관리할 수 있게 도와주는 기찻길 건널목과 같은 개념

Intercept는 Spring Security에서 매우 중요한 역할을 한다.

요청 URL 별로 다른 기능을 제공할 때 access 별로 권한 / 역할 확인을 진행하거나 Session 별로 사용자 정보에 대한 저장 여부나 현재 사용자 정보를 가져오거나 등의 기능을 담당하는 역할이 바로 Intercept이다.

기찻길 별로 건널목을 만드는 과정을 생각한다면 각각 요청 받은 URL 별로 설정하는 과정을 바로 Customizing(커스터마이징) 과정이라고 한다. 

프로젝트에서 Security를 다룰 때 Intercept 관련 기능인 Customizing의 구성을 초반에 확실히 잡아두고 난 후에 진행을 해야 다음 프로젝트 진행에서 차질이 안 생긴다.

### CSRF

![csrf_explanation](/Application_Computer_Science/3_Securities_Framework/img/csrf_explanation.png)

> 해커의 특정 코드 꼽사리로 인하여 서버에서 논리적인 오류가 발생하는 현상.

CSRF(Cross-Site Request Forgery)는 사이트 간 요청을 위조하는 개념인데 웹 어플리케이션 취약점 중 하나로 사용자가 자신의 의지와 무관하게 공격자가 의도한 행동을 하여 특정 웹 페이지를 보안에 취약하게 하거나 수정, 삭제 등의 작업을 하게 만드는 공격 방법을 뜻한다. 

Web Explore 측에서 설명한다면 해커가 Web Site에 특정 코드를 넣는 과정을 진행하고, 해커가 아닌 사용자가 Web Server로 요청할 때 해커가 그 요청을 따와서 특정 코드를 추가하거나 마음대로 변조하여 Web Server에서 처리하는 과정이다. 특정 코드는 스크립트 코드나 SQL Injection Query 문 등이 해당된다.

Spring Security에서는 CSRF를 방지하기 위해 Configuration 클래스에서 필수로 설정해야 한다. 이를 방지하는 방법은 아이러니하게도 간단하게 구성되어 있다.

### AuthenticationProvider

```
@Component
public class CustomAuthenticationProvider implements AuthenticationProvider{
    @Autowired UserService userService;

    // 1단계 적용
    @Override
    public Authentication authenticate(Authentication authentication) throws AuthenticationException {
        String loginId = authentication.getName();
        String passwd = authentication.getCredentials().toString();
        return authenticate(loginId, passwd);
    }

    public Authentication authenticate(String loginId, String passwd) throws AuthenticationException{
        // 2단계 적용
        User user = userService.login(loginId, passwd);
        if(user == null) return null;

        // 3단계 적용
        List<GrantedAuthority> grantedAuthorities = new ArrayList<>();

        String role = "";
        for(Role r : user.getRoles()) {
            switch (r.getName()) {
                case "ADMIN":
                    role = RoleType.ADMIN.getRoleType();
                    break;
                case "USER":
                    role = RoleType.USER.getRoleType();
                    break;
            }
            grantedAuthorities.add(new SimpleGrantedAuthority(role));
        }

        // 4단계 적용
        return new TokenAuthentication(loginId, passwd, grantedAuthorities, user);
    }

    @Override
    public boolean supports(Class<?> authentication){
        return authentication.equals(UsernamePasswordAuthenticationToken.class);
    }

    public class TokenAuthentication extends UsernamePasswordAuthenticationToken{
        private static final long serialVersionUID = 1L;
        UserVO user;

        public TokenAuthentication(String loginId, String passwd, List<GrantedAuthority> grantedAuthorities, UserVO user){
            super(loginId, passwd, grantedAuthorities);
            this.user = user;
        }

        public UserVO getUser(){
            return user;
        }

        public void setUserVO(UserVO user){
            this.user = user;
        }
    }
}
```

> 사용자 정보가 포함된 데이터베이스를 기반으로 로그인 인증을 적용하게 도와주는 역할.

Spring Security에서 Custom Authentication 절차는 다음과 같이 치러져야 한다. 사용자 ID와 비밀번호가 무조건 일치하다고 사용자의 정보를 가져온다면 인증 과정을 제대로 거치지 않았기 때문에 제공하는 기능을 이용하지 못 한다.

1. 로그인 화면에서 입력한 정보는 authenticate() 메소드의 파라미터로 전달이 된다.

2. Database에서 사용자 정보를 가져오기 위해서는 `UserDetailsService` 등의 컴포넌트를 이용하여 조회할 필요가 있다.
    - 컴포넌트 안에서는 초반에 사용자 이름(ID)를 이용하여 조회를 하되, 존재하지 않으면 인증 예외로 사용자가 존재하지 않음을 강조해야 한다.
    - 그 다음에 사용자가 존재한다 할지라도 비밀번호를 비교해야 하는데 Database에서 비밀번호는 암호화되어 저장이 되어 있음으로 현재 화면에서 입력한 비밀번호를 같은 암호화를 적용하여 일치 여부를 비교한다. 
    - 여기서 비밀번호가 일치하지 않으면 인증 예외로 비밀번호가 일치하지 않음을 안내하고, 비밀번화 일치한다면 그 사용자의 정보를 가져와서 다음 과정을 거치면 된다.

3. 사용자 정보 내부에는 그 사용자의 Role이 있는데 이를 기반으로 GrantedAuthority로 정리하여 설정해야 한다. Role인 경우는 대문자로 지정을 하고, ROLE_(role주체) 형식으로 정리해야 한다. 허나 Role이 아닌 권한(Authority or Privilege)인 경우에는 이에 대해 따로 설정하는 방법을 구상해야 한다.

4. 기본 인증을 통하여 사용자에게 권한 혹은 역할 부여 과정이 끝났으면 `UsernamePasswordAuthenticationToken`을 기반으로 한 Authentication 객체를 반환해야 실제 HTTP 서버에서 Header 부분에 포함된 Authorization Token(Basic ~~~, ~~~부분이 Token을 뜻한다.)과 비교하여 서비스를 제공할 때 참조하여 차별화를 둘 수 있다.

## Kinds of Spring Security Modules

Module은 Maven에서 제공하는 .jar 파일을 기반으로 정리하였으며, 전부 다루진 않고 알아두면 도움이 되는 Module에 대해 작성하였음을 밝힌다.

- `spring-boot-security-starter`
    - `Spring Boot`에서 Security를 적용하는 Maven Module.
    - `spring-security-core`, `spring-security-web`, `spring-boot-starter` Module이 포함되어 있다.

- `spring-security-core`
    - Authentication, Authorization에서 필요한 핵심 요소를 함유하는 Module.
    - `JDBC`, `MyBatis`, `JPA` 등을 이용하여 데이터베이스 내부에 있는 사용자 정보와 접근할 수 있는 `Authentication Provider`의 뼈대를 쉽게 구축하게 도와주는 Module. 

- `spring-security-web`
    - Web 보안 인프라 구조에서 효율적인 보안을 설정할 수 있게 도와주는 모듈.
    - Request URL 별로 Web 보안을 강화시킬 수 있는 `Filter`의 뼈대를 쉽게 구축하게 도와주는 Module. 

- `spring-security-config`
    - Spring Security 설정을 구현할 수 있게 도와주는 Module.
    - Spring Security Configuration을 할 수 있는 방법은 Java Configuration Class 작성, Configuration XML 파일 작성, `AOP`를 적용한 Annotation을 추가해서 설정하는 방법 등이 있다.

- `spring-security-taglibs`
    - `JSP`에서 Tag(&lt;sec:[context]&gt; &lt;/sec:[context]&gt;)를 이용해서 Authority 별로 View 내용을 보여줄 때 사용한다.
    - View에 대한 보안성이 강화되기 때문에 Server-Side의 보안 기능에 대해서 따로 적용해야 하는 단점도 있다.

- `spring-security-oauth2-core`
    - `Facebook`, `Google`, `GitHub` 등 저장된 본인의 Basic Authentication 정보를 이용해서 각 사이트에서 제공하는 API에서 인가 과정을 거치고 토큰을 받아 현재 서버에서도 이 계정을 통해 접근할 수 있게 제공하는 Security Framework가 바로 `OAuth 2.0`이다.
    - 위에서 언급한 개념이 통합 로그인 서비스인데 이를 Spring Security에서 적용할 수 있게 도와주는 Module로 생각하면 된다.

- `spring-security-oauth2-jose`
    - `JOSE`(JavaScript Object Signing & Encryption) Framework에서 제공하는 기능을 Spring Security에서 적용할 수 있게 도와주는 Module이다.
    - `JOSE` Framework 중에 다음 장에서 살펴볼 `JWT`(JSON Web Token) 기능도 포함된다.

- `spring-security-test`
    - `JUnit`, `Mockito Mock MVC`를 이용해서 Spring Security를 테스팅할 수 있게 도와주는 Module이다.

## Basic Configuration Class

```
@Configuration // Java Configuration 파일임을 알린다.
@EnableWebSecurity // Spring Web Application에서 Security를 적용한다는 것을 알린다.
public class SecurityConfig extends WebSecurityConfigurerAdapter {  
    @Autowired AuthProvider authProvider;
    @Autowired AuthenticationEntryPoint authEntryPoint;
    @Autowired AuthLoginSuccessHandler authLoginSuccessHandler;
    @Autowired MyAccessDeniedHandler myAccessDeniedHandler;
   
    // Part 1.
    @Override 
    public void configure(WebSecurity web) throws Exception { 
        web.ignoring().antMatchers("/res/**", "/img/**", "/js/**", "/css/**");
        web.ignoring().antMatchers(HttpMethod.OPTIONS, "/**");
    }

    // Part 2.
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .antMatchers("/admin/**").hasRole("ADMIN")
                .antMatchers("/user/**").hasRole("USER")
                .antMatchers("/common/**").hasAnyRole("USER", "ADMIN")
                .antMatchers("/guest/**").anonymous()
                .antMatchers("/").permitAll();

        http
            .csrf().disable();

        http
            .httpBasic()
            .authenticationEntryPoint(authEntryPoint)
            .and()
            .exceptionHandling().accessDeniedHandler(myAccessDeniedHandler);


        http
            .authenticationProvider(authProvider);

        http
            .formLogin()
            .successHandler(authLoginSuccessHandler)
            .failureHandler(authLoginFailureHandler());
            .and()
            .logout();
    }

    // Part 3.
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication().withUser("kangAdmin").password("test123").roles("ADMIN");
        auth.inMemoryAuthentication().withUser("kangUser").password("test123").roles("USER");
    }

    // Part 4.
    @Bean
    public SimpleUrlAuthenticationFailureHandler authLoginFailureHandler() {
        return new SimpleUrlAuthenticationFailureHandler();
    }
}
```

`SecurityConfig.java` 파일에서는 Spring Boot에서 Security를 적용하도록 설정하는 클래스이다. 여기서 설정해야 하는 기능들이 생각보다 많은데 이에 대해서 작성할 때 유의해야 할 사항은 다음과 같다.

**WebSecurity**
- `ignoring()` : WEB Application에서 사용자 권한을 신경쓰지 않아도 되는 것들에 대해서 설정하는 것이다. 예를 들어 resource 파일, css 파일, js 파일 등이 있다.
    - `antMatchers("URI 상대주소")` : 이 뒤에 권한을 굳이 안 해도 되는 파일의 상대 주소를 입력한다. webapp을 기준으로 잡아줘야 한다. URI 상대 주소를 예를 들면 `/css/**`로 작성하면 webapp에 있는 css 폴더를 포함된 파일에 대해 적용한다는 뜻이다.
    - `antMatchers(HttpMethods, "URI 상대주소")` : 이는 HTTP Method를 통한 요청 URI를 불러올 때 이 HTTP Method를 적용하였을 때 권한을 신경 쓰지 않는다.
    <br/>

**HttpSecurity**

HTTP Security에서는 인가를 위한 URL의 커스터마이징을 할 수 있도록 도와준다.

그리고 사용자 로그인과 로그아웃 상황에 맞춘 Handler Bean을 생성하여 설정할 수 있고, 로그인 실패에 따른 접근 거부를 제어하는 Handler Bean을 생성하여 설정할 수 있다.

- `authorizeRequests()` : Spring Security에서 HTTP Request URL 요청에 대한 커스터마이징을 진행할 수 있는 메소드이다. 이 메소드에는 아래와 같은 속성을 첨가하여 설정하면 된다. 다만 주의사항으로 여기서 설정되는 기능은 권한 별 순위에 맞춰서 조정을 잘 해야 한다.

    - `antMatchers("URI 상대주소")` : 방금 WebSecurity에서 설명한 거와 다를 바가 없다. 상대 주소를 예를 들면 `guest/**`로 설정한다면 상대 요청 URI가 guest로 시작하는 경우에 해당되는 것으로 이해해야 한다.

    - `access("[ROLE_role이름] or [Privilege이름]")` : 권한이나 역할을 설정하여 접근이 가능하도록 할 때 적용한다.

    - `hasRole("[role이름]")` : 사용자가 가진 역할 중에서 하나의 역할이 포함되어 있을 때 접근을 허가한다.

    - `hasAnyRole("[role이름1]", "[role이름2]", "[role이름3]", ...)` : 사용자가 가진 역할 중에서 그 중에 해당 되는 역할이 포함되어 있을 때 접근을 허가한다.

    - `permitAll()` : 사용자의 권한을 떠나서 그 요청에 대해서는 모두 접속할 수 있게 한다. 여기서 비회원도 포함된다. 그러나 비회원을 철저하게 구분하기 위해서는 아래 anonymous() 메소드를 더욱 권장한다.

    - `denyAll()` : 사용자의 권한을 떠나서 모두 접속을 할 수 없게 차단을 할 때 이용한다. 물론 비회원도 접속이 불가능하게 된다.

    - `anonymous()` ( `not().authenticated()` ) : 접속을 아직 하지 않은 비회원에 해당되며 요청 URL에 대해서 모두 접속할 수 있게 한다.

    - `rememberMe()` : 사용자가 자동 로그인을 할 때 적용할 수 있는 개념으로 이를 가진 사용자에게 접근을 허가한다.

    - `authenticated()` : 비회원이 아닌가를 판별할 때 사용한다.

    - `fullyAuthenticated()` : 비회원이 아닌가와 자동 로그인을 요청하지 않은 회원인가를 판별할 때 사용한다.
    <br/>
- `csrf().disable()`
    - CSRF를 이용한 해킹을 예방할 때 작성하는 메소드이다.
    <br/>
- `httpBasic()` : HTTP 요청에 대하여 접근 거부 및 인증 예외 처리를 위한 Handler를 설정할 때 이용한다. 따로 설정하지 않으면 자동으로 구현이 되어 있다.
    - `authenticationEntryPoint`, `accessDeniedHandler` 등을 설정할 수 있다.
    - 이에 대한 설명은 Kinds Of Handler Component를 참고하자.
    <br/>
- `authenticationProvider(authProvider)` : Custom AuthenticationProvider를 설정할 때 적용한다. 이는 Component로 구현이 되어 있다면 Singleton 원리인 @Autowired로 불러오면 된다.
<br/>
- `formLogin()`, `logout()` : 말 그대로 Basic Authentication을 가져와서 로그인, 로그아웃을 설정할 때 Handler를 설정하는 메소드로 볼 수 있다. Handler를 따로 만들지 않아도 기본으로 쓰이는 Handler를 불러와서 설정을 시켜준다.
<br/>

**AuthenticationManagerBuilder**

- Spring Security에서 자동으로 Configuration이 되어 있는 설정을 빌미로 사용자 이름과 비밀번호를 입력하는 창이 뜰 것이다. 
- 만의 하나로 방지하기 위한 대책으로 서버 상에 필요한 사용자 ID와 비밀번호를 설정한다. 
- 이는 데이터베이스에 저장이 되지 않지만 단점으로 서버 정보 유출에 대한 파급 효과를 당할 수 있으니 이는 공개되는 코드에서는 배제할 필요가 있다.
<br/>

**Kinds Of Handler Component**

1. 로그인 / 로그아웃 관련

- `AuthenticationSuccessHandler`
    - REST API를 기반으로 사용자가 로그인을 하고 난 후에 200 OK Status에 맞춰 추가로 제어하고 싶은 기능을 구현하는 Interface이다.
    - 이 부분에서 사용자 정보와 Session ID를 받아오고 난 후에 Token을 임시 저장하는 `Authentication Cache` 를 추가로 구현할 수 있다.

- `AuthenticationFailureHandler`
    - REST API를 기반으로 로그인을 통한 인증 실패 시에 401 Unauthorized Status에 맞춰 추가로 제어하고 싶은 기능을 구현하는 Interface이다.
    - 여기서 로그인을 실패한 경우에 대비한 시나리오를 구현할 수 있다.
    
- `LogoutSuccessHandler`
    - REST API를 기반으로 사용자가 로그아웃을 진행하면 200 OK Status에 맞춰 추가로 제어하고 싶은 기능을 구현하는 Interface이다.
    - 로그아웃의 작업도 Transaction의 일종으로 취급해야 하기 때문에 현재 로그인 된 사용자 목록에서 배제할 수 있도록 구분하는 DELETE Method로 구현되어야 한다.
    <br/>

2. 인증 예외 처리 관련

- `AuthenticationEntryPoint`
    - REST API를 기반으로 401 Unauthorized Status에 맞춰 추가로 제어하고 싶은 기능을 추가하는 Interface.
    - Unauthorized 상태는 **로그인을 진행 조차 하지 않은 사용자**에 해당되는 경우이다. 즉 인증이 제대로 진행되지 않은 요청을 진행할 때 나오는 결과로 볼 수 있다.

- `AccessDeniedHandler` 
    - REST API를 기반으로 403 Forbidden Status에 맞춰 추가로 제어하고 싶은 기능을 추가하는 Handler Interface.
    - Forbidden 상태는 로그인을 진행한 사용자 중에 **권한 혹은 역할이 적절하지 않은 경우**이다. 예를 들어 관리자만이 접속할 수 있는 기능에 대해 일반 사용자가 접근하는 경우를 방지하기 위한 건널목을 내리는 경우다.
    <br/>

3. WEB MVC에서 Status와 REST API에서 Status

    |동작|기본 Spring Security Form (WEB MVC 환경)|REST Security Form|
    |:-------:|:-----------------------:|:--------------------:|
    |인증 성공시|설정된 인증 성공 URL로<br/>302 Redirect|200 OK<br/>Status|
    |인증 실패시|설정된 인증 실패 URL로<br/>302 Redirect|401 Unauthorized<br/>Status|
    |로그아웃|설정된 로그아웃 성공 URL로<br/>302 Redirect|200<br/>OK Status|

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
- http://hhjeong.tistory.com/105 - URL 커스터마이징에서 필요한 메소드 목록.
- http://netframework.tistory.com/entry/REST-API-%EA%B5%AC%EC%84%B1%EC%8B%9C-Spring-Security-%EA%B5%AC%ED%98%84 - REST API에서 Spring Security를 구성할 때 필요한 요소들 참조
- http://micropilot.tistory.com/2884 - AuthenticationProvider를 작성할 때의 사례 참조

## Thanks To
- [서종현](https://github.com/shouwn) - CSRF 개념에 대해 인용할 수 있도록 도움을 줌.

## Post Script
- 여기에 작성된 내용 이외에도 필요한 개념들을 발견하게 된다면 언제든지 갱신될 수 있습니다.
- `Spring Security`에서 더욱 다뤄주면 좋은 개념들이나 오탈자가 있으시면 KangBakSa Issues에 올려주세요.