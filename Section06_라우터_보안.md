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

# 3. 라우터를 통한 네트워크 보안
### 1) 콘솔, AUX, VTY 포트 패스워드 보안
1. 콘솔 포트는 케이블을 이용하여 직접 터미널에 연결하여 라우터를 조작할 수 있도록 하며 장애 처리 시 많이 사용된다.
2. 패스워드를 설정하지 않은 콘솔 포트에 접속하게 되면 일반적으로 아무런 인증 절차 없이 라우터 설정을 조회할 수 있다.
3. 따라서 여러 사람이 콘솔을 사용하여 라우터에 접근할 가능성이 있는 경우에는 콘솔 패스워드를 반드시 설정하여야 한다.
```
> enable
# conf t
(config) # line console 0
(config-line) # login
(config-line) # password cisco
(config-line) # end
#
```
4. VTY 포트는 원격지에서 텔넷을 통하여 접속 시 사용되는 패스워드를 설정한다. AUX 포트는 현재 잘 사용되지 않는 포트이며 VTY 포트 설정과 동일하다.
```
> enable
# conf t
(config) # line aux 0
(config-line) # login
(config-line) # password cisco
(config-line) # end
#
```
```
> enable
# conf t
(config) # line vty 0 4 # 0 부터 4의 의미, 5 user 접속 가능
(config-line) # login
(config-line) # password cisco
(config-line) # end
# show user # 접속한 user 확인
```
4. 정리하면 console 과 aux는 config 모드에서 ```line [console or aux] 0``` 명령어로 접속하여 ```password [password]``` 명령어로 패스워드를 설정한다. vty 는 config 모드에서 ```line vty 0 4``` 명령어로 접속하여 ```password [password]``` 명령어로 패스워드를 설정하고 enable 모드에서 ```show user``` 명령어로 접속한 user를 확인합니다.

### 2) 텔넷 접근 제한
1. 기본적으로 VTY 포트는 외부로부터의 연결 시도를 모두 받아들인다. 이러한 특징을 악용하여 무한대의 공격 시도를 통하여 라우터를 공격할 수 있다.
2. 따라서 라우터로 들어오는 패킷의 IP 주소를 차단하여 허가된 IP를 가진 사용자에게만 연결 시도를 허용하는 것이 안전하다.
```
> enable
# conf t
(config)# access-list 10 permit host 192.168.0.1
(config)# access-list 10 permit host 192.168.1.1
(config)# access-list 10 deny any
(config)# line vty 0 4
(config-line)# access-class 10 in
(config-line)# end
#
```
3. SSH(Secure Shell)는 암호화 통신을 하고 텔넷은 평문 통신을 하므로 텔넷은 SSH에 비해 보안에 취약하여 SSH를 설정하여 사용할 것을 권장한다.
```
> enable
# conf t
(config)# hostname [router name]
(config)# IP domain-name [domain name]
(config)# crypto key generate rsa
(config)# IP ssh time-out [time out value]
(config)# IP ssh authentication-retries [retries value]
(config)# line vty 0 4
(config-line)# transport input ssh
(config-line)# end
#
```
4. 정리하면 텔넷 접근 제한은 예를 들어 특정 주소만 허용하려면 ``` access-list 10 permit 192.168.0.1 access-list 10 deny any``` 명령어를 사용하여 access-list 를 입력하고 config 모드에서 ```line vty 0 4``` vty 모드로 접속하고 ```access-class 10 in``` 명령어를 사용하여 접근 제한한다. SSH는 config 모드에서 ``` hostname [router name]``` ```IP domain-name [domain name]``` ```crypto key generate rsa``` ```IP ssh time-out [time out value]``` ```IP ssh authentication-retries [retries value]``` 명령어를 입력하고 config 모드에서 ```line vty 0 4``` vty 모드로 접속하고 ```transport input ssh``` 명령어를 사용하여 ssh 사용한다.

