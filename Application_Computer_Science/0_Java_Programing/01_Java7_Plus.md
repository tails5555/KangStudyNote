# Java 7에서 알아두면 좋은 개념들
우리가 Java Programing을 계속 진행하면서 각 버전 별로 어떤 개념들이 추가되어 오는가를 공부할 필요가 있다. 그래서 Java 7, 8, 9 버전 별로 개선이 되고 있는 개념들을 공부할 기회를 가져보도록 하자.

## Java 7 SE Release Information
생각보다 오래 됐는데 2011년 7월 7일에 처음으로 공개되었다.

이 버전이 발표된 직후에 Oracle이 Java를 인수하였고, Oracle 측과 Google 측에서 java.util 패키지에 대한 저작권 문제가 있었던 시기이기도 하다. 

## Type Interface
아주 간단한 예로 Map에 Key 값을 Integer 형 객체로, Value 값을 List 객체를 통해 저장한다고 가정하자.

Java 7 버전 이전에는 아래와 같이 작성을 해야 했었다.

```
Map<Integer, List<String>> map = new HashMap<Integer, List<String>>();
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
Java 7 이전에는 int 형 데이터, char 형 데이터 등만 들어올 수 있었는데 이 버전 이후로 갱신이 되어 switch 문에서도 String을 적용할 수 있게 되었다. switch 문에서 String을 활용할 때 Enumeration, final 상수 등을 이용하면 정확한 String 값을 적용하는데 도움을 준다. 

## Try-With-Resource
예를 들어 로컬 디스크 C에 있는 example 폴더의 test.txt에 있는 내용들을 읽어서 출력하는 사례로 설명을 하겠다. 파일 입-출력에 필요한 객체(InputStream, OutputStream etc.)에 대해 작업 완료 혹은 불시의 문제에 따라 입-출력 간의 종단(finally 구문에서 close() 메소드 이용)을 해 줄 필요가 있다. Java 7 이전으로 작성한다면 아래와 같다.

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
## Exception in |

## Optional
