# Java 8에서 알아두면 좋은 개념
이번에는 **Java 8**에서 어떤 점들이 바뀌면서 어떤 개념들이 추가 되었는가를 짚고 넘어가도록 하자. 참고로 Spring Framework를 가장 안정적으로 적용할 수 있는 버전이 바로 이 버전이다.

## Java 8 SE Release Information
Java 8 버전은 2014년 3월 18일에 발표 되었다. 이번 버전에서 새로운 개념들이 증폭된 시기이기도 하지만, Oracle 사와 Google 사 간 저작권 소송도 또한 이뤄졌는데 2차 소송이 벌어지던 시기이기도 하다.

## Lambda Expression
> Java 8에서 **가장 이슈가 된** 개념.
>
> Java 뿐만 아니라 다른 개발 언어를 공부한다면 이제는 필수가 되어 버린 개념.

Lambda 식을 또 다른 말로 **익명 함수(Anonymous Functions)** 로 부른다. Java 언어 이외에도 여러 언어들(대표적으로 `C++`(C++ 11버전 부터 지원), `Python`(2.7버전 부터 지원), `Javascript`(ES6 버전 부터 지원) etc.)에서도 제공하는 추세로 프로그래머가 되기 위해 어느 정도는 인지하고 있어야 하는 개념이다.

코드의 간결함과 지연 연산을 통한 퍼포먼스 향상과 Iteration(반복자)에 관한 코드를 구현하는데 있어 불필요한 부분을 제거할 수 있는 면에서 장점을 얻을 수 있다.

하지만 모든 원소를 전부 순회할 때 Lambda 식은 Bytecode나 Assmbly Code에서 단순 while, for 문 보다 몇 단계를 더 거쳐야 되는 면에서 일반적인 코드보다 느리고, 너무 많은 Lambda 식은 코드를 이해하기 어려운 단점도 감안해야 한다.

여기서는 `Java`에서 새로 추가 된 Lambda Expression을 어떻게 적용하는가에 대해 인지하고 넘어가는 수준으로 작성하겠다.

우선 `Java`에서는 당연한 이야기지만 Function(함수)의 개념이 없다. 대신에 Object 내부에 method를 이용해서 행위를 전달을 하는 것이 끝이었다. 하지만 순수한 함수(Pure Function. 함수의 실행이 외부의 상태를 변경하지 않는 함수. Multi-Thread 환경에서 권장하는 함수.)의 필요성을 `Java`에서 느끼게 되어 Interface, Labmda Expression을 추가로 지원하게 되었다. Java 8에서 적용할 수 있는 Lambda Expression의 기본 문법과 예제는 다음과 같다.

<h3>Basic Lambda Expression</h3>

```
// int 형의 변수 x를 출력하기 위한 문장은 아래와 같이 이용한다.
(int x) -> { System.out.println(x); }

// 그러나 Lambda Expression에서는 매개 변수 타입을 언급하지 않는 것이 관례이다.
// 이처럼 작성하는 방법을 더욱 권장하고 있다.
(a) -> { System.out.println(a); }

// 하나의 매개 변수만 있으면 굳이 소괄호를 쓰지 않아도 무관하다.
// 그리고 하나의 실행문만 있으면 실행 구문에 중괄호를 쓰지 않아도 무관하다.
b -> System.out.println(b);

// 그러나 매개 변수가 없어도 꼭 소괄호는 써 줘야 한다.
() -> { System.out.println("ABCDEFG"); }

// 중괄호 내부를 실행하고 결과 값을 반환하면 그대로 return 문을 쓰면 된다.
(x, y) -> { return (x > y) ? x : y; }

// 그렇지만 Lambda Expression에서는 return 문 쓰는 것 보다 이를 추구하고 있다.
(x, y) -> (x > y) ? x : y;
```

<h3>Simple Example of Lambda Expression</h3>

