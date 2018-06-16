# Java Enumeration Issues

![bills](/Application_Computer_Science/0_Java_Programing/img/bills.jpg)

우리 나라의 지폐는 1000원, 5000원, 10000원, 50000원 권이 있다. 각 지폐에 대한 단위를 정해두는 이유는 무엇일까? 예를 들어 거스름 돈이 5300원인 경우에 5000원 짜리 1장과 100원짜리 3개를 주면 되는데 4000원 짜리 지폐 1장과 1300원 짜리 지폐 1장으로 준다면 더욱 단위가 안 잡힌다. 그래서 위처럼 언급한 단위를 나뉘는 과정을 열거라고 한다.

Java에서는 클래스 내부에서 진행하는 것들이 눈에 띄게 나뉜다. 예를 들어 남자와 여자를 분별하는 것부터 시작해서 색상을 구분할 때 등이 있다.

Java에서도 C, C++ 처럼 Enumeration 클래스가 존재한다. 위에서 내부에서 진행하는 개념들을 Type화 하기 위해서는 이 개념을 적극 활용하는 것을 추천한다.

이번에는 Java에서 Enumeration 개념을 적용하는 방법에 대해서 자세히 알아보도록 하자.

## Enumeration Class Structure

![enumeration_uml](/Application_Computer_Science/0_Java_Programing/img/enumeration_uml.png)

Java 에서 Enumeration Class를 따로 생성한다면, 위의 사진 처럼 Enum 클래스(java.lang.Enum)를 자동으로 상속한다.

여기서 name은 TEN, FIFTY 등이고, ordinal은 TEN은 0, FIFTY는 1, ... FIFTY_THOUSAND는 7로 각 name 선언 순서 대로 부여한 index이다.

Enumeration Class를 이용하기 이전에는 아래와 같은 순서로 구현 했던 것이 전부였다.

## Example Of Enumeration

### Static Final or Interface

```
public class MoneyMain{
    public static final int TEN = 10;
    public static final int FIFTY = 50;
    public static final int ONE_HUNDRED = 100;
    public static final int FIVE_HUNDRED = 500;
    public static final int ONE_THOUSAND = 1000;
    public static final int FIVE_THOUSAND = 5000;
    public static final int TEN_THOUSAND = 10000;
    public static final int FIFTY_THOUSAND = 50000;
}
```

```
public interface MoneyMain{
    public int TEN = 10;
    public int FIFTY = 50;
    public int ONE_HUNDRED = 100;
    public int FIVE_HUNDRED = 500;
    public int ONE_THOUSAND = 1000;
    public int FIVE_THOUSAND = 5000;
    public int TEN_THOUSAND = 10000;
    public int FIFTY_THOUSAND = 50000;
}
// interface에 있는 모든 변수는 static final 변수로 이용되어 위와 똑같다.
```

static final 키워드를 이용한 상수 선언은 값들을 변경할 수 없는 점에서 매우 좋은 방법이다. 하지만 이러한 방법은 몇 가지 한계가 존재한다.

**No Type Safety**

첫 번째로 짐작할 수 있는 문제가 바로 타입에 안전하지 않다는 점이다. 여기 이외의 단위인 2000원에 대해서 유효한 Type을 넣으려고 해도 이 값을 대표하는 값은 존재하지 않는다.

**No Meaningful Printing**

상수를 이용해서 어느 값을 출력할 때 TEN_THOUSAND의 뜻을 함유해야 한다. 그렇지만 이 변수를 이용한다면 방금 값이 아닌 1000의 값을 출력하는 것으로 끝난다.

static final 변수를 이용한다 쳐도, 의미 있는 이름의 상수는 무용지물이 된다.

**No Namespace**

Namespace는 C++에서 어딘가의 소속의 뜻으로 쓰인다. 위의 클래스에서 5000원 단위를 가져올 때 FIVE_THOUSAND가 아니라 MoneyMain.FIVE_THOUSAND 으로 고쳐 사용해야 한다.

