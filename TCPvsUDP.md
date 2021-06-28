
# TCP와 UDP
- 네트워크의 계층들 중 **전송 계층(데이터의 신뢰성있는 전달을 담당)에서 사용**하는 프로토콜
    - Common layer shared by all applications
    - **Reliable delivery** of data
    - Ordering of delivery (**in the same order** as sent)
    - **Commonly use TCP**
- 데이터(패킷)를 보내기 위해 사용하는 프로토콜
- 두 프로토콜 모두 패킷을 한 컴퓨터에서 다른 컴퓨터로 전달해주는 IP 프로토콜을 기반으로 구현되어 있음

<img src="./img/tcpudp1.png" width="550" />

---

# TCP (Transmission Control Protocol)
- **신뢰성을 보장**하는 연결형 서비스
- 네트워크에 연결된 컴퓨터에서 실행되는 프로그램 간 패킷(데이터, 메시지, TCP segment) 를 **안정적으로, 순서대로, 에러없이** 교환하는 것을 보장
- 일반적으로 TCP와 IP를 함께 사용 : IP가 데이터의 배달을 처리 + TCP는 패킷을 추적/관리

### 특징
1. **연결형 서비스**
    - 연결형 서비스로 가상 회선 방식을 제공
    - 3-way handshaking 과정을 통해 연결을 설정하고 4-way handshaking을 통해 해제
2. **흐름 제어 (Flow Control)**
    - 데이터 처리 속도를 조절하여 수신자의 버퍼 오버플로우 방지
    - 송신하는 곳에서 감당이 안되게 많은 데이터를 빠르게 보내 수신하는 곳에서 문제가 일어나는 것을 막는다
    - 수신자가 윈도우크기(Window Size) 값을 통해 수신량을 정할 수 있다
3. **혼잡 제어 (Congestion control)**
    - 네트워크 내의 패킷 수가 넘치게 증가하지 않도록 방지
    - 정보의 소통량이 과다하면 패킷을 조금만 전송하여 혼잡 붕괴 현상이 일어나는 것을 막는다
4. **신뢰성 높은 전송 (Reliable Transmission)**
    - Dupack-based retransmission
        - 정상적인 상황에서는 ACK 값이 연속적으로 전송되어야 한다
        - ACK 값이 중복으로 올 경우 패킷 이상을 감지하고 재전송을 요청한다
    - Timeout-based retransmission
        - 일정시간동안 ACK 값이 오지 않을 경우 재전송을 요청한다
    - **ACK** : 수신측에서 송신측에게 긍정 응답을 표현하기 위해 보내는 전송 제어용 캐릭터
5. **전이중, 점대점 방식**
    - 전이중 (Full-Duplex) : 전송이 양방향으로 동시에 일어날 수 있다
    - 점대점 (Point to Point) : 각 연결이 정확히 2개의 종단점을 가지고 있다

### 단점
1. UDP보다 속도가 느림
    - CPU를 사용하기 때문에 속도에 영향을 주는 것

> ⇒ TCP는 연속성보다 **신뢰성 있는 전송이 중요할 때에 사용**하는 프로토콜 (ex. 파일 전송)


### TCP 연결 및 해제 과정
<img src="./img/tcpudp2.png" width="550" />

1. TCP Connection (3-way handshake)
    - 먼저 open()을 실행한 클라이언트가 SYN을 보내고 SYN_SENT 상태로 대기
    - 서버는 SYN_RCVD 상태로 바꾸고 SYN과 응답 ACK를 전송
    - SYN과 응답 ACK을 받은 클라이언트는 ESTABLISHED 상태로 변경하고 서버에게 응답 ACK를 전송
    - 응답 ACK를 받은 서버는 ESTABLISHED 상태로 변경
2. TCP Disconnection (4-way handshake)
    - 먼저 close()를 실행한 클라이언트가 FIN을 보내고 FIN_WAIT1 상태로 대기
    - 서버는 CLOSE_WAIT 으로 바꾸고 응답 ACK를 전송. 동시에 해당 포트에 연결되어 있는 어플리케이션에 close() 요청
    - ACK를 받은 클라이언트는 상태를 FIN_WAIT2 로 변경
    - close() 요청을 받은 서버 어플리케이션은 종료 프로세스를 진행하고 FIN을 클라이언트에 보내 LAST_ACK 상태로 변경
    - FIN을 받은 클라이언트는 ACK를 서버에 다시 전송하고 TIME_WAIT 상태로 변경. TIME_WAIT 에서 일정 시간이 지나면 CLOSED로 상태 변경.
    - ACK를 받은 서버도 포트를 CLOSED로 닫음

<img src="./img/tcpudp3.png" width="550" />

---

# UDP (User Datagram Protocol)
- 사용자 데이터그램 프로토콜 == 데이터를 데이터그램(독립적 관계를 지니는 패킷) 단위로 처리하는 프로토콜
- 비연결형 프로토콜 (즉, 연결을 위해 할당되는 논리적인 경로가 없음)
    - 각각의 패킷은 다른 경로로 전송되고, 독립적인 관계를 지니게 됨

### 특징
- 데이터의 순서, 복제에 대해 신경쓰지 않고 reliable한 delivery를 보장하지 않음
    - **No guarantee of delivery**
    - No preservation of sequence
    - No protection against duplication
- **Minimum overhead** ⇒ 오버헤드를 최소화하는 것이 주목적이다!
- 비연결형 서비스로 데이터그램 방식을 제공
    - 비연결형 서비스이기 때문에, 연결을 설정하고 해제하는 과정 존재 X
- 정보를 주고 받을 때 정보를 보내거나 받는다는 신호절차를 거치지 않음
- UDP 헤더의 CheckSum 필드를 통해 최소한의 오류만 검출
- TCP보다 **속도가 빠르고 네트워크 부하가 적음**
    - 서로 다른 경로로 독립적으로 처리함에도 패킷에 순서를 부여하여 재조립을 하거나 흐름 제어 또는 혼잡 제어와 같은 기능도 처리하지 않기 때문
- 신뢰성이 낮음
    - 신뢰성있는 데이터의 전송을 보장하지 못함

⇒ UDP는 신뢰성보다는 **간단한 데이터를 빠른 속도로 전송해야하는 연속성이 중요한 서비스**에 사용 (ex. 실시간 서비스 streaming)

<img src="./img/tcpudp4.png" width="550" />

---

# TCP vs UDP
### 공통점
- 포트 번호를 이용하여 주소를 지정
- 데이터 오류 검사를 위한 CheckSum 존재

### 차이점
<img src="./img/tcpudp5.png" width="550" />

## 참고링크
- [참고링크1](https://mangkyu.tistory.com/15)
- [참고링크2](https://velog.io/@hidaehyunlee/TCP-%EC%99%80-UDP-%EC%9D%98-%EC%B0%A8%EC%9D%B4)