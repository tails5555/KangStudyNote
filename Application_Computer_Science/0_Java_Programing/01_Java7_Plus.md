# Java 7에서 알아두면 좋은 개념
우리가 Java Programing을 계속 진행하면서 각 버전 별로 어떤 개념들이 추가되어 오는가를 공부할 필요가 있다. 그래서 Java 7, 8, 9 버전 별로 개선이 되고 있는 개념들을 공부할 기회를 가져보도록 하자. 여기서는 **Java 7**에서 추가 된 개념들을 우선적으로 다루고, 다음은 Java 8에 대해서 자세하게 살펴보도록 한다.

## Java 7 SE Release Information
생각보다 오래 됐는데 2011년 7월 7일에 처음으로 공개되었다. 이 때는 Oracle 사가 Java 언어를 인수한 첫 번째 버전으로 볼 수 있다.

이 버전이 발표된 직후에 Oracle이 Java를 인수하였고, Oracle 측과 Google 측에서 java.util 패키지에 대한 저작권 문제에 대한 1차 소송이 벌어졌던 때이다.

## Type Interface
아주 간단한 예로 Map에 Key 값을 Integer 형 객체로, Value 값을 List 객체를 통해 저장한다고 가정하자.

Java 7 버전 이전에는 아래와 같이 작성을 했었다.

```
Map<Integer, List<String>> map = new HashMap<Integer, List<String>>();
```

양쪽에다가 Type을 구분해서 넣는 것은 좋은 것 보단 귀찮음이 존재한다. 그렇지만 Java 7에서 Diamond Operator를 이용하면 2번 이상 쓰지 않아도 된다는 점과 소스 코드에 대한 용량을 줄일 수 있다. <>으로 되어 있는 것이 바로 Diamond Operator이다.

```
Map<Integer, List<String>> map = new HashMap<>();
```

## switch 함수에 String 쓰기
```
String animal = "DOG";
String sound = "";
switch(animal){
    case "DOG" : 
        sound = "멍멍";
        break;
    case "CAT" : 
        sound = "야옹";
        break;
    case "PIG" : 
        sound = "꿀꿀";
        break;
    default : 
        sound = "음소거";
        break;
} 
```
Java 7 이전에 switch 함수의 기준 변수는 int 형 데이터, char 형 데이터, Enumeration으로 구분 된 데이터 등만 이용할 수 있었는데 이 버전 이후로 갱신이 되어 switch 문에서도 String을 적용할 수 있게 되었다. switch 문에서 String을 활용할 때 Enumeration, final 상수 등을 이용하면 정확한 String 값을 적용하는데 도움을 준다. 

## Try-With-Resource
예를 들어 로컬 디스크 C에 있는 example 폴더의 test.txt에 있는 내용들을 읽어서 출력하는 사례로 설명을 하겠다. 파일 입-출력에 필요한 객체(InputStream, OutputStream etc.)에 대해 작업 완료 혹은 불시의 문제에 따라 입-출력 간의 종단(finally 구문에서 close() 메소드 이용. 이러한 기능을 제공하는 객체를 Closable Object 라고 한다.)을 해 줄 필요가 있다. Java 7 이전으로 작성한다면 아래와 같다.

```
File testFile = new File("C:\\example", "test.txt");
BufferedReader br = null;
try{
    br = new BufferedReader(new FileReader(testFile));
    String line;
    while((line = br.readLine() != null)){
        System.out.println(line);
    }
}catch(IOException e){
    e.printStackTrace();
}finally{
    if(br != null)
        br.close();
}
```
그렇지만 Java 7에서 제공하는 Try-With-Resource 함수를 이용해서 작성한다면 짧게 작성할 수 있다.
```
File testFile = new File("C:\\example", "test.txt");
try(
    BufferedReader br = new BufferedReader(testFile);
){
    String line;
    while((line = br.readLine() != null)){
        System.out.println(line);
    }
}catch(IOException e){
    e.printStackTrace();
}
```
try를 실행할 때 BufferedReader 객체가 생성됨과 동시에 testFile 내용에 대한 참조가 끝나면 자동으로 객체에 대해 종결(finally 구문에 close() 함수 이용하는 것)을 해 주는 역할을 한다.

참고로 자동으로 처리하기 위해서는 자원 관리가 필요한 객체 중 **AutoCloseable, Closeable 인터페이스**가 구현이 되어 있어야 한다. 예를 들어 어느 클래스를 만든다면 아래와 같이 Closeable에 있는 close() 함수를 Override하면 된다.

```
public class Position implements Closeable{
    ...
    @Override
    public void close() throws IOException{
        // 자원 관리를 마치고 진행하고 싶은 내용을 여기에 쓰면 된다.
    }
}
```

## Exception in |
한 함수 내부에서 다중 예외들에 대한 처리를 진행한다면 어떻게 해야 할까? 과거까진 다중 예외들에 대해서 각각 처리를 하는 방법을 이용했지만, Java 7에서는 | 기호를 이용해서 함께 처리할 수 있다.
```
// Java 7 이전 까지는 아래와 같이 작성을 해야 한다.
try{
    ...
} catch(SQLException e){
    e.printStackTrace();
} catch(IOException e){
    e.printStackTrace();
}

// 그러나 이처럼 작성하면 각 예외에 대한 번거로움을 줄일 수 있다.
try{
    ...
} catch(SQLException | IOException e){
    ...
}

// 아래의 문장은 컴파일 오류가 난다. 
// Exception을 제외한 같은 부모의 자식 예외들을 동시에 다중 예외로 처리하면 안 된다.
// 여기서 UnsupportedEncodingException과 FileNotFoundException은 부모가 IOException이다.
try{
    ...
} catch(UnsupportedEncodingException | FileNotFoundException e){
    ...
}
```

