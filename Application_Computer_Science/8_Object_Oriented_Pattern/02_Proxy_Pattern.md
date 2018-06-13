# Proxy Pattern Issues

![proxy_example](/Application_Computer_Science/8_Object_Oriented_Pattern/img/proxy_example.png)

> Proxy Pattern을 잘 못 오용하면 이렇게 될 수 있다.

이 장면은 짱구 1기 아빠 회사에 가자 에피소드에서 따온 일부 내용이다. 여기서 짱구가 아침 부부싸움 전에서 봉미선 씨와 신형만 씨가 중간에 가정 살림과 회사 업무로 인한 충돌로 인해 서로 불만 사항을 이야기 하고 있는데 중간에 짱구가 각자 단점을 끼어 들어 둘의 사이를 더욱 흐트러 놓았다.

이 만화 내용이 단순한 만화 내용이라 유치할 수 있지만, Proxy Pattern에서 쓰이는 Proxy(대리자)의 중요성을 알 수 있는 사례로 손꼽을 수 있다. 

이번 스터디 노트에서는 Proxy Pattern에 대해 어떤 구조로 되어 있는지 공부하는 시간을 가져보도록 하자.

## What Is Proxy Pattern?

![proxy_pattern_uml](/Application_Computer_Science/8_Object_Oriented_Pattern/img/proxy_pattern_uml.png)

Proxy Pattern은 소프트웨어 디자인 패턴 중에서 **구조 패턴(Structure Pattern)** 을 구성하는 방법 중 하나이다. Proxy의 영문 뜻은 대리의 의미를 가져서 네트워크 기술에서 프로토콜에 있어서 대리 응답 등에서 친숙한 개념이다.

방금 짱구가 봉미선 씨와 신형만 씨가 싸우고 있을 때 서로의 단점을 언급하면서 싸움을 부추기는 방향으로 진행하듯이 Proxy는 어느 프로세스 내부에서 다른 무언가와 이어지는 Interface의 역할을 하는 **인터페이스 격 클래스**로 볼 수 있다.(어지간히 Interface와는 다른 개념이다.) 

Proxy Pattern을 사용하는 사례는 다음과 같다.

> Authorization 처리(인가 처리 / 보호), AOP(Aspect Oriented Programming)의 구성 시발점, 생성 자원에 대한 Background 처리, 원격 메소드를 호출하기 위한 작업 etc.

Spring Framework를 공부했다면 Proxy Pattern에 친숙할 것이다. AOP를 기반으로 Transaction, Security, Cache 처리 등에 대하여 어노테이션으로 작성하여 구조를 더욱 보기 좋게 정리할 수 있다.

그리고 Android 어플리케이션 개발에서 인터페이스 정의 언어인 AIDL(Android Interface Definition Language)도 Proxy Pattern에 해당된다.

## Proxy Pattern Example

![Proxy_Pattern_Example](/Application_Computer_Science/8_Object_Oriented_Pattern/img/Proxy_Pattern_Example.png)

단일 문서 저장소를 사례로 작성하였다.

처음으로 Service Bean을 이용해야 하는데 Service에서 꼭 필요한 함수를 정리하는 Interface를 `Subject Interface`로 추상화를 시켜 의존하게 만든다.

그 다음으로 Service 인터페이스를 상속 시켜서 추상 메소드들에 대해 구현을 하는 ServiceImpl 클래스를 작성하는 것이 `Real Subject Class`를 작성하는 과정이다.

최종적으로 Web Application이 실행될 때 RestController의 요청 URI를 이용해서 데이터를 접근할 때 이 객체를 대리로 삼는다. 이후로 REST API에서 요청한 URL를 해석하여 데이터베이스에 있는 Domain Object를 토대로 JSON 객체로 반환하게 된다.

**Subject Interface 작성**

```
package net.kang.proxy.subject;

import net.kang.proxy.dto.Document;
import net.kang.proxy.enumeration.Suffix;

public interface StoredDocument {
    public void setDocument(String title, Suffix suffix, int bytes);
    public Document getDocument();
    public void readStoredDocument();
}

```

Subject Interface의 역할은 **Proxy 역할과 Real Subject 역할을 동일하게 행위들을** 결정한다. Client 측에서는 Real Subject 클래스 내부에서는 어떻게 돌아가는지에 대해서는 이해할 수 없다. 어차피 Subject Interface를 이용하면 끝이기 때문이다.

StoredDocument Interface의 행위는 문서 수정, 문서 정보 얻기, 문서 읽어 들이기를 한다. 이 행위는 Proxy Object와 Real Subject Object까지 영향이 끼친다.

**Real Subject Class 작성**

