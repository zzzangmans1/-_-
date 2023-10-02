## SAM(Security Account Manager)
1. 크래킹을 통해 패스워드를 얻을 수 있다.
2. 운영체제가 작동하는 한 접근할 수 없도록 잠겨져 있다.
3. 레지스트리 HKEY_LOCAL_MACHINE/SAM에 구체화된 자료들을 실제로 저장한다.

## SCAN
* 엘리베이터 알고리즘이라고 불리는 디스크 스케줄링 알고리즘이다.

## BitLocker
* exFAT 파일 시스템은 지원하지 않는다.
* 컴퓨터를 시작하는데 필요한 시스템 파티션 부분도 암호화하지 않는다.

## EDR (Endpoint Detection Response)
1. 보안사고 탐지 영역
2. 보안사고 통제 영역
3. 보안사고 치료 영역

## 리눅스 로그
1. secure 로그 : 사용자의 원격 로그인 정보를 저장
2. dmesg 로그 : 시스템 부팅 관련 시스템 메시지 저장
3. lastlog 로그 : 사용자가 로그인한 마지막 로그 저장
4. utmp 로그 : 현재 로그인한 사용자 정보
5. wtmp 로그 : 성공한 로그인/로그아웃 정보
6. btmp 로그 : 로그인 실패 정보

## AI 보안
1. Membership inversion 공격 : 특정 데이터가 모델의 훈련에 속하는지 확인
2. Adversarial 공격 : 모델을 속이기 위해 작은 변화나 노이즈를 사용하는 공격
3. Poisoning 공격 : 악의적인 데이터를 훈련 데이터에 주입하여 모델을 속이거나 성능 저하 공격
4. Model inversion 공격 : 모델의 출력 기반으로 입력 데이터의 일부 추측 공격

## 화이트박스 분석
* 소스코드 이해하고 분석하는 방법으로 프로그래밍 오류와 구현 오류를 찾을 때 유용한 방법

## lsof
* umount 명령을 통해 분리 과정에서 "Device is busy" 문구 뜨면 디바이스 사용 프로세스 찾는 명령어

## 리버싱 도구
1. OllyDbg : PE 파일의 구조와 동작 확인
2. Proexp : 프로세스 동작 정보 확인
3. Filemonitor : 파일 이벤트 정보 확인
4. ltrace : 공유 라이브러리 호출 확인

## 미라이
* 2016년 처음 발견되고, IP 카메라나 가정용 라우터와 같은 IoT 장치 공격 DDoS 공격용 봇넷

## ARP Spoofing
* ```arp -s [IP] [MAC]``` 명령어를 통해 정적으로 ARP 테이블 관리

## ModSecurity
* SecAuditEngine 에서 DetectionOnly 옵션을 설정 불가능

## VLAN 
* VLAN 오/남용 경감시키기 위한 방법이 아닌 것은 VLAN 관리 정책 서버(VMPS)사용

## 포트 - 서비스
1. 138 - NetBIOS 데이터그램 서비스-UDP

## 무선 LAN
* Enhanced Open :  패스프레이즈와 같은 인증 없이 단말과 액세스 포인트간의 무선 통신을 암호화하는것
* WSP(Wireless Session Protocol) : 장시간 활용하는 세션을 정의하고 세션 관리를 위해 Suspend/Resume 기능과 프로토콜 기능에 대한 협상 가능

## PGP 
* 사용 알고리즘 RSA, SHA, Diffie-Hellman

## 이중서명
* 주문정보의 메시지 다이제스트와 지불정보의 메시지 다이제스트를 합하여 다시 이것의 메시지 다이제스트를 구한 후 고객의 서명용 개인키로 암호화

## 안드로이드
* AndroidManifest.xml : 앱 실행시 반드시 필요한 권한을 선언하며, 안드로이드 빌드 도구 및 안드로이드 운영체제에 관힌 필수 정보 설명 파일
* SET_PROCESS_LIMIT : 제한처리 지정 권한
* LOADER_USAGE_STATS : 액세스 로그 읽기 권한
* ACCESS_CHECIN_PROPERTIES : 체크인 데이터베이스의 속성테이블 액세스 권한

## SMTP
* 인증절차 후 메일 발송 절차 EHLO > AUTH > RCPT > MAIL > DATA > QUIT

## tftp
* 적은 양의 데이터 보낼 때 사용하며 보안 취약

## ebXML
* 전자상거래를 위해 컨소시엄인 OASIS가 개발한 전자상거래 분야 개방형 표준

## CRL
* 만료된 디지털 인증서 일련번호는 포함된다.

## SSL/TLS
* 1.3 버전을 사용하면 통신의 기밀성 확보

## OTP
1. PKI를 개변 연동한다.

## HSM
* 안전성 인증 적용은 FIPS 140-2이다.
* 공개키 사용한다.

## Needham-Schroeder
* 재전송공격 취약하지 않는다.

## OSCP 
* 서버 응답값 상태표시 메시지 : good, revoked, unknown

## Blom
* 키 분배센터(KDC)에서 두 노드에게 임의의 함수값을 전송하면 두 노드는 전송받은 정보로부터 두 노드 사이의 통시넹 필요한 세션키 생성

## MAC(Message Authentication C)
* 부인방지는 재전송 공격을 예방하지 않는다.

## 사이드채널 공격
* 암호화 장치에서 암호화 처리시에 소비 전력을 측정하는 등 해당 장치 내부의 비밀 정보를 추정하는 공격

## ISO 27001
* Plan-Do-Check-Action 모델 채택

## 포렌식 수행 절차 단계
* 증거물 획득 : 컴퓨터의 검사, 사진, 라벨 붙임

## 개인정보처리 업무 위탁 계약
* 개인정보취급자를 채용 요청은 안된다.

## 전문인력 양성 전담 기관 
* 한국인터넷진흥원, 한국지역정보개발원, 한국지능정보사회진흥원

## 정보보호관리체계 인증 범위
* 필수적인 자산 아닌 것은 ERP, DW, GroupWare

## 정보보안 속성
* 로그는 책임추적성

## 정량적 위험분석
* 과거 자료 분석법


