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

`Spring Web MVC`에서 쓸 User Interface(`HTML`, `JSP`, `JSTL` etc.)를 이용해 Model를 이용해 가공된 데이터를 보여주는 역할과 사용자가 Controller에게 요구 사항을 보내서 처리하도록 지시할 수 있게 만드는 역할을 한다. View는 여러 개가 존재할 수 있다. 최근에는 JSP 이외에도 JavaScript Singled Page Application을 더욱 쓰는 추세로 앞으로도 이의 기반에 맞춘 언어들(`TypeScript`, `JSX` etc.)이 증가하고 있다. 

### Controller

> 소프트웨어의 카운터를 담당하는 개념

View에는 여러 개의 Controller를 함유하고 있다. 사용자가 View에서 보여진 데이터를 변경하기 위해서는 Controller를 꼭 거쳐야 가능하다. Controller에게 전달 받은 요구 사항을 통해 Model의 상태가 바뀌게 된다면 등록된 View에 자기 자신이 바뀌었다는 사실을 알리고 View는 Model에서 최근에 변경된 데이터를 반영해서 보여준다.

## Structure of Spring Web MVC

![web_mvc_pattern](/Application_Computer_Science/2_Spring_Framework/img/web_mvc_structure.png)

Spring Web MVC의 구조는 위와 같이 나뉘게 된다. 위와 같은 Pattern을 Front-Controller Pattern이라고 한다. 적용하는 과정에 대해서 구체적으로 읽어볼 필요가 있다.

**Spring Web MVC의 작동 순서**
> 1. Client(Web Browser)에서 URL를 요청한다.
> - 요청 URL를 DispatcherServlet에서 읽어들인다.
> 2. 요청 URL과 매핑되는 Controller를 검색한다.
> - @RequestMapping, @GetMapping, @PostMapping 등에 기재된 요청 URL를 검색한다.
> 3. Controller에 처리 요청한다.
> 4. ModelAndView 아니면 View의 실존 주소를 반환한다.
> 5. Controller의 실행 결과를 보여줄 View를 검색한다.
> 6. 최종적으로 Model를 정리해서 View에 보여준다.


![domain_class](/Application_Computer_Science/2_Spring_Framework/img/domain_class.png)

## Front-Controller Pattern

![front_controller](/Application_Computer_Science/2_Spring_Framework/img/front_controller.PNG)

이는 Web Application과 관련된 패턴이다. Page-Controller는 Presenter가 Model과 여러 View에게 요청을 하기 때문에 복잡한 패턴이다. 하지만 Front-Controller Pattern을 이용하는 점에서는 모든 Resource 요청을 처리해주는 역할을 하되 하나의 Controller에서 모두 처리하는 Pattern으로 볼 수 있다.

Front-Controller Pattern의 장점으로는 모든 요청에 대해 하나의 Controller에서 작업할 때 Tracking(추적), Security(보안)를 적용할 때 AOP(Aspect Oriented Programing)의 원리를 이용하게 되어 편의성을 제공한다. 또한 파일 구조가 바뀌어도 URL를 유지하는 점에서도 큰 이득을 볼 수 있다.

## Useful Annotations In Controller
- `@RequestMapping`

- `@GetMapping`

- `@PostMapping`

- `@PutMapping`

- `@DeleteMapping`

- `@PatchMapping`

- `@ResponseBody`

- `@RequestBody`

- `@RequestParam`

- `@PathVariable`



## References
- https://ko.wikipedia.org/wiki/%EB%AA%A8%EB%8D%B8-%EB%B7%B0-%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC - MVC Pattern Basic Issues
- http://addio3305.tistory.com/41 - Spring Web MVC Structure
- http://qwefgh90.github.io/sphinx/spring/spring_web_mvc.html - Spring Web MVC 작동 원리를 자세하게 작성한 글
- https://nesoy.github.io/articles/2017-02/Front-Controller - Front-Controller에 대한 설명 참고
- https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html - Spring Web MVC Tutorial 영문 참고