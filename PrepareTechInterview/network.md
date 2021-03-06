# 네트워크



---



## TCP/IP

두 가지 뜻이 있다.

1. Network Layer의 IP와 Transport Layer의 TCP/UDP를 합친 용어로, 현재 인터넷 통신을 구성하는 표준 통신 모델.

   Application Layer는 사용자의 사용 용도에 따라 HTTP, SSH, FTP, SMTP 등 다른 프로토콜이 사용되고, Data Link Layer 및 Physical Layer는 전송 장비에 따라 Ethernet, Wi-Fi 등 다른 프로토콜이 사용되지만, Transport Layer 및 Network Layer는 이 TCP/IP로 고정되어 있다.

2.  TCP를 기반으로 통신함으로써 IP 프로토콜의 비연결성과 비신뢰성을 극복하고자 한 통신 방식이 TCP/IP, UDP 기반으로 통신함으로써 IP 프로토콜의 비연결성과 비신뢰성을 그대로 가져가고자 한 것이 UDP/IP



### TCP/IP 5계층

데이터 통신 역사 초기에는 여러 정보통신 업체들의 장비가 서로 호환이 되지 않아서, ISO(국제표준화기구)가 데이터 통신 규격을 통일하기 위해 OSI 7계층을 제시하였다. 그런데 현재는 그 뒤에 만들어진 TCP/IP 5계층이 세계 표준으로 받아들여지고 있다.

계층을 나눈 이유는 데이터 통신 과정을 단계별로 쉽게 파악할 수 있고,  각 계층이 독립적이기 때문에 어느 한 계층이 문제가 되었을 때 전체 시스템을 고칠 필요 없이 그 계층에 대해서만 문제를 해결하면 되기 때문이다.

| Layer Name  |      Equipment       | Addressing | Data Unit |       Protocol       |
| :---------: | :------------------: | :--------: | :-------: | :------------------: |
| Application |          -           |     -      |  message  | HTTP, SSH, FTP, SMTP |
|  Transport  |          -           | Port 번호  |  segment  |       TCP, UDP       |
|   Network   |        router        |  IP 주소   |  packet   |          IP          |
|  Data Link  |    switch, bridge    |  MAC 주소  |   frame   |   Ethernet, Wi-Fi    |
|  Physical   | repeater, cable, hub |     -      |    bit    |          -           |



#### Physical Layer

기기들이 연결되기 위한 물리적인 사항(전선, 전압)을 정하고, 케이블(ex: 랜선) 등의 물리적 연결을 통해 전기신호를 주고 받는다.

장비는 리피터, 케이블, 허브 등이고, 데이터의 단위는 bit이다.



#### Data Link Layer

물리적으로 연결되어 인접한 두 장치 간에 전기 신호를 해석해서 충돌 없이 데이터를 주고 받기 위한 방법을 결정한다.

장비는 Switch 또는 Bridge이고, 데이터의 단위는 frame이며, 물리적 주소인 MAC 주소를 통해 통신한다.

서로 다른 컴퓨터들이 물리적으로 연결되어 네트워크를 형성하며, 각 컴퓨터들은 MAC 주소를 통해 구분된다.



#### Network Layer

라우팅을 한다. 즉, 데이터 전송을 위한 최적의 전달 경로를 결정한다.

장비는 Router이고, 데이터의 단위는 Packet(datagram)이며, 논리적 주소인 IP 주소를 통해 통신한다.

서로 다른 네트워크들이 라우터를 통해 연결되어 인터넷을 형성하며, 각 네트워크 및 컴퓨터들은 IP 주소를 통해 구분된다.



#### Transport Layer

전송받은 데이터를 어떤 프로그램에 어떻게 전달할지를 결정한다. 즉, 해당되는 Port 번호를 점유하는 프로그램에 데이터를 전달한다.

TCP 또는 UDP 프로토콜을 통해 통신을 활성화한다.

데이터의 단위는 Segment이며, Port 번호를 통해 통신한다.



#### Application Layer

최종 사용자 간의 논리적인 연결을 나타낸다.



### 프로토콜 (Protocol)

네트워크 통신 규약. 즉, 프로토콜의 가장 큰 역할은 TCP/IP의 각 계층을 지나갈 때마다 데이터를 캡슐화(위 -> 아래)하거나 캡슐 해제화(아래 -> 위)를 수행하는 것이다.

