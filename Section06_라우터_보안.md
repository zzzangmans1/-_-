# 1. 라우터 자체 보안
### 1) 개요
1. 라우터는 처음 접속하게 되면 사용자는 사용자 모드로 접속을 하게 되며 라우터 관련 정보의 조회가 가능하다. ```enable``` 명령어를 사용하여 privileged 모드로 레벨을 변경하면 조회뿐만 아니라 설정 변경 등의 작업을 할 수 있다. 참고로 라우터 사용 모드는 다음과 같다.
   * User EXEC 모드 : 한정된 명령어만 사용할 수 있으며 주로 라우터의 간단한 상태 등을 조회할 수 있다. 프롬프트는 Router> 와 같이 보이게 된다.
   * Privileged EXEC 모드 : 재부팅이나 라우팅 등 라우터에서의 모든 명령어에 대해 수행이 가능하며 프롬프트는 Router# 와 같이 보이게 된다.
   * Global Configuration 모드 : Privileged EXEC 모드에서 라우터 전반적인 설정을 변경하고자 할 때의 모드로 Router(config)# 와 같이 보이게 된다. 만약 특정 인터페이스나 특정 라우팅 등을 변경하고자 할 때에는 아래의 Other Configuration 모드가 사용된다.
   * Other Configuration 모드 : 좀 더 복잡하고 세부적인 설정을 하는 메뉴로서 Router(config-mode)# 와 같이 보이게 된다. 설정이 끝난 후에는 exit 나 Ctrl+z를 입력하면 된다.
2. Privileged 모드로 변경할 때 사용하는 ```enable``` 패스워드를 설정하기 위해서는 ```enable password``` 와 ```enable secret```의 두 가지 명령어를 사용할 수 있다. 안전한 운영을 위하여 enable secret 명령어를 사용할 것을 권장한다.
3. ```enable password [패스워드]``` 명령어
   * type 0(평문으로 저장되는 방식)의 평문으로 저장
   * 평문으로 저장된 암호는 ```service password-encryption``` 명령을 실행하면 암호화를 수행하는데 type 7(역함수가 존재하여 원래의 평문 암호를 알 수 있는 방식) 방식으로 암호화한다.
4. ```enable secret [패스워드]``` 명령어
   * type 5(일방향 함수로 암호화/MD5)의 암호문으로 저장되어 원래의 평문 암호를 알 수 없다.
   * 만약 ```enable password```와```enable secret```가 함께 설정되어 있으면 ```enable secret```가 적용된다.
5. 정리하면 User 모드에서는 Route> 로 표시되고 ```enable``` 명령어를 사용하여 Privileged 모드를 들어가면 Router# 으로 표시되고 Global Configuration 모드에서는 Router(config)# 으로 표시되고 Other Configuration 모드에서는 Router(config-mode)로 표시된다. Privileged 모드 패스워드 설정은  ```enable password [패스워드]``` 명령어와 ```enable secret [패스워드]``` 명령어로 패스워드 설정을 할 수 있는데 password는 평문으로 저장(type 0)되어 ```service password-encryption``` 명령어를 사용하여 암호화 시키는데 역함수가 존재하는 암호화(type 7)이고 secret 은 해시 함수로 저장(type 5)된다. password, secret 둘 다 설정되어 있으면 secret으로 적용된다.

# 2. 라우터와 접근제어(ACL)
### 1) 개요
1. 라우터에서는 access-list라는 것을 이용하여 특정 패킷에 대한 접근제어를 설정할 수 있다.
2. 접근제어 방법에는 크게 standard(표준) access-list와 extended(확장) access-list로 나누어 볼 수 있다.

### 2) standard access-list
1. 패킷의 소스 IP만으로 패킷을 허용하거나 차단한다.
2. ```access-list(acl) number```로 1~99까지 사용한다.
3. **형식) ```access-list acl번호 [permit 또는 deny] [소스주소 wildcard 또는 any]```**
4. 예1) access-list 5 permit host 192.168.159.131
   * source ip가 192.168.159.131일 경우 접근을 허용한다.
   * 특정 ip를 지정할 경우 ```host```를 명시
   * ip 대역을 지정할 경우에는 ```host```를 명시하지 않고 ```wildcard``` 마스크를 명시한다. ```wildcard``` 마스크는 서브넷 마스크 비트를 반전시킨 것과 동일하다.
   * 모든 주소를 의미할 때는 ```any``` 키워드를 사용한다.
5. 예2) access-list 5 deny any
   * 모든 출발지 IP에 대해서 차단/필터링한다.
6. 정리하면 standard access-list 는 1~99번 을 사용하고 특정 ip를 지정할 경우 ip 주소 앞에 host를 붙인다. 모든 ip에 대해서는 any를 사용하고 특정 네트워크 대역을 사용할 경우에는 10.10.10.0 0.0.0.255 로 wildcard 를 쓴다 서브넷 비트 반전시킨 것이 wildcard이다. 예) ```access-list 5 permit host 192.168.159.133일 경우 192.168.159.133``` ip에 대해 허용
  