```
@FunctionalInterface
interface Order0{
    public void print();
}
@FunctionalInterface
interface Order1{
    public String orderMessage(String food, int quantity);
}
@FunctionalInterface
interface Order2{
    public String orderMessage(String food, int quantity, String option);
}
public class Main{
    public static void main(String[] args){
        // Lambda 예제 1. FunctionalInterface를 이용한 Lambda 표현식 구현
        Order0 order0 = () -> System.out.println("자장면 음식을 1인분 주문했습니다.");
        Order1 order1 = (food, quantity) -> String.format("%s 음식을 %d인분 주문했습니다.", food, quantity);
        Order2 order2 = (food, quantity, option) -> String.format("%s 음식을 %d인분 주문합니다. 요구사항 : %s", food, quantity, option);

        order0.print(); 
        // 결과는 자장면 음식을 1인분 주문했습니다. 로 나온다.
        System.out.println(order1.orderMessage("양념치킨", 3));
        // 결과는 양념치킨 음식을 3인분 주문했습니다. 로 나온다.
        System.out.println(order2.orderMessage("물냉면", 2, "무 절임 많이 넣어 주세요."));
        // 결과는 물냉면 음식을 2인분 주문합니다. 요구사항 : 무 절임 많이 넣어 주세요. 로 나온다.

        // Lambda 예제 2. Collections.sort()에서 요소를 이용한 내림차순 정렬 구현
        List<String> stations = Arrays.asList("영등포역", "대전역", "수원역", "동대구역", "서울역", "강릉역", "전주역");
        Collections.sort(stations, (s1, s2) -> {
            return s1.compareTo(s2) * -1;
        });
        System.out.println(stations);
        // 실행 결과는 [전주역, 영등포역, 수원역, 서울역, 동대구역, 대전역, 강릉역] 으로 문자열 역순서 결과로 출력된다.
    }
}
```

**Functional Interface**를 이용하기 위한 주의 사항으로 interface 내부에 있는 추상 메소드를 반드시 **1개**만 써야 한다. 그래서 Interface를 이용한 Lambda Expression을 구현할 때 여러 추상 메소드에 대한 혼란을 최소화할 수 있는 면에서 `@FunctionalInterface`를 이용하는 것을 권장하고 있다. 또한 함수에 대한 매개변수와 반환 데이터 형이 정해졌고 이를 기반으로 한 함수의 외부에서 다양한 연산을 시도할 수 있는 점에서 Lambda Expression에 대한 메리트로 볼 수 있다.

<h3>Applicative of Lambda Expression</h3>
Android 어플리케이션 제작에서 Lambda Expression은 필수 요소가 되었다.
간단한 사례로 Button의 onClickListener를 제작할 때 Anonymous Class를 이용할 때 축약된 소스 코드로 작성할 수 있는 점에서 큰 이득을 볼 수 있다.

```
// 이 두 버튼의 역할은 버튼을 클릭하면 간단하게 버튼을 클릭한 안내문을 출력하는 것이다.
// 소스 코드를 읽어 보면 Anonymous Class를 이용한 방법보다 더욱 짧아진 모습을 볼 수 있다.

// Anonymous Class를 이용한 기법
Button button1 = (Button)findViewById(R.id.my_button1);
button1.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v){
        Toast.makeText(this, "버튼1을 클릭했습니다.", Toast.LENGTH_SHORT).show();
    }
});

// Lambda Expression을 이용한 기법
Button button2 = (Button)findViewById(R.id.my_button2);
button2.setOnClickListener(v -> Toast.makeText(this, "버튼2를 클릭했습니다.", Toast.LENGTH_SHORT).show());
```

## Optional Interface

`Spring Data JPA`, `Spring Data MongoDB` 등을 자주 다뤘으면 이 개념에 대해서 짚고 넘어가야 한다.

Java 언어에서 제일 발생하기 쉬운 예외는 null인 객체에 대한 멤버 변수를 접근할 때 발생하는 `NullPointerException`이다.

객체의 존재(객체의 null 여부)에 따른 NullPointerException을 최소화를 하기 위한 방법 중에서 객체에 대한 보호막을 씌우는 역할이 필요하다. 이를 `Optional` 개념이라고 볼 수 있다.

![sonic_monitors](/Application_Computer_Science/0_Java_Programing/img/sonic_monitors.jpg)

**Optional**의 개념은 쉽게 이야기한다면 Sonic 게임 시리즈에서 나오는 아이템 모니터와 같은 원리로 생각하면 된다.

> - Sonic이 링이 그려진 모니터를 깨트린다. 그럼 Sonic이 현재 얻은 링의 수가 10개 더 누적된다.
>   - Optional 속에 객체가 존재한다. 이제 이 객체를 메소드 내에서 쓸 수 있다.
> - Sonic이 아무 것도 없는 모니터를 깨트린다. 그러나 아무 일도 안 일어난다.
>   - Optional 속에 객체가 없다. 이 객체는 null로 반환되는 것이 아니라 아무 일도 안 일어나게 된다.

