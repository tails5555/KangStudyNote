# Spring Web MVC Issues
`Spring`으로 프로젝트를 진행했다고 이야기 한다면 이는 부산 자갈치 시장에 가서 자갈치를 사 먹었다는 이야기가 되어 버린다. 이제는 본인이 무슨 프레임워크를 이용했는가에 대해 올바르게 설명해야 하고, 프레임워크 내부에서 사용한 라이브러리를 어떤 기능에 적용했는가를 올바르게 언급할 수 있어야 한다.

이번에는 `Spring Framework`의 첫 관문인 `Spring Web MVC`를 공부해보도록 하자.

## What Is MVC Pattern?

MVC는 소프트웨어 공학에서 사용되는 소프트웨어 디자인 패턴이다. 무려 우리나라에 컴퓨터가 보급되기 한창 전인 1979년에 최초로 소개된 개념이다.

MVC Pattern은 Web에서만 쓰는 Pattern이 아니다. Web에서 이 패턴을 현재까지 지향을 해서 그렇지, 실제로는 게임, DBMS, 오피스 등등 여러 소프트웨어에서도 적용을 할 수 있다.

MVC 패턴을 좀 더 일상 속에서 뉴스와 신문 등 언론을 사례로 설명한다면 아래와 같은 그림으로 설명할 수 있다.

![web_mvc_pattern](/Application_Computer_Science/2_Spring_Framework/img/web_mvc_pattern.png)

### Model

> 소프트웨어의 데이터를 책임지는 일꾼

`Spring Web MVC`에서는 어플리케이션의 데이터를 담당하는 역할을 한다.

View에서 데이터를 이용해 사용자에게 화면을 보여줄 때 요구 사항에 필요한 데이터를 가져와서 쇼핑 카트와 같은 원리로 최종적으로 데이터 가공이 완료된 상태에서 View에 전송을 해 준다.

### View

> 소프트웨어의 비쥬얼을 담당하는 개념

`Spring Web MVC`에서 쓸 User Interface(`HTML`, `JSP`, `JSTL` etc.)에 Model에 포함된 가공된 데이터를 전달하는 역할과 사용자가 Controller에게 요구 사항을 보내서 처리하도록 지시할 수 있게 만드는 역할을 한다. View는 여러 개가 존재할 수 있다. 최근에는 JSP 이외에도 JavaScript Singled Page Application을 더욱 쓰는 추세로 앞으로도 이의 기반에 맞춘 언어들(`TypeScript`, `JSX` etc.)의 소비가 증가하고 있다. 

### Controller

> 소프트웨어의 카운터를 담당하는 개념

View는 여러 개의 Controller를 통해 호출할 수 있다. 사용자가 View에서 보여진 데이터를 변경하기 위해서는 Controller의 요청 URI를 꼭 거쳐야 된다. Controller에게 전달 받은 요구 사항을 통해 Model의 상태가 바뀌게 된다면 등록된 View에 자기 자신이 바뀌었다는 사실을 알리고 View는 Model에서 최근에 변경된 데이터를 반영해서 보여준다.

## Structure of Spring Web MVC

![web_mvc_pattern](/Application_Computer_Science/2_Spring_Framework/img/web_mvc_structure.png)

Spring Web MVC의 구조는 위와 같이 나뉘게 된다. 위와 같은 Pattern을 Front-Controller Pattern이라고 한다. 적용하는 과정에 대해서 구체적으로 읽어볼 필요가 있다.

