# 계정 관리
## (1) root 이외의 UID가 0 금지
### 1) 개요

![image](https://github.com/zzzangmans1/boangisa_practice/assets/52357235/8578b049-7b7c-4e26-9775-22b02cd7ad00)

1. root(UID=0)와 동일한 UID를 가진 계정 존재 시 root 권한으로 시스템 접근이 가능하므로 root의 UID를 가진 계정이 존재하지 않도록 확인
2. 위 예에서는 algisa 계정의 UID와 GID가 모두 0로 설정되어 있으며 Telnet을 통해 algisa 계정 접속 시 root 계정으로 접속한 것과 동일한 권한을 가지고 있음을 확인할 수 있다.

### 2) 보안 설정
#### (가) /etc/passwd 파일 내 UID 확인
1. root 이외의 계정이 UID=0인 경우 0이 아닌 적절한 UID를 부여하거나 불필요한 경우 해당 계정을 삭제한다.

#### (나) UID 변경
||OS별 점검 파일 위치 및 점검방법|
|:-:|:-|
|SOLARIS, LINUX, HP-UX:|:usermod 명령으로 UID가 0인 일반 계정의 UID 변경(SOLARIS, HP-UX의 경우 100 이상, LINUX의 경우 500 이상 설정) </br> 예) usermod -u 2016 algisa </br> algisa 계정의 UID를 2016으로 설정|
|AIX|chuser 명령으로 UID가 0인 일반 계정의 UID를 100 이상 설정 </br> 예) chuser id=2016 algisa </br> algisa 계정의 UID를 2016으로 설정|

## (2) 패스워드 복잡성 설정
### 1) 개요
1. 사용자 계정(root 및 일반 계정 모두 해당) 암호를 유추하기 쉽게 설정하면 비인가자의 시스템 접근을 허용하게 하는 위험이 존재

### 2) 보안설정