### 3) SNMP 설정
1. SNMP는 TCP/IP 네트워크를 모니터링하고 관리하기에 아주 효율적인 프로토콜이다. 대부분의 네트워크 장비들은 기본적으로 SNMP 서비스들을 포함하고 있어서 원격의 네트워크에 관련된 정보들을 수집할 수 있다.
2. 많은 시스템에서 SNMP의 읽기 권한은 기본적으로 설정되어 있기에 이를 이용하려는 공격자들이 많다. 따라서 이를 위한 보안 설정이 필요하다.
3. 기본 community string 값은 public으로 SNMP를 지원하는 많은 장비에서 기본값을 변경해야 한다.
4. access-list를 이용하여 접근 통제를 하여야 하며 SNMP 정보 수신이 필요한 장비를 명시하여 허용하고 나머지는 차단하도록 한다.
```
> enable
# conf t
(config)# snmp-server community x27swf3 ro 11
(config)# snmp-server contact antihong@tt.co.kr
(config)# access-list 11 permit host 211.1.2.5
(config)# access-list 11 deny any
(config)# interface FastEthernet 0/0
(config-if)# ip access-group 11 in
(config-if)# end
# show access-list
```
5. SNMP 서비스를 사용하지 않는다면 서비스를 제거하는 것이 좋다.
```
> enable
# conf t
(config)# no snmp-server
(config)# end
# show access-list
```
6. SNMP version 1, 2는 평문으로 정보를 전송하는 데 반해 **SNMP version 3**는 암호화가 지원되므로 SNMP version 3의 지원 여부를 확인하여 사용하기를 권장한다.
7. 정리하면 SNMP 는 읽기 권한과 community string의 public 값이 기본으로 설정되어 있어 변경해줘야 한다. config 모드에서 ```snmp-server community [패스워드] ro(readonly) [acl 번호]``` 명령어를 통해 community string 설정하고 ```contact``` 명령어를 사용하여 사용자 등록하고 ```ip access-group [acl 번호] [in or out]``` 으로 설정한다. snmp 서비스 제거방법은 config 모드에서 ```no snmp-server``` 명령어를 사용하여 제거한다.

### 4) 불필요한 서비스 제거
#### (가) 개요
1. 라우터는 네트워크 관리자의 편의를 위하여 기본 설정을 여러 가지 서비스들을 제공한다. 이러한 서비스들은 공격자에 의해서 네트워크 정보를 얻거나 접근하는 수단으로 사용될 수 있다.
2. 따라서 불필요한 서비스는 보안 위협 요소가 될 수 있으므로 모두 차단하는 것이 좋다.

#### (나) ICMP
1. ICMP MTU Discovery
   * 데이터링크 계층에서 source와 destination 사이를 지나는 패킷 크기를 조절해 주는 역할을 한다. 따라서 ICMP 패킷을 차단하더라도 MTU discovery를 제공하는 패킷은 허용해야 네트워크가 제대로 동작할 수 있다.
   * 아래와 같은 ACL을 적용하게 되면 MTU discovery를 담당하는 패킷인 ICMP type 3, type 4 패킷만을 허용하고 다른 ICMP 패킷은 모두 차단할 수 있다.
```
> enable
# conf t
(config)# access-list 103 permit icmp any any 3 4
(config)# access-list 103 deny icmp any any
(config)# access-list 103 permit ip any any
(config)# interface FastEthernet 0/0
(config-if)# ip access-group 103 in
(config-if)# end
# 
```
2. ICMP Redirects
   * 악의적인 공격자들은 ICMP Redirect를 전송하여 네트워크를 지나는 패킷의 방향을 바꿔서 정보 수집이 가능하므로 차단하는 것을 권장한다.
   * 라우터의 인터페이스로 ICMP Redirect 패킷이 들어오는 것을 차단
```
> enable
# conf t
(config)# interface FastEthernet 0/0
(config-if)# no ip redirects
(config-if)# end
#
```
3. ICMP Directed Broadcasts
   * 라우터에 directed-broadcast를 허용하면 외부에서 브로드캐스트 주소로 특정 패킷(e.g. ICMP Echo Request 메시지)을 전송하여 해당 네트워크의 모든 호스트에 전달할 수 있다. 공격자가 도스나 디도스 공격에 악용할 수 있으므로(e.g. smurt 공격) 차단을 권장한다.
```
> enable
# conf t
(config)# interface FastEthernet 0/0
(config-if)# no ip directed-broadcast
(config-if)# end
#
```
4. ICMP Mask Reply
   * 라우터가 해당하는 네트워크의 서브넷 마스크를 전송하도록 한다. 공격자는 이 기능을 이용하여 네트워크의 구성을 알아낼 수 있으므로 이 기능은 차단하는 것이 권장된다.