**Spring Web MVC의 작동 순서**
> 1. Client(Web Browser)에서 URL를 요청한다.
> - 요청 URL를 DispatcherServlet에서 전달 받아 읽어 들인다.
>
> 2. 요청 URL과 매핑되는 Controller를 검색한다.
> - `@RequestMapping`, `@GetMapping`, `@PostMapping` 등에 기재된 요청 URL를 이용해서 검색한다.
>
> 3. Controller에 처리 요청한다.
> - 요청 종류(GET, POST, PUT, DELETE etc.)에 따라서 차별화를 두고, HTTP Status(OK(200), NO CONTENT(204), NOT FOUND(404), CREATED(201) etc.)를 데이터 처리 상황에 맞춰서 동시에 반환한다.
>
> 4. ModelAndView 아니면 View의 실존 주소를 반환한다.
> - 모든 View를 참고할 때에는 `application.properties` 파일에 설정해두고 난 후에 views 파일 아래에 있는 상대 주소를 기준으로 View를 불러야 한다.
>
>   ```
>   spring.mvc.view.prefix=/WEB-INF/views/ 
>   spring.mvc.view.suffix=.jsp
>   ... 생략 ...
>   ```
> 
>   `application.properties` 에서 View의 상대 주소를 쓸 Prefix 주소와 Suffix(확장자)를 설정한 일부 문장이다.
> 
>   ```
>   webapp
>   L res
>   |-- common.js
>   |-- common.css
>   L WEB-INF
>     L views
>       L student
>       |-- list.jsp
>       |-- edit.jsp
>       L department
>       |-- list.jsp
>       |-- create.jsp
>   ```
>
>   `WEB-INF/views`는 webapp 폴더 바로 안에 포함이 되어 있어야 View로 이용할 수 있다.
>
> **View의 실존 주소 방식은 다음과 같이 적용한다.**
>   ```
>   @Autowired StudentService studentService;
>   @GetMapping("student/list")
>   public String studentList(Model model){
>       model.addAttribute("students", studentService.findAll());
>       return "student/list";
>   }
>   ```
> - Model에 students 이름을 가진 데이터는 현재 데이터베이스에서 학생 목록들을 조회한 모든 데이터 결과를 보관하고, 위의 파일 목록을 기준으로 /WEB-INF/views/student/list.jsp 는 현재 Model에 포함된 students 데이터에 대한 표출 기능을 추가해야 한다. 
> - 또한 Model의 데이터에 따른 View의 논리를 설정할 때 자체 내부에서 JSTL 문을 이용하여 설정해야 하는데 여기서 View의 신뢰성이 떨어질 수 있다.
>
> **ModelAndView 방식은 다음과 같이 적용한다.**
>   ```
>   @Autowired StudentService studentService;
>   @GetMapping("student/list")
>   public ModelAndView studentList(){
>       ModelAndView modelAndView = new ModelAndView();
>       mv.addObject("students", studentService.findAll());
>       mv.setViewName("/student/list");
>       return modelAndView;
>   }
>   ```
> - 방금 실존 주소를 이용한 방법과 별 차이는 없어 보이지만, ModelAndView 객체에 View에서 보여줄 Model 데이터와 View 주소 이름을 설정을 하여 객체로 반환하는 특징을 보여줄 수 있다. 
> - View에 대한 논리를 설정할 때 Controller에서 처리할 수 있게 되면서 View의 신뢰성을 증대시킬 수 있다.
> 
> 5. Controller의 실행 결과를 보여줄 View를 검색한다.
> - 여기서 검색한다는 뜻은 `application.properties`를 통해 설정한 Prefix와 Surfix를 조합하여 그 View의 페이지로 이동한다는 의미로 생각하면 된다.
>
> 6. 최종적으로 Model를 정리해서 View에 보여준다.
> - View에서 Model의 데이터를 정리할 때에 EL(Expression Language), JSTL(JSP Standard Tag Library)를 대부분 이용한다.

## DispatcherServlet

> Java Enterprise Application의 축소판인 Spring Framework의 시발점

![dispatcherServlet](/Application_Computer_Science/2_Spring_Framework/img/dispatcherServlet.png)

`DispatcherServlet` 내부에는 `WebApplicationContext` 들이 존재하는데 `Servlet` 영역과 `Root` 영역으로 나뉘게 된다. `DispatcherServlet`는 각 Web Application 별로 여러 개 등록하는 것은 실제로는 행하지 않고 이론적인 이야기이다. 실제 사례로는 각 Web Application 별 독자적인 `Servlet WebApplicationContext`를 가지고 있으며, `Root WebApplicationContext`에 대하여 공유하여 이용하고 있다.

`DispatcherServlet`은 모든 HTTP Request, Response에 대하여 참조하는 역할을 한다. 이는 Servlet 버전에 따라 생성될 수 있다.