이러한 점에서 Java에서 Enumeration을 적용할 때의 한계가 나온다. 이를 극적으로 해결하는 Enumeration Class를 소개한다.

### Enumeration Class

```
public enum MoneyMain{
    TEN, FIFTY, ONE_HUNDRED, FIVE_HUNDRED, ONE_THOUSAND, FIVE_THOUSAND, TEN_THOUSAND, FIFTY_THOUSAND;
}
```

방금 전 까지만 해도 일일히 변수를 선언했지만, 여기서는 상수의 이름을 대문자로만 작성만 해도 자동으로 static final 변수로 만들어준다. 그래서 위에서 선언한 값에 대해 해당 값을 절대로 바꾸지 못 하는 특징이 있다. 이는 static final 변수도 마찬가지인데, 방금 전 까지 이용했던 방법보다 Type을 안전하게 묶을 수 있고, Namespace도 가지게 된다.

또한 아래 처럼 int 형이나 char 형으로 초기화해서 적용하거나 의미 있는 상수에 대한 부가 설명을 쓰기 좋은 toString() 메소드처럼 오버라이딩이 가능하다.

```
public enum MoneyMain{
    TEN(10), FIFTY(50), ONE_HUNDRED(100), FIVE_HUNDRED(500), ONE_THOUSAND(1000), FIVE_THOUSAND(5000), TEN_THOUSAND(10000), FIFTY_THOUSAND(50000);

    private int value;
    private MoneyMain(int value){
        this.value = value;
    }

    public int getValue(){
        return this.value;
    }

    @Override
    public String toString(){
        String result = null;
        switch (this) {
            case TEN :
                result = String.format("Ten : %d", this.value);
                break;
            case FIFTY :
                result = String.format("Fifty : %d", this.value);
                break;
            case ONE_HUNDRED :
                result = String.format("One Hundred : %d", this.value);
                break;
            case FIVE_HUNDRED :
                result = String.format("Five Hundred : %d", this.value);
                break;
            case ONE_THOUSAND :
                result = String.format("One Thousand : %d", this.value);
                break;
            case FIVE_THOUSAND :
                result = String.format("Five Thousand : %d", this.value);
                break;
            case TEN_THOUSAND :
                result = String.format("Ten Thousand : %d", this.value);
                break;
            case FIFTY_THOUSAND :
                result = String.format("Fifty Thousand : %d", this.value);
                break;
        }
        return result;
    }
}
```

Enumeration 생성자는 private로 구성된다. 이외의 생성자는 오류를 방출한다. 생략을 하더라도 private로 생성이 된다. 그래서 enum 클래스 외부에서는 new 연산자를 이용하여 생성이 애초에 불가능한 것이었다.

그렇다면 FIFTY는 int 형 값 50이랑 똑같다는 의미일까? 전혀 그렇지 않다. C언어의 enum 개념은 Java와 전혀 다르게 돌아간다. C언어에서 FIFTY를 50으로 설정한다면 FIFTY와 50이 같은 값이 되지만, Java에서는 Enum 클래스에 선언된 값들도 전부 Type이 유지되게 하기 때문이다.

위의 Enumeration Class를 기반한 테스팅에서 아래와 같은 문장을 작성한다면 어떤 정답이 나올지 예측해보도록 하자.

```
public class MoneyTest{
    public static void main(String[] args){
        MoneyMain m1 = MoneyMain.TEN;
        MoneyMain m2 = MoneyMain.FIFTY;
        MoneyMain m3 = MoneyMain.TEN;

        System.out.println(m1 == m3); // 1

        for(Money m : Money.values()){ // 2
            System.out.println(m);
        }
    }
}
```

1번에서는 MoneyMain 형 Enumeration 객체는 곧 static final 변수이기 때문에 m1과 m3의 데이터 값이 같기 때문에 **true**를 반환한다. Enumeration 객체는 Type 별로 1개씩만 생성하는 Singleton Pattern과 유사하다. 참고로 이 개념도 Enumeration Class로 구현할 수 있는 사실은 안 비밀이다.(KangBakSa 객체 지향 패턴 Singleton Pattern 참고.)