```
> enable
# conf t
(config)# interface FastEthernet 0/0
(config-if)# no ip mast-reply
(config-if)# end
#
```
5. ICMP Unreachable
   * 공격자에 의해 시도되는 많은 스캐닝 기법들은 ICMP Unreachable 메시지를 이용하여 스캔하는 라우터 혹은 호스트의 특정 포트가 열려있는지를 판단한다.
   * ICMP Unreachable를 차단하게 되면 공격자는 공격 대상의 상태에 대한 정보를 얻을 수 없으며 스캐닝에 소요되는 시간도 길어지기 때문에 차단을 권장한다.
```
> enable
# conf t
(config)# interface FastEthernet 0/0
(config-if)# no ip unreachables
(config-if)# end
#
```
6. ICMP Timestamp and Information Requests
   * 네트워크 관리자가 많이 사용하는 정보는 아니지만, 공격자로 하여금 네트워크 현황을 알 수 있게 하는 취약점이므로 이 두 개의 서비스를 모두 차단할 것을 권장한다.
```
> enable
# conf t
(config)# access-list 102 dney icmp any any timestamp-request
(config)# access-list 102 deny icmp any any information-request
(config)# access-list 102 permit ip any any
(config)# interface FastEthernet 0/0
(config-if)# access-group 102 in
(config-if)# end
#
```

#### (다) Source Route
1. Source Route란 패킷이 전송되는 경로를 각각의 시스템이나 네트워크에 설정된 라우팅 경로를 통하지 않고 패킷의 발송자가 설정할 수 있는 기능이다. 악용될 소지가 있으므로 중지한느 것이 좋다.
```
> enable
# conf t
(config)# no ip source-route
(config)# end
#
```

#### (라) Small Service
1. 시스코 라우터에 사용되고 있는 IOS 버전에 따라서 TCP, UDP Small Services가 자동으로 설정되어 실행된다. 이러한 서비스로는 echo, discard, daytime 등 20번 이하 포트를 사용하고 있으며 네트워킹에 특별히 중요한 역할을 담당하지는 않기에 모두 차단하는 것이 권장된다.
```
> enable
# conf t
(config)# no service tcp-small-servers
(config)# no service udp-small-servers
(config)# end
#
```

#### (마) Finger 서비스
1. 원격의 사용자로 하여금 어떤 사용자가 라우터에 접속해 있는지를 알려주는 역할을 한다. 이는 라우터에 로그인할 수 있는 사용자 이름을 비롯한 중요한 정보를 제공하기에 공격자들이 많이 사용하는 서비스 중의 하나이므로 차단을 권장하낟.
```
> enable
# conf t
(config)# no service finger
(config)# end
#
```

#### (바) HTTP Server 서비스
1. 라우터에 HTTP 서비스가 설정되어 있으면 웹을 통해 라우터의 설정을 조회하거나 변경할 수 있는데, 이는 보안상 문제가 될 뿐만 아니라 라우터의 HTTP Server 자체가 취약성을 가지고 있으므로 중지하는 것이 좋다.
```
> enable
# conf t
(config)# no ip http server
(config)# end
#
```

#### (사) CDP 서비스
1. LAN 구간에서 직접 연결된 시스코 장비들 사이에서 서로의 정보를 얻기 위해 사용되는 프로토콜이다. CDP는 라우터에 연결된 장비들의 종류와 설정을 보여주기에 전체 네트워크 구성을 파악할 때 많이 사용한다. 그러나 이는 공격자에게도 유용한 정보를 제공하기에 차단을 권장한다.
```
> enable
# conf t
(config)# no cdp run
(config)# interface FastEthernet 0/0
(config-if)# no cdp enable
(config-if)# end
#
```

#### (아) proxy-arp 서비스
1. 디폴트 라우터나 게이트웨이를 가지고 있지 않은 네트워크의 호스트들에게 ARP 서비스를 제공하는 역할을 한다. 이 경우에 호스트가 목적지 IP 주소에 대한 MAC 주소를 요청하면 Proxy ARP가 설정된 라우터가 이에 응답하여 자신의 MAC 주소를 목적지 MAC 주소인 것처럼 전송한다.
2. 공격자들은 패킷의 주소를 위조하여 Proxy ARP를 요청할 수 있으며 라우터가 이에 응답하는 것을 이용하여 라우터와 네트워크에 관련된 정보를 획득할 수 있으므로 차단을 권장한다.
```
> enable
# conf t
(config)# interface FastEthernet 0/0
(config-if)# no ip proxy-arp
(config-if)# end
#
```