아래 소스 코드는 Optional&lt;T&gt;를 이용하는 방법 중에서 최근에 이를 기반으로 Database에 접근할 수 있는 JPA(Java Persistence API)의 Repository를 Service 함수 내에서 쓰는 사례로 작성하였다.

```
// 방법 1. get() 함수를 이용해서 Person 객체를 반환하기
public Person findOne(long id){
    Optional<Person> person = personRepository.findById(id);
    // 여기서 person 내부에 실제 Person 데이터가 있으면 이를 반환하고 아니면 null를 반환한다.
    if(person.isPresent()) return person.get();
    else return null;
}

// 방법 2. orElse(object) 함수를 이용해서 Person 객체를 반환하기
public Person findOne(long id){
    Optional<Person> person = personRepository.findById(id);
    // 여기서 person 내부에 실제 Person 데이터가 들어 있으면 이를 반환하고 없다면 null을 반환한다.
    return person.orElse(null);
}
```

## Stream API
[계속 작성하겠습니다.]

## Default Method in Interface

Java에서 Interface에는 추상 메소드(Abstract Method)를 작성하고 이를 또 다른 Class에서 구현하는 방법으로 객체와 행위(Method)에 대한 활용도를 향상시키는데 큰 역할을 하고 있다. Java 7까지만 해도 Abstract Method만 작성해서 이용하는 것이 전부였는데 Interface만의 행위를 만들어서 다양한 Class 내부에서 Abstract Method에 대해 구현하는 것을 최소화하기 위한 목적으로 Default Method와 Static Method를 Inferface에서 처리할 수 있게 된 점이 Java 8에서 새로 추가되었다. 이를 적용하는 간단한 예제를 아래 소스 코드를 읽어 보도록 하자.

```
interface AbstractPerson{ // 가상 등장인물 인터페이스
    void say();
    void hello();
}
interface AbstractNarration extends AbstractPerson{ // 가상 나레이션 인터페이스
    public default void bye(){
        System.out.println("[나레이션] 1972년 11월 21일. 김두한은 쓰러졌다.");
    }
    public static void start(){
        System.out.println("[나레이션] 나레이션이 시작됩니다.");
    }
}
class Person implements AbstractPerson{ // 실제 등장인물
    private String name;
    private String message;
    public Person(String name, String message){
        this.name=name;
        this.message=message;
    }
    @Override
    public void say(){
        System.out.println(String.format("[%s] %s", this.name, this.message));
    }
    @Override
    public void hello(){
        System.out.println(String.format("[%s] 안녕하세요?", this.name));
    }
}
class Narration implements AbstractNarration{ // 실제 나레이션
    private String message;
    public Narration(String message){
        this.message=message;
    }
    @Override
    public void say(){
        System.out.println(String.format("[나레이션] %s", this.message));
    }
    @Override
    public void hello(){
        System.out.println("[나레이션] 테스트 중입니다...");
    }
}
public class DefaultMethod {
    public static void main(String[] args){
        Person p1 = new Person("김두한", "4달러~"); // 등장인물 생성
        p1.hello(); // [김두한] 안녕하세요?
        p1.say(); // [김두한] 4달러~

        Narration n1 = new Narration("그랬다. 김두한은 4달러를 좋아했다."); // 나레이션 생성
        AbstractNarration.start(); // [나레이션] 나레이션이 시작됩니다.
        n1.hello(); // [나레이션] 테스트 중입니다...
        n1.say(); // [나레이션] 그랬다. 김두한은 4달러를 좋아했다.
        n1.bye(); // [나레이션] 1972년 11월 21일. 김두한은 쓰러졌다.
    }
}
```

위의 소스 코드의 내용을 토대로 AbstractNarration 인터페이스에 있는 default 메소드인 bye() 함수와 static 메소드인 start() 메소드를 주목해야 하는데 AbstractNarration 인터페이스를 클래스에서 상속을 받기 위해서 hello(), say() 메소드를 구현해야 한다. 

그렇지만 AbstractNarration 인터페이스에서 쓰인 bye() 메소드는 AbstractPerson을 상속하였기 때문에 hello(), say() 등의 추상 메소드를 구현하는 요소와 나중에 추가 될 AbstractNarration 인터페이스의 추상 메소드 등을 상속 받은 클래스에서 구현하는 번거로움을 줄이도록 하기 위해 작성하였다. Inteface에서 Default Method의 역할은 다중 상속(참고로 Interface는 다중 상속이 가능하다.)에서 늘어나는 추상 메소드의 구현량을 줄임으로서 Interface의 효율성을 증대 시킬 수 있다.