```
package net.kang.proxy.subject;

import net.kang.proxy.dto.Document;
import net.kang.proxy.enumeration.Suffix;

public class RealStoredDocument implements StoredDocument {
    private String id;
    private Document document = null;

    public RealStoredDocument(String id, String title, Suffix suffix, int bytes){
        this.id = id;
        System.out.printf("[%s - 단일 파일 저장소를 생성하였습니다.]\n", id);
        this.document = new Document(title, suffix, bytes);
        this.uploading();
    }

    private void uploading(){
        String tmpFileName = String.format("%s.%s", this.document.getTitle(), this.document.getSuffix().toString().toLowerCase());
        System.out.printf("[%s - 파일 업로드 정보] 단일 파일 저장소에 %s 업로딩이 시작됩니다.\n", this.id, tmpFileName);
        try {
            Thread.sleep(this.document.getBytes()); // 1MB를 1000Byte로 가정하고, 1MB 당 1초씩 업로딩 된다고 치자.
        } catch(InterruptedException e) {
            e.printStackTrace();
        }
        System.out.printf("[%s - 파일 업로드 정보] 단일 파일 저장소에 %s 업로딩이 완료되었습니다.\n", this.id, tmpFileName);
    }

    private void removing(){
        String tmpFileName = String.format("%s.%s", this.document.getTitle(), this.document.getSuffix().toString().toLowerCase());
        System.out.printf("[%s - 파일 삭제 정보] 단일 파일 저장소에 %s 삭제가 시작됩니다.\n", this.id, tmpFileName);
        try {
            Thread.sleep(this.document.getBytes() / 5); // 1MB를 1000Byte로 가정하고, 5MB 당 1초씩 삭제 된다고 치자.
        } catch(InterruptedException e) {
            e.printStackTrace();
        }
        System.out.printf("[%s - 파일 삭제 정보] 단일 파일 저장소에 %s 삭제가 완료되었습니다.\n", this.id, tmpFileName);
        this.document = null;
    }

    public void setId(String id){
        this.id = id;
    }

    public String getId(){
        return this.id;
    }

    @Override
    public void setDocument(String title, Suffix suffix, int bytes){
        this.removing();
        this.document = new Document(title, suffix, bytes);
        this.uploading();
    }

    @Override
    public Document getDocument(){
        return this.getDocument();
    }

    @Override
    public void readStoredDocument(){
        String tmpFileName = String.format("%s.%s", this.document.getTitle(), this.document.getSuffix().toString().toLowerCase());
        System.out.printf("[%s - 파일 열람] 단일 파일 저장소의 %s 정보는 다음과 같습니다.\n", this.id, tmpFileName);
        System.out.println(this.document);
    }

}
```

Real Subject 클래스는 실제 동작에 대해 구체적으로 적는 클래스이다. 

StoredDocument Interface에서 작성한 추상 메소드들을 여기에 확장하여 어떤 행위를 할 지를 적으면 된다.

Spring Framework에서는 내부에 Component Bean을 불러와서 적용이 가능하다. Service 객체에 대하여 ServiceImpl 클래스에 실제 동작들을 작성하여 사용하는 사례를 들 수 있다.

**Proxy Class 작성**

```
package net.kang.proxy.proxy;

import net.kang.proxy.dto.Document;
import net.kang.proxy.enumeration.Suffix;
import net.kang.proxy.subject.RealStoredDocument;
import net.kang.proxy.subject.StoredDocument;

public class ProxyStoredDocument implements StoredDocument {
    private RealStoredDocument realStoredDocument = null;
    private String id = null;
    private Document document = null;

    public ProxyStoredDocument(String id, String title, Suffix suffix, int bytes){
        this.id = id;
        this.document = new Document(title, suffix, bytes);
        System.out.printf("[임시 서버 저장소 %s를 생성하였습니다.]\n", this.id);
    }

    @Override
    public void setDocument(String title, Suffix suffix, int bytes){
        if(realStoredDocument == null){
            realStoredDocument = new RealStoredDocument(this.id, this.document.getTitle(), this.document.getSuffix(), this.document.getBytes());
        }
        realStoredDocument.setDocument(title, suffix, bytes);
    }

    @Override
    public Document getDocument(){
        if(realStoredDocument == null){
            realStoredDocument = new RealStoredDocument(this.id, this.document.getTitle(), this.document.getSuffix(), this.document.getBytes());
        }
        return realStoredDocument.getDocument();
    }

    @Override
    public void readStoredDocument(){
        if(realStoredDocument == null){
            realStoredDocument = new RealStoredDocument(this.id, this.document.getTitle(), this.document.getSuffix(), this.document.getBytes());
        }
        realStoredDocument.readStoredDocument();
    }
}
```