### 3) extended access-list
1. 패킷의 소스 IP뿐만이 아니라 목적지 IP, 포트, 프로토콜 등을 이용하여 차단할 수 있어 좀 더 확장된 기능을 이용할 수 있다.
2. ```access-list(acl) number```로 100~199까지 사용한다.
3. **형식) ```access-list acl번호 [permit 또는 deny] 프로토콜 소스 소스-wildcard 목적지 목적지-wildcard```**
4. 예1) ```access-list 150 deny ip host 192.168.159.131 100.100.100.0 0.0.0.255```
   * 특정 ip를 지정할 경우 ```host``` 명시
   * ip 대역을 지정할 경우에는 ```host```를 명시하지 않고 wildcard 마스크를 명시한다. wildcard 마스크는 서브넷 마스크 비트를 반전시킨 것과 동일하다.
   * 모든 주소를 의미할 때는 ```any``` 키워드를 사용한다.
   * 출발지가 192.168.159.131 이고 목적지 100.100.100.0/24 대역인 IP 패킷에 대해서 모두 차단/필터링 한다.
5. 예2) ```access-list 150 deny tcp 192.168.1.0 0.0.0.255 host 100.100.100.1 eq80```
   * 출발지가 192.168.1.0/24 대역이고 목적지 IP가 100.100.100.1의 http의 tcp 패킷에 대해서 차단/필터링한다.
6. 예3) ```access-list 100 deny udp host 192.168.2.5 100.100.100.0 0.0.0.255 range 100 200```
   * 출발지 IP가 192.168.2.5 이고 목적지가 100.100.100.0/24 대역에 100~200 port에 대해서 udp 패킷이면 차단/필터링한다.
7. 정리하면 extended access-list 는 100~199번 을 사용하고 permit, deny 다음에 프로토콜을 사용한다. 그리고 eq 80 으로 포트가 80번 포트라면, range 100 200 으로  포트가 100~200번 포트라면 등 옵션이 있다. 예) ```access-list 150 permit tcp 10.10.10.0 0.0.0.255 host 192.169.159.133 eq 80``` 으로 192.168.159.133의 http tcp 패킷을 10.10.10.0/24 대역으로 오면 허용한다. 예) ```access-list 150 deny udp host 192.168.2.100 10.10.10.0 0.0.0.255 range 100 200``` 출발지 ip가 192.168.2.100 에서 목적지 10.10.10.0/24 대역으로 100번~200번포트의 udp 패킷은 차단한다.

### 4) Filtering 유형
#### (가) Ingress Filtering
1. standard 또는 extended access-list를 활용하여 라우터 내부로 즉 사내 네트워크로 유입되는 패킷의 source ip나 목적지 port 등을 체크하여 허용하거나 거부하도록 필터링하는 것을 의미한다.
2. 대부분의 공격이 실제 존재하지 않는 위조된  ip 주소를 소스로 하여 진행되므로 인터넷상에서 사용되지 않는 ip 대역만 차단해도 비정상 패킷을 사전에 차단하는 효과가 있다.
3. 정리하면 Ingress Filtering은 라우터 내부로 들어오는 패킷의 소스 IP나 목적지 PORT 등을 체크하여 허용 거부 하는 것이고 실제 존재하지 않는 위조된 ip 주소를 소스로 하여 진행되므로 인터넷상에서 사용되지 않는 ip 대역만 차단해도 효과가 있다.

#### (나) Egress Filtering
1. 라우터 내부에서 라우터 외부로 나가는 패킷의 source ip나 목적지 port 등을 체크하여 필터링한다.
2. 외부로 나가는 패킷의 source ip는 반드시 내부 네트워크 대역인 것이 정상이며 이외의 패킷은 모두 위조된 패킷이다.
3. 정리하면 Egress Filtering은 라우터 내부에서 라우터 외부로 나가는 패킷의 소스 IP나 목적지 PORT 등을 체크하여 허용 거부 하는 것이고 외부로 나가는 패킷의 출발지 IP는 반드시 내부 네트워크 대역인 것이 정상이며 이외는 위조된 패킷이다.

#### (다) Blackhole Filtering(Null routing)
1. 특정 IP 또는 IP 대역에 대하여 비정상적인 시도가 감지되었을 경우 가상의 쓰레기 인터페이스로 보내도록 함으로써 패킷 통신이 되지 않도록 하는 방법이다.
2. 정리하면 Blackhole Filtering(Null routing)은 비정상적인 시도가 감지되었을 경우 가상의 쓰레기 인터페이스로 보냄으로써 패킷 통신이 되지 않는 방법

#### (라) Unicast RPF(Reverse-Path Forwarding) Filtering
1. access-list나 blackhole 필터링을 이용하여 일일이 ip 또는 ip 대역을 지정하지 않고도 비정상 트래픽을 효율적으로 필터링하는 기법
2. 인터페이스를 통해 들어오는 패킷의 소스 ip에 대해 라우팅 테이블을 확인하여 들어온 인터페이스로 다시 나가는지 확인하는 원리이다. 즉, URPF가 enable된 인터페이스에 10.10.10.10이라는 source ip를 달고 들어오는 패킷이 있다면 라우팅 테이블을 확인하여 만약 10.10.10.10이라는 목적지로 라우팅 될 때 같은 인터페이스로 나가는지 확인하여 비정상 여부를 판단한다.
3. 정리하면 Unicast RPF(Reverse-Path Forwarding)는 만약 URPF가 enable된 인터페이스로 10.10.10.10 이라는 출발지 IP가 들어오는 패킷이 있다면 라우팅 테이블(특정 목적지에 가는 경로를 저장하고 있는 테이블)을 확인하여 10.10.10.10이라는 목적지로 라우팅될 때 같은 인터페이스로 나가는지 확인하여 비정상 여부 판단한다. 즉, 위조된 IP이면 차단한다.