Servlet만 이용해서 Web Application을 작성할 수 있다는 사실을 이미 짐작할 수 있는데 그러나 web.xml의 역할이 점차 줄어들면서 Servlet 매핑은 결국 `DispatcherServlet`으로 넘겨주는 추세로 바뀌어 가는 건 사실이다.

## Domain Model Structure
우리가 객체 지향 프로그래밍을 이용해서 일상에 필요한 시스템을 소프트웨어에 반영하기 위해서 기초적으로 다뤄야 하는 것이 Domain Model을 제대로 설계하는 것이다. 

Domain Driven Design(DDD)의 핵심은 Domain 내 여러 업무 정의나 문제를 Model로 잘 표현하고 이를 바탕으로 구현하기 쉽게 이해할 수 있는 구조로 정의되어 있는 것이 주름 잡는다고 볼 수 있다. DDD 3가지 요구사항은 아래와 같다.

- Model과의 핵심 설계는 상호 영향을 주고 받으며 구체화 되어야 한다.
- Model은 모든 팀 구성원들이 사용하는 언어를 기반으로 작성되어야 한다.
- Model은 불순물을 걸러낸 핵심 지식만 포함되어야 한다.

Domain Model에서 구현 패턴은 다양하게 존재하는데 일반적으로 사용하는 패턴은 Entity 기반 모델을 정의하는 패턴을 이용한다. Spring Framework에서도 적용이 가능하다. 이를 기준으로 작성한 Domain Model의 구조는 아래와 같다.

![domain_class](/Application_Computer_Science/2_Spring_Framework/img/domain_class.png)

Spring Web MVC에서 쓰이는 Domain Model의 구조는 위처럼 유지하는 것을 권장한다. 각 개념이 무슨 의미를 가지는지 어노테이션과 함께 작성해보도록 하겠다.

- Entity Class (`@Entity`, `@Document`, `@RedisHash`)
    - Database ER Model에서 Table에 해당되는 객체. MongoDB에서는 Document 단위. Redis에서는 RedisHash 단위를 이용.
    - 데이터베이스에 있는 데이터 속성에 따라 가공할 수 있는 클래스를 명시한다.
    - 무조건 Database에 있는 속성을 그대로 복붙한다고 생각하면 안 되고, Java에서 가공 가능한 속성들을 묶어서 정리하는 의미로 받아 들여야 한다.
    - Entity 클래스에서 쓰는 다양한 Annotation은 `Spring Data JPA`에서 차차 다루도록 하겠다.

- Value Object Class (특별한 어노테이션은 없다.)
    - 식별성이 아닌 속성을 이용해 정의되는 불변 객체이다.
    - 식별성을 부여하고 Entity로 관리하면 복잡성이 증대된다.
    - 식별성이 있다면 Entity, 아니면 Value Object로 구분된다.

- Repository Interface (`@Repository`)
    - Domain 영역과 Infrastructure 계층에 대해 분리하여 데이터 계층에 대한 결합도를 낮추기 위해 쓰이는 Interface이다.
    - 생성된 Domain Object에 대한 Persistence를 향상시키고, CRUD(Create, Read, Update, Delete) 작업에 대한 일관성을 유지한다.
    - Database에 현존하는 데이터의 CRUD를 요구할 때 사용한다.

- Service Interface (`@Service`)
    - Domain Object에서 위치시키기 어려운 Operation을 가지는 객체이다.
    - 특히 여러 Domain Object를 다루는 연산에 대하여 적용을 많이 하는데 이러한 Operation은 일반적으로 Stateless(무상태) Operation으로 남게 되는데 Cache에서 데이터 조회를 할 때 임시 데이터를 저장하는 면에선는 Stateless Operation을 권장하고 있다.
    - Domain Object에 해당되는 역할을 Service의 Operation으로 만든다면 Domain 역할을 침범하게 되어 강결합이 발생하게 된다.

- Controller Class (`@Controller`)
    - Client로부터 전달된 데이터를 RequestMapping을 이용해서 가공을 할 수 있게 도와주는 최전방 계층.
    - Controller에서는 Service에게, Service에서는 DAO or Repository에게, DAO or Repository는 DTO or Domain Class에게 요청하는 순서를 꼭 적용해야 한다.
    - Controller 클래스에서 곧바로 Repository 인터페이스를 이용하는 것은 간단하게 공부하기 위한 예제에서 적용할 수는 있지만, 실무에서 키보드로 맞을 수 있으니 되도록이면 자제하도록 하자.