이를 반영하였기 때문에 switch 문에서 Enumeration를 이용한 비교를 진행할 때 매우 유리하다.

```
MoneyMain m1 = MoneyMain.TEN;
switch(m1){
    case TEN : 
        System.out.println("다보탑");
        break;
    case FIFTY : 
        System.out.println("벼");
        break;
    case FIVE_HUNDRED : 
        System.out.println("두루미");
        break;
    default : 
        System.out.println("인물");
        break;
}
```

2번에서는 MoneyMain 형 Enumeration 객체에 해당되는 모든 Type 별 toString() 메소드를 통해 보여주는 역할을 한다. 여기서는 방금 TEN, FIFTY ... , FIFTY_THOUSAND 순으로 변수를 선언했기 때문에 이 순서를 유지한 상태로 반환이 된다.

```
Ten : 10
Fifty : 50
One Hundred : 100
Five Hundred : 500
One Thousand : 1000
Five Thousand : 5000
Ten Thousand : 10000
Fifty Thousand : 50000
```

**Abstract Method In Enumeration**

Enumeration Class에서도 마찬가지로 추상 메소드를 작성할 수 있다.

예를 들어 동전이나 지폐 앞면에 나오는 사진 주체를 출력하는 메소드인 frontPicture()를 작성할 때이다. 또한 Enumeration 객체에서도 Abstract Method는 모두 구현이 되어 있어야 정상 작동을 한다.

```
public enum Money{
    TEN(10){
        @Override
        public void frontPicture(){
            System.out.println("다보탑");
        }
    }, FIFTY(50){
        @Override
        public void frontPicture(){
            System.out.println("벼");
        }
    }, ONE_HUNDRED(100){
        @Override
        public void frontPicture(){
            System.out.println("이순신 장군");
        }
    }, FIVE_HUNDRED(500){
        @Override
        public void frontPicture(){
            System.out.println("두루미");
        }
    }, ONE_THOUSAND(1000){
        @Override
        public void frontPicture(){
            System.out.println("퇴계 이황");
        }
    }, FIVE_THOUSAND(5000){
        @Override
        public void frontPicture(){
            System.out.println("율곡 이이");
        }
    }, TEN_THOUSAND(10000){
        @Override
        public void frontPicture(){
            System.out.println("세종 대왕");
        }
    }, FIFTY_THOUSAND(50000){
        @Override
        public void frontPicture(){
            System.out.println("신사임당");
        }
    };

    private int value;
    Money(int value){
        this.value = value;
    }

    public int getValue(){
        return this.value;
    }
    
    public abstract void frontPicture();
}
```

이를 작성하고 아래와 같은 함수를 돌릴 경우의 결과는 아래 주석과 같이 나온다.

```
public class MoneyTest{
    public static void main(String[] args){
        for(Money m : Money.values()){
            m.frontPicture();
        }
    }
}
/* 10원부터 5만원까지 앞면에 나온 사진 주체를 반환한다.
다보탑
벼
이순신 장군
두루미
퇴계 이황
율곡 이이
세종 대왕
신사임당
*/
```

## References
- https://bluepoet.me/2012/07/18/%EB%B2%88%EC%97%AD%EC%9E%90%EB%B0%94-enum%EC%9D%98-10%EA%B0%80%EC%A7%80-%EC%98%88%EC%A0%9C/ - Java Enumeration을 직접 구현하면서 자세히 설명한 글

## Post Script
- 여기에 작성된 내용 이외에도 필요한 개념들을 발견하게 된다면 언제든지 갱신될 수 있습니다.
- 이 강의노트 개념에서 더욱 다뤄주면 좋은 개념들이나 오탈자가 있으시면 KangBakSa Issues에 올려주세요.