그 외 프로토콜의 주요 기능으로는 주소 설정, 순서 제어(연결 지향형에만 사용), 데이터 분할 및 재조합, 연결 제어, 흐름 제어 등이 있다.

![image](https://user-images.githubusercontent.com/53200166/158025185-8cdaad0b-3834-4728-b0db-56fdd3f88758.png)

- Network Layer -> Data Link Layer : packet을 frame으로 encapsulation 

- Data Link Layer -> Network Layer : frame을 packet로 decapsulation



#### ARP (Address Resolution Protocol)

L3(Network Layer)와 L2(Data Link Layer)의 사이에 위치하여, IP 주소를 MAC 주소로 대응시키기 위해 사용한다.



---



## LAN vs WAN

![image](https://user-images.githubusercontent.com/53200166/158024200-f4a38768-d3e5-4262-b69d-26fd25e14f5e.png)

|               | LAN (Local Area Network)    | WAN (Wide Area Network) |
| ------------- | --------------------------- | ----------------------- |
| 뜻            | 근거리 통신망               | 광역 통신망             |
| 범위          | 가정, 사무실, 회사, 학교 등 | 도시, 주, 국가          |
| 장비          | Switch                      | Router                  |
| 통신 프로토콜 | Ethernet                    | IP                      |



---



## 네트워크 전송 방식



### Packet Switching

데이터를 패킷이라는 일정한 단위로 잘라서 전송하는 방식

송신측에서 전송한 패킷을 Router에 저장시켰다가 이를 다시 적절한 통신 경로를 선택하여 수신측에 전달한다.



### Circuit Switching

물리적인 회선으로 연결하여 접속하는 방식

일단 접속이 이루어지면 전용선처럼 사용할 수 있고, 다른 사람이 끼어들지 못한다.



---



## TCP

일반적으로 TCP와 IP를 함께 사용하는데, IP가 데이터의 배달을 처리한다면 TCP는 패킷을 추적 및 관리한다.

연결형 서비스로, 높은 신뢰성을 보장한다.  연결형이란, 송신자와 수신자 사이의 연결 통로를 확실하게 수립하고 데이터를 전송하며, 데이터의 송수신이 정상적으로 수행되었는지 확인하는 것을 의미한다. 즉, 그저 패킷을 목적지를 향해 출발시키는 것에만 중점을 둔 IP의 비연결성 및 비신뢰성을 보완하고자 생겨난 프로콜이다.

TCP는 연결 지향 방식이기 때문에 데이터 교환 전후로 연결 설정 및 해제 과정을 거치는데, 이를 handshake라 한다. 양쪽 모두 데이터를 전송할 준비가 되었다는 것을 보장하고, 실제로 데이터 전달이 시작하기 전에 한 쪽이 다른 쪽이 준비되었다는 것을 알 수 있도록 한다.



### 연결 및 해제 방법

#### 3-way handshake

연결 설정 및 데이터 송수신 과정

![image](https://user-images.githubusercontent.com/53200166/157698973-1b5c095a-5703-4553-a9d6-e065f070cd94.png)

![image](https://user-images.githubusercontent.com/53200166/157699008-9e051525-d4ff-4926-aa16-05ffa81e7a61.png)



#### 4-way handshake

연결 해제 과정

![image](https://user-images.githubusercontent.com/53200166/157697859-a6f8ba2f-68fb-43ba-a219-32d65f5f2003.png)



### 흐름 제어 (Flow Control)

송신 측이 수신 측보다 데이터 처리 속도가 더 빠를 경우 데이터 유실 등의 문제가 생기므로 송신 측의 데이터 전송량을 수신측에 따라 조절하는 것



#### Stop and Wait

매번 전송한 패킷에 대해 확인 응답을 받아야만 그 다음 패킷을 전송하는 방법

![image](https://user-images.githubusercontent.com/53200166/157811629-9fef4df8-28a4-485f-bdc2-7c479a2f5caa.png)



#### Sliding Window

수신측에서 설정한 윈도우(버퍼) 크기 N만큼 송신측에서 확인응답(ACK) 없이도 세그먼트를 전송할 수 있게 하여 데이터 흐름을 동적으로 조절하는 방법

수신측으로부터 ACK를 받게 되면, 윈도우를 옆으로 옮긴 다음 윈도우의 크기를 다시 N으로 늘려 다음 전송을 수행한다.

만약 데이터를 계속 보내다가 결국 ACK를 받지 못하고 윈도우 크기의 한계까지 다다르면 (윈도우의 크기가 0이 되면), 송신자는 전송을 멈추고 대기한다.

![image](https://user-images.githubusercontent.com/53200166/157813252-ff9e46d6-9f7d-4c42-a2d4-7abf30063316.png)

![image](https://user-images.githubusercontent.com/53200166/157826963-57b8f6b0-6077-4f31-93d6-bb745303df09.png)



### 혼잡 제어 (Congestion Control)

송신 측의 데이터 전송 속도 네트워크의 데이터 처리 속도보다 더 빠를 경우 네트워크 혼잡의 문제가 생기므로 송신 측의 데이터 전송량을 네트워크 상태에 따라 조절하는 것

송신측의 데이터 전달과 네트워크의 처리속도 차이를 해결하기 위한 기법

만약 한 라우터에게 데이터가 몰릴 경우(혼잡할 경우), 라우터는 자신에게 온 데이터를 모두 처리할 수 없다. 그렇게 되면 호스트들은 또 다시 재전송을 하게 되고 결국 혼잡을 가중시켜 오버플로우나 데이터 손실을 발생시킨다. 따라서, 이러한 네트워크 혼잡을 피하기 위해 송신측에서는 데이터 전송 속도를 강제로 줄여야 한다.



![image](https://user-images.githubusercontent.com/53200166/157878108-9de621f0-e036-4996-9818-1fc15de4a8fb.png)



#### AIMD (Additive Increase / Multiplicative Decrease)

패킷을 하나씩 보내고 이것이 문제없이 도착하면 Window 크기(단위 시간 내에 보내는 패킷의 수)를 1씩 증가시켜가며 전송한다. 네트워크 혼잡이 감지되면 (즉, 패킷 전송에 실패하거나 일정 시간을 넘으면(Timeout)) Window 크기를 절반으로 줄인다.

윈도우 크기를 선형적으로 증가시키기 때문에, 제대로 된 속도가 나오기까지 시간이 조금 걸리는 단점이 있다.



#### Slow Start

패킷을 하나씩 보내고 이것이 문제없이 도착하면 Window 크기를 2배씩(지수적으로) 증가시켜가며 전송한다. 네트워크 혼잡이 감지되면 Window 크기를 1로 줄인다.

윈도우 크기를 지수적으로 증가시키기 때문에, 윈도우 크기가 기하급수적으로 증가하여 네트워크 혼잡의 위험성이 커진다.



#### Congestion Avoidance (혼잡 회피)

Slow Start 도중 Window가 어떠한 정해놓은 임계값에 도달하면 그 이후부터는 선형적으로 Window 크기를 1씩 증가시킨다. 네트워크 혼잡이 감지되면 Window 크기를 1로 줄이고, 임계값을 해당 시점의 윈도우 크기의 절반으로 줄인다.



#### Fast Retransmit (빠른 재전송)

![image](https://user-images.githubusercontent.com/53200166/158019606-367aecb7-d3ed-406f-ad29-d0682165e5d0.png)

![image](https://user-images.githubusercontent.com/53200166/158019429-bda04698-7c78-4a08-88d0-95424429dd64.png)



송신측에서 바로바로 여러 개의 패킷을 전송할 경우 패킷 N이 손실되면, 손실된 패킷 N 이후에 보내진 패킷들에 대해서 수신측이 송신측에게 응답하는 ACK는, 손실되기 직전 가장 최근에 정상적으로 보내진 패킷 N-1에 대한 ACK와 동일한 ACK로 인식된다. 그렇게 송신측이 수신측으로부터 중복된 ACK N-1을 3번 받게 되면, 송신측은 패킷 N이 손실되었다고 판단하여 패킷 N을 재전송한다.

이러한 현상이 생겼다는 것은 네트워크 혼잡이 감지되었다는 뜻이므로 윈도우 크기를 1로 줄이고, 임계값을 해당 시점의 윈도우 크기의 절반으로 줄인다.



#### Fast Recovery (빠른 회복)

송신측이 수신측으로부터 3개의 중복 ACK를 받게 되면 재전송을 한다는 개념은 Fast Retransmit과 유사하지만, 윈도우의 크기를 1이 아닌 절반으로 줄인다는 차이점이 있다.



#### TCP Tahoe

- `Slow Start` + `AIMD` + `Fast Retransmit`
- Timeout 시 임계값을 해당 시점 윈도우 크기의 절반으로, 윈도우 크기는 1로

![image](https://user-images.githubusercontent.com/53200166/158020966-43c70c2a-581d-4c33-8ad6-65cf359bd4e0.png)



#### TCP Reno

- `Slow Start` + `AIMD` + `Fast Recovery`
- Timeout 시 임계값은 변경하지 않고, 윈도우 크기는 1로

![image](https://user-images.githubusercontent.com/53200166/158020974-cb0899fb-028e-49dc-913b-574f37f9c032.png)



---



## TCP vs UDP

TCP는 HTTP, 이메일, 파일 전송 등 <u>신뢰성 및 순서</u>가 중요한 통신에 사용되고,
UDP는 동영상 스트리밍, DNS 등 <u>연속성 및 속도</u>가 중요한 통신에 주로 사용된다.

![image](https://user-images.githubusercontent.com/53200166/157685759-91f44a96-7314-401c-94c6-85beea5701bb.png)

|                            |      TCP       |       UDP        |
| :------------------------: | :------------: | :--------------: |
|         연결 방식          | 연결형, 양방향 | 비연결형, 단방향 |
|         전송 속도          |      느림      |       빠름       |
|           신뢰성           |       O        |        X         |
| 흐름제어, 혼잡제어, 재전송 |       O        |        X         |
|       전송 순서 보장       |       O        |        X         |
|       수신 여부 확인       |       O        |        X         |
|         통신 방식          |      1:1       |  1:1, 1:N, N:M   |



---



## HTTP vs HTTPS



### HTTP (HyperText Transfer Protocol)

인터넷 상에서 클라이언트와 서버 간에 요청/응답으로 데이터를 주고 받을 때 사용하는 통신 프로토콜로, TCP 또는 UDP 위에서 동작한다.

클라이언트는 주로 웹브라우저일테지만, 모바일 앱, 터미널 프로그램 등도 클라이언트일 수 있다.



#### HTTP의 두 가지 특징

##### 비연결성 (Connectionless)

주로 연결 상태를 유지하지 않는 비연결성 프로토콜이기 때문에 요청/응답 방식으로 동작한다. 클라이언트의 요청을 서버가 응답하자마자 바로 연결이 끊긴다.

##### 무상태 (Stateless)

서버는 클라이언트의 상태를 보존하지 않기 때문에 쿠키와 세션을 사용한다.

|          |                        Cookie                        |     Session      |
| :------: | :--------------------------------------------------: | :--------------: |
| 저장위치 |                        Client                        |      Server      |
| 저장형식 |                         Text                         |      Object      |
| 만료시점 | 쿠키 저장시 설정<br />(설정 없으면 브라우저 종료 시) | 정확한 시점 모름 |
|  리소스  |                 클라이언트의 리소스                  |  서버의 리소스   |
| 용량제한 |                          O                           |        X         |



#### 상태 코드 (Status Code)

- 1xx(조건부 응답) : 요청을 받았으며 프로세스를 계속 진행함
- 2xx(성공) : 요청을 성공적으로 수신 -> 승낙 -> 처리하였음
- 3xx(리다이렉션) : 요청 완료를 위해 클라이언트의 추가 작업 조치가 필요
- 4xx(클라이언트 오류) : 요청의 문법이 잘못되었거나 요청을 처리할 수 없음
- 5xx(서버 오류) : 유효한 요청을 서버가 명백하게 수행하지 못했음



#### HTTP의 역사

- HTTP/0.9 : GET 메서드만 지원, HTTP 헤더 없음
- HTTP/1.0 : POST 메서드, 헤더 추가(⇒ HTML 이외에도 JSON, XML 등 다른 파일 전송 가능)
- HTTP/1.1 : DELETE 및 PUT 메서드 추가, 현재 가장 많이 사용하는 버전
- HTTP/2 : 텍스트가 아닌 바이너리 처리를 통한 성능 개선
- HTTP/3 : 진행중, TCP 대신에 UDP 사용, 성능 개선



### HTTPS (HTTPS + SSL)

HTTP가 일반 텍스트를 그대로 송수신하는데 반해, HTTPS는 TLS/SSL 프로토콜을 통해 데이터를 암호화하여 송수신한다. 따라서 중간에 제3자가 정보를 가로채는 것을 차단하여 안전하다는 장점이 있고, 암호화 및 인증 과정에 시간이 걸리기 때문에 속도가 느리다는 단점(컴퓨터의 발전으로 미미해졌다)이 있다.

HTTP는 TCP와 직접 통신하지만, HTTPS는 TLS/SSL과 통신하고 TLS/SSL이 TCP와 통신한다.

TLS/SSL은 대칭키 암호화 방식과 공개키 암호화 방식을 하이브리드하여 데이터를 암호화한다.



#### 암호화 방식

##### 대칭키 암호화

암호화하고 복호화하는데 동일한 키를 사용한다.

##### 공개키 암호화

비밀키는 나만 갖고, 공개키는 모두에게 알려준다. 공개키로 암호화한 건 비밀키로 복호화할 수 있고, 비밀키로 암호화한 건 공개키로 복호화할 수 있다.

##### 대칭키 암호화 + 공개키 암호화

1. A가 B의 공개키로 암호화 통신에 사용할 대칭키를 암호화하고 B에게 보냄
2. B는 암호문을 받고, 자신의 비밀키로 복호화함
3. B는 A로부터 얻은 대칭키로 A에게 보낼 평문을 암호화하여 A에게 보냄
4. A는 자신의 대칭키로 암호문을 복호화함
5. 앞으로 이 대칭키로 암호화를 통신함

즉, 대칭키를 주고받을 때만 공개키 암호화 방식을 사용하고 이후에는 계속 대칭키 암호화 방식으로 통신하는 것이다.



#### TLS/SSL Handshake

HTTPS에서 클라이언트와 서버 간 통신을 수행하기 전, SSL 인증서로 신롸성 여부를 판단하기 위해 연결하는 방식

- SSL 인증서 : [`인증서를 발급한 CA 정보` + `서버측 공개키`] 를 CA의 비밀키를 이용해서 암호화한 것

- CA (Certificate Authority) : 공개키를 저장해주는 신뢰성이 검증된 민간기업. 서버는 CA와 계약하여 SSL 인증서를 만들어 가지고 있는다.



#### HTTPS 통신 과정

1. 클라이언트는 서버에 접속하면, SSL 인증서를 받는다.
2. 클라이언트는 CA의 공개키를 이용해서 SSL 인증서를 복호화하여 서버의 공개키를 추출한다.
3. 클라이언트는 서버와 통신하는 동안만 사용할 대칭키를 임의로 만들고, 해당 대칭키를 서버의 공개키로 암호화 후 전송한다.
4. 서버는 개인키로 클라이언트가 보낸 메시지를 복호화하여 클라이언트의 대칭키를 추출하고, 해당 대칭키를 이용하여 클라이언트와 통신한다.



---



## DNS (Domain Name System)

Domain Name을 IP 주소로 변환해주는 시스템



### Domain Name

사람들은 전화번호들을 일일이 외우고 다니지 않으며, 어떤 사람에게 전화를 걸려 할 때  주소록을 열고 그 사람의 이름을 검색해서 전화를 건다. 이와 마찬가지로, IP 주소를 기억하는 것은 어렵기 때문에, 휴대폰의 주소록에 있는 "이름"에 해당하는 것을 통해서 IP  주소로 접근한다. 이 "이름"에 해당하는 것을 도메인 네임 또는 도메인이라 한다.

ex) opentutorials.org, youtube.com, google.com 등등



### Domain Name Server

주소록을 열고 전화를 걸 사람의 이름을 검색해서 전화를 걸지만 실제로는 그 이름에  해당하는 '전화번호'를 통해서 전화를 걸 수 있듯이, 도메인을 통해서는 서버에 접속할 수 없고 반드시 IP 주소를 통해서만 서버에 접속할 수 있다. 도메인을 입력했을 때 해당 IP  주소를 갖는 컴퓨터를 찾아갈 수 있도록 해주는 기술을 DNS라 하고, Domain Name Server에서 이 작업을 수행한다.

모든 컴퓨터는 이 도메인 네임 서버의 주소를 알고 있다. 따라서, 클라이언트가 IP 주소를 모르는 상태에서 도메인 네임만으로 그 IP 주소에 해당하는 서버에 접속하려 할 때, 먼저 도메인 네임 서버에 접속하여 도메인 네임을 전송해준다. 도메인 네임 서버는 그 도메인 네임에 해당하는 IP 주소를 클라이언트로 응답해주고, 클라이언트는 이렇게 알아낸 IP 주소를 통해서 그 IP 주소에 해당하는 서버로 접속하게 된다. 도메인을 사용하고 싶으면, 안타깝게도 그 도메인을 구입해서 네임 서버에 등록해야 한다.