또한 Interface에서 Static Method를 이용할 수 있는데 Interface에서 간단한 연산 작업을 처리할 수 있는 유용성 메소드를 구현할 때 이용하면 적합하다. 여기서 AbstractNarration 인터페이스에 쓰인 start() 메소드로 볼 수 있다. Class에서 Static Method를 이용하는 방법과 유사하다.

<h3>Applicative of Default Method</h3>

Default Method를 응용할 수 있는 곳은 바로 `JPA`에서 Pagination을 구현할 때 기본으로 제공하는 findAll() 함수에 대해 변경할 경우 사용하는 사례를 들 수 있다. 또한 Interface에서도 변수를 볼 수 있는데 이는 Interface에 작성된 static final 변수로 볼 수 있다. 추가로 Default Method와 Static Method는 Interface에서 여러 개를 써도 무방하다.

```
@Repository
public interface FoodRepository extends JpaRepository<Food, Long>{
    public static final int PAGE_SIZE = 10;
    // 앞에 static final이 생략되어 그렇지 실제로 이는 외부 클래스의 method에서 변경이 불가능하다.
    Option[] searchBy = { new Option(0, "검색 없음"), new Option(1, "음식 이름"), new Option(2, "주문 가능 시간"), new Option(3, "가격") }; 
    public default List<Food> findAll(Pagination pagination){
        // Pagination에 대한 내용 구현
    }
}
```

## Date API Update
[계속 작성하겠습니다.]

## IO/NIO Expansion
[계속 작성하겠습니다.]

## Meta Data Reflection About Parameters
`Spring Framework`에서 `@RequestParam`, `@RequestBody` 어노테이션을 이용할 때 매개변수에 대해 이름을 정해서 이용하는 방법을 밥 먹듯이 했을 것이다. 예를 들어 어느 View에서 학생에 대한 정보를 id라는 변수를 이용해 가져와서 보여주는 사례다.

```
@RequestMapping("student/info")
public String showAboutStudent(Model model, @RequestParam("id") int id){
    model.addAttribute("student", studentService.findOne(id));
    return "student/info";
}
```

이처럼 작성을 꼭 해야만 했던 이유는 Java 7까지만 해도 많은 메타 데이터(Query String에 있는 데이터도 포함 된다.) 정보를 Byte Code에 남기긴 하지만, Method의 매개 변수의 이름 따윈 신경을 쓰지 않아서 타입만 확인이 가능했기 때문이다. (물론 Java Compiler에 -debug 옵션을 줘서 Byte Code에 디버깅용 소스를 남기면 자동으로 매개변수 이름을 인식하는 기능이 Spring Framework에서는 제공을 하였다.)

이처럼 메타 데이터에 대해서 매개 변수의 이름을 동일하게 받아 적용하는 과정을 Reflection으로 부르는데 Java 8에서 이 기능을 제공하게 되면서 오로지 어노테이션으로 구분을 하고, 변수 이름을 제대로 선언한다면 위와 같은 작업에 대해 작동할 수 있도록 도와준다.

```
@RequestMapping("student/info")
public String showAboutStudent(Model model, @RequestParam int id){ // 방금 전까지 RequestParam에 변수 이름을 넣었지만, Reflection 기능을 제공하여 같은 변수 이름이라면 굳이 안 써도 된다.
    model.addAttribute("student", studentService.findOne(id));
    return "student/info";
}
```

## References
- https://namu.wiki/w/%EB%9E%8C%EB%8B%A4%EC%8B%9D - Lambda Expression 정의 참고
- https://skyoo2003.github.io/post/2016/11/09/java8-lambda-expression - Lambda Expresion 개념과 예제 소스 코드 참고
- http://palpit.tistory.com/671 - Lambda 식 기본 이용 방법
- https://wraithkim.wordpress.com/2017/04/13/java-8-%EC%8A%A4%ED%8A%B8%EB%A6%BC-%ED%8A%9C%ED%86%A0%EB%A6%AC%EC%96%BC/ - Java 8 Stream 튜토리얼
- http://blog.eomdev.com/java/2016/03/30/default-method.html - Default Method In Interface
- http://blog.fupfin.com/?p=27 - Java 8 변경 사항