## Front-Controller Pattern

![front_controller](/Application_Computer_Science/2_Spring_Framework/img/front_controller.PNG)

이는 Web Application과 관련된 패턴이다. Page-Controller는 Presenter가 Model과 여러 View에게 요청을 하기 때문에 복잡한 패턴이다. 하지만 Front-Controller Pattern을 이용하는 점에서는 모든 Resource 요청을 처리해주는 역할을 하되 하나의 Controller에서 모두 처리하는 Pattern으로 볼 수 있다.

Front-Controller Pattern의 장점으로는 모든 요청에 대해 하나의 Controller에서 작업할 때 Tracking(추적), Security(보안)를 적용할 때 AOP(Aspect Oriented Programing)의 원리를 이용하게 되어 편의성을 제공한다. 또한 파일 구조가 바뀌어도 URL를 유지하는 점에서도 큰 이득을 볼 수 있다.

## Useful Annotations & Interfaces

**Main Controller Annotations**

- `@Controller`
    - 클래스 상단부에 이를 붙으면 Controller Bean을 자동적으로 구성하게 된다.
    - 이 어노테이션을 붙일 때 View와의 연동이 요구된다.

- `@RestController`
    - 클래스 상단부에 이를 붙으면 REST API를 만들 수 있는 Controller Bean을 자동적으로 구성하게 된다.
    - 주로 JavaScript을 기반으로 한 `SPA`에서 데이터를 받을 때 사용된다.
    - Web에서는 데이터를 JSON으로 반환하게 되는데 `ResponseEntity<T>`를 이용해서 Status와 함께 반환하여 확인할 수 있는 방식을 더욱 지향하고 있다.

- `@ComponentScan`
    - Web Configuration 클래스에서 Controller가 존재하는 패키지를 정의하여 설정할 수 있도록 도와주는 역할을 한다.

**HTTP Head or Options 관련 Annotation**

- `@RequestMapping`
    - 각 Controller에서 요청한 URL에 대해 분석을 진행하고 그 요청에 대해 처리할 수 있는 책갈피 역할을 한다.
    - `name`은 요청 URI, `method`는 HTTP Method를 작성하는 방식, `produces`는 반환하는 데이터에 대한 형식과 인코딩을 설정하는 곳으로 이용된다.
    - 요청 URI에 정규식(Regex Expression)을 넣을 수 있다.

- `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`, `@PatchMapping`
    - 여기서부터는 Spring Web MVC 4.3 버전부터 추가된 어노테이션으로 각각 GET(조회), POST(등록), PUT(수정), DELETE(삭제), PATCH 요청에 대한 URI를 작성할 때 적용하면 된다.
    - URI 속의 파라미터를 가져올 때 `@RequestParam`보다 `@PathVariable`을 더욱 이용한다.

**Handler Method 관련 Annotation or Interface**
- `@RequestBody`
    - 데이터를 객체로 정리해서 보낼 때 주로 이용된다.
    - HTTP Request에 Body 구문에 요청할 데이터 객체를 정리하고 이용해야 한다.
    - 이를 위해서 Value Object(혹은 Model Object)의 규격을 맞춰줘야 한다.

- `@RequestParam`
    - 요청 URL 속에 있는 Query String을 분석해서 이에 해당되는 데이터를 가져올 때 사용된다.
    - 쿼리 스트링 뿐만 아니라 MultipartFile(첨부파일)을 보낼 때도 이를 적용하는 것이 관례이다.
    - Java 8 버전 이후로 Meta-Data Reflection 기능을 제공하게 되어 파라미터 이름을 정확하게 작성하면 이 어노테이션에 속성을 작성하지 않아도 된다.

- `@PathVariable`
    - URI에 현존하는 Template 변수를 가져올 때 쓴다.
    - Template는 Query String과 달리 주소에 포함된 데이터 내용을 뜻한다.

