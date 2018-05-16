# Java 8에서 알아두면 좋은 개념
이번에는 **Java 8**에서 어떤 점들이 바뀌면서 어떤 개념들이 추가 되었는가를 짚고 넘어가도록 하자. 참고로 Spring Framework를 가장 안정적으로 적용할 수 있는 버전이 바로 이 버전이다.

## Java 8 SE Release Information
Java 8 버전은 2014년 3월 18일에 발표 되었다. 이번 버전에서 새로운 개념들이 증폭된 시기이기도 하지만, Oracle 사와 Google 사 간 저작권 소송도 또한 이뤄졌는데 2차 소송이 벌어지던 시기이기도 하다.

## Lambda Expression
> Java 8에서 **가장 이슈가 된** 개념.

Lambda 표현 식은 Java 언어 이외에 여러 언어들에서 제공하고 있는 기능으로 볼 수 있다.

```
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
        
        Order1 order1 = (food, quantity) -> String.format("%s 음식을 %d인분 주문했습니다.", food, quantity);
        Order2 order2 = (food, quantity, option) -> String.format("%s 음식을 %d인분 주문합니다. 요구사항 : %s", food, quantity, option);

        System.out.println(order1.orderMessage("양념치킨", 3));
        // 결과는 양념치킨 음식을 3인분 주문했습니다. 로 나온다.
        System.out.println(order2.orderMessage("물냉면", 2, "무 절임 많이 넣어 주세요."));
        // 결과는 물냉면 음식을 2인분 주문합니다. 요구사항 : 무 절임 많이 넣어 주세요. 로 나온다.

        // Lambda 예제 2. Collections.sort()에서 요소를 이용한 정렬 구현

        List<String> stations = Arrays.asList("영등포역", "대전역", "수원역", "동대구역", "서울역", "강릉역", "전주역");
        Collections.sort(stations, (String s1, String s2) -> {
            return s1.compareTo(s2) * -1;
        });

        System.out.println(stations);
        // 실행 결과는 [전주역, 영등포역, 수원역, 서울역, 동대구역, 대전역, 강릉역] 으로 역순서로 나온다.
    }
}

```
## Optional Interface

Spring Data JPA, Spring Data Redis, MongoDB 등을 공부한 사람들이 많이 접했는데 객체를 불러올 때 쓴다.
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
```
@Repository
public interface FoodRepository extends JpaRepository<Food, Long>{
    public default List<Food> findAll(Pagination pagination){
        // Pagination에 대한 내용 구현
    }
}
```
## Date API Update
[계속 작성하겠습니다.]

## IO/NIO Expansion
[계속 작성하겠습니다.]