Proxy Object로 구현된 ProxyStoredDocument 객체는 RealStoredDocument 객체가 하는 행위를 대신 처리하는 역할을 한다. 이 객체를 이용할 수 있는 사례는 단일 문서 관리자가 호환이 되지 않는 환경(예를 들어 맥 환경에서 작성한 문서를 윈도우 환경에서 못 할 때 등.) 속에서 이로 대체하여 RealStoredDocument를 사용할 때이다.

Spring 프로젝트에서 Controller 클래스와 유사한데, Real Subject 클래스에서 구현한 메소드를 클라이언트로 들어오는 URI를 통하여 적절한 행동을 취하게 만든다. 여기서는 요청 URI 별로 취하는 Method를 불러오는 사례로 볼 수 있다. 그렇지만 이 내부에서는 비용이 많은 객체 생성, 보안 관리, 리모트 콜 등의 작업이 추가로 늘어나게 된다.

**Client Class 정의**

```
package net.kang.proxy.client;

import net.kang.proxy.enumeration.Suffix;
import net.kang.proxy.proxy.ProxyStoredDocument;

public class MainClient {
    public static void main(String[] args){
        ProxyStoredDocument storedDocument1 = new ProxyStoredDocument("X01", "보고서", Suffix.DOCX, 3000);
        storedDocument1.setDocument("멘토링_보고서", Suffix.DOCX, 3000);
        storedDocument1.readStoredDocument();
        System.out.println();

        ProxyStoredDocument storedDocument2 = new ProxyStoredDocument("X02", "업무_프로세스", Suffix.XLSX, 2000);
        storedDocument2.readStoredDocument();
        System.out.println();

        ProxyStoredDocument storedDocument3 = new ProxyStoredDocument("X03", "대중서사_n조_발표자료", Suffix.PPTX, 4000);
        storedDocument3.setDocument("대중서사_3조_발표자료", Suffix.PPTX, 4000);
        storedDocument3.readStoredDocument();
    }
}
```

클라이언트 측에서 ProxyStoredDocument 객체로 단일 문서 저장소 역할을 위임하여 사용하는 예제이다. 

여기서는 Real Subject가 취해야 할 행동에 대해서 불러온다면 눈으로만 보여주는 역할이 끝이지, 실제로 내부에서 어떻게 동작하는지에 대해서는 소스 코드를 까보지 않는 이상 모른다.

실행 결과는 다음과 같다.

```
[임시 서버 저장소 X01를 생성하였습니다.]
[X01 - 단일 파일 저장소를 생성하였습니다.]
[X01 - 파일 업로드 정보] 단일 파일 저장소에 보고서.docx 업로딩이 시작됩니다.
[X01 - 파일 업로드 정보] 단일 파일 저장소에 보고서.docx 업로딩이 완료되었습니다.
[X01 - 파일 삭제 정보] 단일 파일 저장소에 보고서.docx 삭제가 시작됩니다.
[X01 - 파일 삭제 정보] 단일 파일 저장소에 보고서.docx 삭제가 완료되었습니다.
[X01 - 파일 업로드 정보] 단일 파일 저장소에 멘토링_보고서.docx 업로딩이 시작됩니다.
[X01 - 파일 업로드 정보] 단일 파일 저장소에 멘토링_보고서.docx 업로딩이 완료되었습니다.
[X01 - 파일 열람] 단일 파일 저장소의 멘토링_보고서.docx 정보는 다음과 같습니다.
- 파일 정보를 읽어 들입니다. -
- 파일 이름 : 멘토링_보고서.docx
- 파일 용량 : 3000 Bytes

[임시 서버 저장소 X02를 생성하였습니다.]
[X02 - 단일 파일 저장소를 생성하였습니다.]
[X02 - 파일 업로드 정보] 단일 파일 저장소에 업무_프로세스.xlsx 업로딩이 시작됩니다.
[X02 - 파일 업로드 정보] 단일 파일 저장소에 업무_프로세스.xlsx 업로딩이 완료되었습니다.
[X02 - 파일 열람] 단일 파일 저장소의 업무_프로세스.xlsx 정보는 다음과 같습니다.
- 파일 정보를 읽어 들입니다. -
- 파일 이름 : 업무_프로세스.xlsx
- 파일 용량 : 2000 Bytes

[임시 서버 저장소 X03를 생성하였습니다.]
[X03 - 단일 파일 저장소를 생성하였습니다.]
[X03 - 파일 업로드 정보] 단일 파일 저장소에 대중서사_n조_발표자료.pptx 업로딩이 시작됩니다.
[X03 - 파일 업로드 정보] 단일 파일 저장소에 대중서사_n조_발표자료.pptx 업로딩이 완료되었습니다.
[X03 - 파일 삭제 정보] 단일 파일 저장소에 대중서사_n조_발표자료.pptx 삭제가 시작됩니다.
[X03 - 파일 삭제 정보] 단일 파일 저장소에 대중서사_n조_발표자료.pptx 삭제가 완료되었습니다.
[X03 - 파일 업로드 정보] 단일 파일 저장소에 대중서사_3조_발표자료.pptx 업로딩이 시작됩니다.
[X03 - 파일 업로드 정보] 단일 파일 저장소에 대중서사_3조_발표자료.pptx 업로딩이 완료되었습니다.
[X03 - 파일 열람] 단일 파일 저장소의 대중서사_3조_발표자료.pptx 정보는 다음과 같습니다.
- 파일 정보를 읽어 들입니다. -
- 파일 이름 : 대중서사_3조_발표자료.pptx
- 파일 용량 : 4000 Bytes
```