## Precise Rethrowin'
예외를 처리하기 위해 각 예외들에 대해 정리를 해두면 메소드 내부에 있는 try 문에 대해서만 작동을 한다. 그렇지만 현재 작동 중인 메소드에서 외부 메소드에 예외가 발생했다는 증거를 알리기 위해서 throws 문을 이용을 해야 한다. 과거에는 throws Exception으로만 작성을 해서 어떤 예외인지 구분하기 어려웠는데 각 예외 종류 별로 작성 한다면 외부 함수에서도 예외 종류 별로 catch 문에서 처리한다.
```
// Java 7 이전. 
// 이 함수를 실행할 때 중간에 예외가 발생하면 내부에서 자체적인 예외들만 구분을 해서 catch를 하지만 
// 외부 함수에서는 Exception으로 전달을 하게 되어 어떤 예외인지 알 수 없다.
public void function(String s) throws Exception{
    try{
        String utf8_name = URLEncoder.encode(s, "UTF-8");
        Photo photo = photoRepository.findByFileName(utf8_name);
        if(photo.getBytes().length){
            ...
        }
    } catch(UnsupportedEncodingException e){
        e.printStackTrace();
    } catch(NullPointerException e){
        e.printStackTrace();
    }
}

// Java 7 이후. 이제는 정확한 예외에 대해 보내줘서 외부 메소드도 
// 어떤 종류의 예외가 걸렸는지 자세하게 알 수 있게 되었다.
public void function(String s) throws UnsupportedEncodingException, NullPointerException{
    try{
        String utf8_name = URLEncoder.encode(s, "UTF-8");
        Photo photo = photoRepository.findByFileName(utf8_name);
        if(photo.getBytes().length){
            ...
        }
    } catch(UnsupportedEncodingException e){
        e.printStackTrace();
    } catch(NullPointerException e){
        e.printStackTrace();
    }
}
```

## _ in Numeric Literal

우리는 숫자를 구분할 때 천(千), 만(萬), 억(億), 조(兆) 등으로 구분하지만 서양 사람들은 숫자 별로 K(Kilo), M(Mega), G(Giga), T(Tera) 등으로 구분을 한다. 그래서 이를 쉽게 구분하기 위해 숫자에 쉼표를 쓰는 경우가 있는데 Java 7에서는 _를 이용해서 구분할 수 있는 기능을 제공한다.
```
int million = 1_000_000; // 백만
long ten_thousand = 10_000L; // 만. L은 long 변수임을 구분할 때 쓴다.
long credit_card = 1234_5678_9012_3456L; // _의 위치는 3단위에서 끊지 않아도 된다. 여러 단위에서 끊어 써도 문제 없다.
double pointDouble = 12.345_678; // 실수에서도 작성이 가능하다.
float pointFloat = 12.345_678f; // f는 float 변수임을 구분할 때 쓴다.
```

## Binary Literal[0b]
```
int decimal = 10; // 10진수로 10
int hexadecimal = 0x0a; // 16진수로 10
int octal = 012; // 8진수로 10
int binary = 0b1100 // 2진수로 10
```
정수형 변수에서 8진수, 16진수에 대해 적용하려면 각각 0, 0x를 붙어주면 된다. 또한 Java 7에서는 2진수에 대해 이용할 수 있게 0b 문자가 추가 되었다.

## Java NIO 2.0

`java.nio.file` 패키지에서 현재 운영체제에서 쓰고 있는 기본 파일 시스템에 접근도 가능하고, 다양한 파일 I/O 기능들도 제공한다. 기본 파일 시스템 중 예를 들어 복사하기, 붙어넣기, 자르기, 휴지통, 파일 속성이 숨김으로 되어 있는가 확인 등을 제공하고, 심볼릭 링크, 하드 링크 생성 가능, 와일드카드를 이용한 파일 검색 등등 윈도우 탐색기에서 할 수 있는 기능을 Java에서도 할 수 있도록 제공한다고 생각하면 된다.

## G1 Garbage Collector

G1 Garbage Collection 중 G1은 Garbage First(1st)의 뜻으로 Garbage가 가장 많은 영역의 청소를 수행하는데 Java Heap 메모리 영역을 여러 개로 분할된 영역으로 나뉜다. JDK 7 이전은 new, old, permgen 영역으로 나뉘었지만, 새로운 영역을 통해 메모리에 집중적으로 접근하는 어플리케이션(Memory-Oriented Application)에 더 큰 Throughput을 제공한다. 이 외에 큰 이슈는 없다. 추가적으로 JVM Garbage Collection에 대한 내용도 같은 폴더에서 다룰 예정이니 이를 더욱 참고하면 좋겠다.

## Fork/Join Framework

멀티 프로세서의 성능을 이용할 수 있는 ExecutorService 인터페이스의 구현체로 볼 수 있는데 다음과 같이 정리할 수 있다.
- 어플리케이션의 성능을 향상 시키기 위해 가능한 모든 프로세스를 이용하도록 반복적으로 작은 조각으로 작업을 나뉘어 수행할 수 있게 설계를 한다.
- ExecutorService를 구현함으로 Fork/Join Framework는 Thread Pool 안의 Worker Thread에 분배를 한다.
- Thread 패턴 중에서 Produce-Consumer 알고리즘과 다른 Work-Stealing 알고리즘을 이용하는데 작업이 없는 Worker Thread는 아직 바쁜 다른 Thread의 작업을 가져 올 수 있는 개념으로 인식할 수 있다.
- Fork/Join Framework의 핵심은 AbstractExecutorService 클래스를 구현한 ForkJoinPool 클래스로 Work-Stealing 알고리즘을 이용하고, ForkJoinTask 프로세스들을 실행할 수 있다.