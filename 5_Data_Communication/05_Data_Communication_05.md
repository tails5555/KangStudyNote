# 데이터 통신 정리5[LAN, CSMA/CD, VAN, B-ISDN, 인터넷과 TCP/IP]

## LAN(근거리 통신망)
> 통신망 계의 가정식 노래방 기계
- 광대역 통신망이 아니라 교내, 사내, 도서관 등의 건물이나 일정 지역에 컴퓨터나 단말기들을 **고속 전송 회선**으로 연결하여 공유할 수 있도록 한 네트워크 형태. 제한 지역 내의 통신인 건 당연하다.
- 광대역 전송 매체의 사용으로 고속 통신이 가능하다.
- 경로 선택이 필요 없고 오류 발생률이 낮음.
- 전송 매체로 꼬임선, 동축 케이블, 광섬유 케이블 등을 사용한다. 뒤로 갈 수록 속도가 향상된다.
- 망의 구성은 버스형, 성(Star, 星)형, 링형, 계층(Tree)형 등으로 분류된다.
- LAN의 계층 구조는 **물리 계층과 데이터 링크 계층** 두가지로 나눈다.
    - 물리 계층에서는 OSI의 1단계 물리 계층과 다를 바 없다.
    - 상위는 논리 링크 제어(LLC), 하위는 매체 접근 제어(MAC) 계층.
    - 매체 접근 제어(MAC) 방식에는 CSMA/CD, Token Bus, Token Ring으로 나뉘게 된다.
- IEEE 802의 주요 표준 규격
> - 802.1 : 전체 구성[연상 : 모든 구성을 한 번에 쉽고 편하게]
> - 802.2 : 논리 링크 제어 계층[연상 : 논ㄹ이(2)]
> - 802.3 : CSMA/CD 방식[연상 : Three -> Chree]
> - 802.4 : 토큰 버스 방식[연상 : 토큰 버스. 4글자]
> - 802.5 : 토큰 링 방식[연상 : 5가 거울 보면 ㄹ자로 보이게 됨을 연상]
> - 802.6 : 도시형 통신망(MAN)[연상 : 1990년대까지만 해도 우리나라 광역시는 서울, 부산, 인천, 울산, 대구, 대전 6개.]
> - 802.11 : 무선 LAN[연상 : 휴대폰이나 탭을 구매할 때 와이파이 관련 통신에서 802.11를 본 적이 있을 거다. 바로 그거다.]

## CSMA / CD 방식
> 통신망 계의 코인 노래방
- 채널 사용권을 서로 경쟁하여 확보하는 방식으로 Node 간 충돌은 허용하는 접근 방식.
- 통신 회선이 사용 중이라면 일정 시간동안 대기하고 통신 회선 상에 데이터가 없을 때 데이터를 송신하고 송신 중에도 전송로의 상태 계속 감시.
- 전송 중 충돌이 감지되면 패킷 전송을 즉시 중단하고 충돌 발생 사실을 모든 국(Station)이 알 수 있도록 간단한 통보를 함.
- 노드 장애가 시스템 전체에 영향은 주진 않는다.
- 버스형, 트리형으로 LAN에 가장 일반적으로 많이 씀.
- 타 매체 접근 제어 방식에 비해 알고리즘이 간단함.
- 전송량이 적을 때는 매우 효율적이고 신뢰성 향상.
- 전송량이 많아지면 충돌이 잦아져서 채널 이용률이 줄어들고 전송 지연 시간 급격히 증가.
- 충돌 발생 시 다른 노드에서 데이터를 전송할 수 없으며 지연 시간을 예측할 수 없다.
- 충돌과 채널 경쟁을 위해서 Non-Persistent, 1-Persistent, P-Persistent 3가지로 나뉘어 1-Persistent 기법을 주로 쓴다.
- CSMA/CD 방식 이용하는 LAN을 Ethernet(이더넷. 인터넷과는 다른 개념.)이라고 칭한다.
> - CS(Carrier Sence) : 통신 회선이 사용 중인지를 점검.
> - MA(Multiple Access) : 통신 회선이 비어 있으면 누구든 이용 가능.
> - CD(Collision Detention) : 데이터 프레임 전송을 하면서 충돌 여부 조사.