- `@CookieValue`
    - 사용자 별로 등록된 쿠키의 값을 가져올 때 쓰는 어노테이션이다.

- `@ModelAttribute`
    - Controller Method에 일반적으로 객체를 작성하더라도 이 어노테이션을 이용할 필요가 없었지만, Form의 name을 정확하게 일치할 때 이용하는 것을 권장한다.

- `@MatrixVariable`
    - URI에 단일 파라미터가 아닌 배열 데이터를 받아올 때 활용된다.
    - 예를 들어 /student/20;a=10;b=20/department/21;a=12;c=20 URI에서 a의 값은 [10, 12], b의 값은 [20], c의 값은 [20]인데 이를 가져오기 위해서 MultiValueMap을 이용한다.

- `ServletRequest`, `ServletResponse`
    - 현재 Servlet에서 요청, 응답하는 상황을 알고 싶을 때 가져올 수 있는 인터페이스이다.
    - 이 내부에는 요청하는 Header, Body, Parameter, Session Attribute 등의 내용이 포함되어 있다.

- `InputStream`
    - 우리가 알고 있는 `java.io.InputStream` 맞다.
    - Request Body에 대해 이용할 때 쓴다.

- `OutputStream`
    - 마찬가지로 `java.io.OutputStream` 맞다.
    - Response Body에 대해 이용할 때 쓴다.

- `Principal`
    - `Spring Security`를 적용할 때 현재 요청 중인 사용자의 정보를 가져올 때 이용한다.
    - `Spring Security`에 대한 사례는 강박사 노트 Application Computer Science의 3번 보안 Framework의 1번째를 읽어보면 된다.

**Return Values 관련 Annotation or Interface**
- `@ResponseBody`
    - 요청에 대한 응답으로 데이터를 보낼 때 필요한 어노테이션에 해당된다.
    - 그렇지만 REST API를 구축할 때는 그렇게 큰 효과를 보지 못 한다.

- `ResponseEntity<T>`
    - 보내지는 데이터와 이 상황에 맞는 Status를 함께 반환해준다.
    - Spring Web MVC에 본래 존재한 개념인데 REST API를 구축할 때 이는 선택이 아닌 필수로 생각해야 한다.

- `String`
    - `webapp`에서 `View`가 실존하는 주소를 반환하거나 `Redirect` 요청에 대해(`redirect:URL`) 처리할 때 이용된다.

- `Model`
    - 요청된 데이터를 주고 받을 때의 연결 고리의 역할을 하는 `Model`을 이용할 때 쓴다.
    - 물론 Java에 있는 Map을 이용해서 작성할 수 있다. 

- `ModelAndView`
    - Model과 View 주소, 심지어 Status까지 묶어서 보낼 때 이용하는 인터페이스이다.

- `Callable<T>`
    - Asynchronous 요청에 대해 처리한 데이터를 반환할 때 이용한다.
    - 이를 이용하기 위해서는 Spring Asynchronous 개념을 인지하고 있어야 한다.

## References
- https://ko.wikipedia.org/wiki/%EB%AA%A8%EB%8D%B8-%EB%B7%B0-%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC - MVC Pattern 기본 개념
- http://addio3305.tistory.com/41 - Spring Web MVC Structure
- http://qwefgh90.github.io/sphinx/spring/spring_web_mvc.html - Spring Web MVC 작동 원리를 자세하게 작성한 글
- https://nesoy.github.io/articles/2017-02/Front-Controller - Front-Controller에 대한 설명 참고
- https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html - Spring Web MVC 공식 Tutorial
- http://ande226.tistory.com/101 - ModelAndView 객체 사용 방법
- http://yellowh.tistory.com/109 - Domain Model에 대한 Annotation 참조
- http://cyberx.tistory.com/57 - Domain Model 기본 개념을 참고하기 좋은 페이지
- https://www.tutorialspoint.com/spring/spring_web_mvc_framework.htm - Spring WEB MVC를 따라하기 좋은 예제 수준으로 작성한 영문 페이지
- http://egloos.zum.com/springmvc/v/504151 - DispatcherServlet에 대해 구체적으로 설명한 페이지
- http://toby.epril.com/?p=934 - `DispatcherServlet` 원문 보면서 함께 참고한 자료