Spring 프로젝트에서 Application Main 클래스에는 Domain, Repository, Service, Controller 등의 Component Bean은 이미 작성이 되어 있다. 우리가 Web Application을 가동할 때 RestController에 작성한 요청 URI를 분석하고 난 후에 이에 맞춘 데이터 결과가 나온다.

## Expansion of Proxy Pattern

**Virtual Proxy**

객체 생성에 많은 비용이 발생한다면 실제 사용할 때 객체가 생성되도록 대리자를 두어 접근하도록 제어하는 방식이다. 예제에서 다룬 Proxy Pattern는 이것을 접목시켰다.

**Remove Proxy**

원격 객체에 대한 로컬의 대리자 역할을 한다. Java에서도 소켓 통신을 통해 원격 객체에 데이터를 얻거나 메소드를 호출할 경우 대리자를 통해 호출이 이뤄진다.

**Protection Proxy**

접근 제어가 필요한 객체에 대한 접근을 통제하고 Proxy 객체를 통해 접근 가능한 정도만 노출할 때 사용된다. 말 그대로 Proxy 객체는 일부 내용만 접근이 가능한데, Spring Security에서 Role에 따른 권한 분배에서 쓰이는 사례로 볼 수 있다.

**Smart Reference**

실제 객체에 접근할 때 부가적인 기능을 수행하고자 할 때 사용한다.

## Comparison of Another Patterns

다음에 이어서 다뤄볼 Adapter Pattern과 Decorator Pattern과는 어떤 점과 비교가 되는지 잠깐 익혀보는 기회를 가져보도록 하자.

- Adapter Pattern(어댑터 패턴)
    - Proxy Pattern에서는 실제 오브젝트와 동일한 인터페이스를 제공하였지만, Adpater Pattern은 실제 오브젝트와 전혀 다른 인터페이스를 제공한다.
    - 어떻게 본다면 수도권 교통 운임 체계와 비슷한 개념이다. 시내버스 가격은 지역 별로 다른데, 환승 체계는 그대로이다. 이에 대해서는 Adapter Pattern에서 계속 언급하도록 하자.

- Decorator Pattern(데코레이터 패턴)
    - Proxy 패턴은 동작을 제어하지 않고 동작을 변경하지 않는다.
    - 데코레이터 패턴은 런타임에 맞춰서 실제 객체에 동작을 추가한다.

## References
- https://ko.wikipedia.org/wiki/%ED%94%84%EB%A1%9D%EC%8B%9C_%ED%8C%A8%ED%84%B4 - Proxy Pattern 위키 백과 참조. 한글보다 영어를 참고하면 더욱 도움된다.
- https://m.blog.naver.com/gracefulife/220650929598 - 예제가 나와있지만, 본인은 Spring Web MVC에서 적용하여 재구성하였다.
- https://blog.seotory.com/post/2017/09/java-proxy-pattern - 더욱 참고하기 좋은 예제로 올리겠음.
- https://www.oodesign.com/proxy-pattern.html - Proxy Pattern에 대해 원문으로 작성된 내용.

## Post Script
- 여기에 작성된 내용 이외에도 필요한 개념들을 발견하게 된다면 언제든지 갱신될 수 있습니다.
- 이 강의노트 개념에서 더욱 다뤄주면 좋은 개념들이나 오탈자가 있으시면 KangBakSa Issues에 올려주세요.
- 그리고 객체 지향 패턴은 Design_Pattern Repository에 추가로 저장하고 있습니다. 나중에 완성되면, public으로 바꿔 소스 코드와 연동하겠습니다. 