#### (자) 기타 불필요한 서비스 차단
1. BootP
2. DNS
3. Network authloading of configuration files
4. PAD - packet assembly/disassembly
5. IP Classless
6. 사용하지 않는 interface는 반드시 shutdonw 하는 것을 권장한다.

### 5) 입/출력 IP 보안 설정
1. 외부에서 내부로 유입되는 IP 주소가 내부의 IP 주소라면 이는 공격자에게 악용된다고 보아야 하며 이를 차단되어야 한다.
```
> enable
# conf t
(config)# access-list 15 deny 130.18.0.0 0.0.0.255 # 내부 IP 주소가 130.18.0.0인 경우
(config)# access-list 15 permit any
(config)# interface FastEthernet 0/0
(config)# ip access-group 15 in
(config)# end
#
```
2. 외부로부터 유입되는 IP 주소 중 예약된 IP 주소가 있다면 차단하는 것을 권장한다.
3. 다음과 같은 예약된 IP 주소는 차단하도록 한다.
```
> enable
# conf t
(config)# access-list 15 deny 127.0.0.0 0.255.255.255  # 127.0.0.0/8
(config)# access-list 15 deny 10.0.0.0 0.255.255.255  # 10.0.0.0/8
(config)# access-list 15 deny 172.16.0.0 0.15.255.255  # 172.16.0.0/12
(config)# access-list 15 deny 192.168.0.0 0.0.255.255  # 192.168.0.0/16
(config)# access-list 15 deny 224.0.0.0 15.255.255.255  # 224.0.0.0/4
(config)# access-list 15 deny 240.0.0.0 7.255.255.255  # 240.0.0.0/3
(config)# access-list 15 deny 255.255.255.255 0.0.0.0  # 255.255.255.255/32
(config)# access-list 15 permit any
(config)# interface FastEthernet 0/0
(config-if)# ip access-group 15 in
(config)# end
#
```
4. 내부에서 외부로 유출되는 IP 주소가 위변조되어 출력되는 것을 차단한다.
5. ACL을 이용하여 내부 네트워크 IP 주소를 가진 패킷만 라우터로부터 전송되는 것을 허용하고 나머지 패킷들은 모두 차단하도록 한다.
```
# 내부 IP 주소가 130.218.0.0인 경우
> enable
# conf t
(config)# access-list 16 permit 130.218.0.0 0.0.255.255
(config)# access-list 16 deny any
(config)# interface FastEthernet 0/0
(config-if)# IP access-group 16 out
(config-if)# end
#
```

### 6) 주소 위변조 방지
#### (가) Unicast RPF
1. 인터페이스로 유입되는 출발지 IP 주소를 라우팅 정보를 이용하여 점검하고 내부로 유입되어 reverse path가 존재하면 패킷을 통과시키고, reverse path가 존재하지 않으면 그 IP 주소를 출발지 주소가 위조된 패킷으로 판단하고 차단한다.
```
> enable
# conf t
(config)# interface FastEthernet 0/0
(config-if)# ip verify unicast reverse-path
(config-if)# end
#
```
2. 정리하면 Unicast RPF 은 거꾸로 패킷을 보내어 출발지가 존재하는지 확인하는 것이며 설정하는 방법은 config 모드에서 ```interface FastEthernet 0/0``` 으로 글로벌 config 모드로 접속하여 ```ip verify unicast reverse-path``` 명령어를 사용하여 설정한다.

### 7) BlackHole 보안 설정
1. DDoS 공격에 대응하는 방법으로 특정 목적지로 DDoS 공격으로 네트워크 트래픽이 과도하게 발생할 경우 해당 목적지 IP를 망 내에서 통신할 수 없도록 차단한다.
```
# 211.1.1.1 ip 차단
> enable
# conf t
(config)# interface FastEthernet 0/0
(config-if)# no ip unreachable
(config-if)# exit
(config)# ip route 211.1.1.1 255.255.255.0 null 0
(config)# end
# 
```
2. 정리하면 blackhole 은 공격 패킷을 null네트워크 인터페이스로 보내어 버려지게 한다. config 모드에서 ```interface FastEthernet 0/0``` 명령어를 사용하여 글로벌 config 모드로 접속하여 ```no ip unreachable``` 명령어를 사용하여 icmp unreachable 을 차단하고 config 모드에서 ```ip rote [공격 패킷] [서브넷 마스크] 0``` 명령어를 사용하여 공격 패킷을 null 네트워크 인터페이스로 버린다