## VAN(부가 가치 통신망)
> 통신망 계의 시간제 노래방
- 공중 통신 사업자(S?, K?, L? 등)로부터 통신 회선을 임대하여 하나 사설망을 구축하고 정보 축적, 가공, 변환 처리 등 부가 가치를 첨가하고 불특정 다수를 대상으로 제공하는 통신망.
- 계층 구조로는 정보 처리 계층, 통신 처리 계층, 네트워크 계층, 기본 통신 계층 4가지로 나뉜다.
- 기능은 간단하게 보면 전송 기능, 교환 기능, 통신 처리 기능, 정보 처리 기능 4가지로 나뉜다.
- 통신 처리 기능은 축적 교환 기능과 변환 기능 2가지로 나뉠 수 있다.
> - 축적 교환 기능 : 전자 사서함, 데이터 교환, 동보 통신 etc.
> - 변환 기능 : 속도 변환, 프로토콜 변환, 코드 변환, 데이터 형식 변환, 미디어 변환 etc.

## B-ISDN(광대역 종합 정보 통신망)
> 통신망 계의 노래빠
- 광대역 전송 방식과 광대역 교환 방식을 통해 데이터, 음성, 영상 등 다양한 형태의 통신 서비스를 제공하는 광대역 ISDN이다.
- 1.5~2Mbps 이상의 고속 전송과 고속 교환 기술이 가능함.
- 전송 방식은 ATM(Asynchronous Transfer Mode)를 이용한다.
- ATM에서 사용하는 셀은 53Byte(5Byte(오텟) : 헤더 정보, 48Byte(오텟) : 사용자 정보)의 작은 크기로 고정 길이다.
- B-ISDN의 참조 계층
> - 물리 계층 : ATM 셀 전송
> - ATM 계층 : 가입자 정보 유형에 따라 셀 헤더 생성 및 추출, 가입자 채널 다중화
> - ATM 적응 계층 : 가입자 정보 유형에 따라 적절한 타입의 패킷 메시지 제공.

* * * 
## 인터넷의 주소 체계
- IP 주소 : 인터넷에 연결된 모든 컴퓨터 자원을 구분하기 위한 고유 주소. 숫자로 8비트(0 to 255) * 4 => 32비트(4Byte)로 구성됨. 참고로 IP는 네트워크 계층(OSI 계층 3단계)을 담당한다. 클래스는 5단계로 나뉜다.
> - Class A : 국가, 대형 통신망.
> - Class B : 중대형 통신망.
> - Class C : 소규모 통신망.
> - Class D : 멀티캐스트 용.
> - Class E : 실험용으로 공용되지 않음.
- 서브넷 마스크 : 4바이트의 IP 주소 중 네트워크 주소와 호스트 주소를 구분하기 위한 비트
- IPv6 : IPv4의 주소 부족 문제를 해결하기 위해 개발된 것으로 16비트(0~65535) 씩 8개 총 128비트로 구성되어 각 부분을 16진수로 표현하고 콜론으로 구분함.

> ### IPv4를 IPv6으로 전환하는 전략(중요)
> - 듀얼 스택(Dual Stack) : 호스트에서 IPv4와 IPv6을 모두 처리할 수 있도록 각각 스택을 두었음.
> - 터널링(Turnelin') : IPv6 망에서 인접한 IPv4 망을 거쳐 다른 IPv6 망으로 통신할 때 IPv4망에 터널을 만들어 IPv6 패킷이 통과할 수 있도록 하는 것으로 들어갈 땐 캡슐화지만 나갈 땐 역캡슐화이다.
> - IPv4/IPv6 변환 중 Header Translation : IP 계층(네트워크 계층)에서 IPv6 패킷 헤더를 IPv4 패킷 해더나 그 반대로 변환하는 방식.
> - 전송 계층 릴레이(Relay) 방식 : 전송 계층(OSI 계층 4단계)에서 IPv6 패킷 헤더를 IPv4 패킷 헤더나 그 반대로 변환하는 방식.
> - 응용 계층 릴레이 방식 : 응용 계층(OSI 계층 7단계)에서 IPv6 패킷 헤더를 IPv4 패킷 헤더나 그 반대로 변환하는 방식. 