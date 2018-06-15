# Java Enumeration Issues

![bills](/Application_Computer_Science/0_Java_Programing/img/bills.jpg)

우리 나라의 지폐는 1000원, 5000원, 10000원, 50000원 권이 있다. 각 지폐에 대한 단위를 정해두는 이유는 무엇일까? 예를 들어 거스름 돈이 5300원인 경우에 5000원 짜리 1장과 100원짜리 3개를 주면 되는데 4000원 짜리 지폐 1장과 1300원 짜리 지폐 1장으로 준다면 더욱 단위가 안 잡힌다. 그래서 위처럼 언급한 단위를 나뉘는 과정을 열거라고 한다.

Java에서는 클래스 내부에서 진행하는 것들이 눈에 띄게 나뉜다. 예를 들어 남자와 여자를 분별하는 것부터 시작해서 색상을 구분할 때 등이 있다.

Java에서도 C, C++ 처럼 Enumeration 클래스가 존재한다. 위에서 내부에서 진행하는 개념들을 Type화 하기 위해서는 이 개념을 적극 활용하는 것을 추천한다.

이번에는 Java에서 Enumeration 개념을 적용하는 방법에 대해서 자세히 알아보도록 하자.

## Enumeration Class Structure

![enumeration_uml](/Application_Computer_Science/0_Java_Programing/img/enumeration_uml.png)

## Example Of Enumeration

### Static Final

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

### Interface

```
public interface MoneyUnit{
    public int TEN = 10;
    public int FIFTY = 50;
    public int ONE_HUNDRED = 100;
    public int FIVE_HUNDRED = 500;
    public int ONE_THOUSAND = 1000;
    public int FIVE_THOUSAND = 5000;
    public int TEN_THOUSAND = 10000;
    public int FIFTY_THOUSAND = 50000;
}
```

### Enumeration Class
```
public enum CoinUnit{
    TEN, FIFTY, ONE_HUNDRED, FIVE_HUNDRED;
}
```

```
public enum BillUnit{
    ONE_THOUSAND, FIVE_THOUSAND, TEN_THOUSAND, FIFTY_THOUSAND;
}
```

```
```

## References
- https://bluepoet.me/2012/07/18/%EB%B2%88%EC%97%AD%EC%9E%90%EB%B0%94-enum%EC%9D%98-10%EA%B0%80%EC%A7%80-%EC%98%88%EC%A0%9C/ - Java Enumeration을 직접 구현하면서 자세히 설명한 글