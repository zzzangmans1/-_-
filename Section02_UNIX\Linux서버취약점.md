# 계정 관리
## (1) root 이외의 UID가 0 금지
### 1) 개요

![image](https://github.com/zzzangmans1/boangisa_practice/assets/52357235/8578b049-7b7c-4e26-9775-22b02cd7ad00)
```
cat /etc/passwd | egrep "^(찾을아이디)|^(찾을아이디)"
```
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
#### (가) 부적절한 패스워드 유형
1. 사전에 나오는 단어나 이들의 조합
2. 길이가 너무 짧거나 공백(NULL)인 패스워드
3. 키보드 자판의 일련 순 (예) asdf, qwer, 1234 등
4. 사용자 계정 정보에서 유추 가능한 단어 (예) 사용자 이름, 지역, 회사, 부서명, 전화번호 등

#### (나) 패스워드 관리 방법
1. 영문, 숫자, 특수문자를 조합하여(패스워드 복잡성을 높임) 계정명과 상이한 8자 이상의 패스워드 설정
2. 다음 문자 종류 중 2종류 이상을 조합하여 최소 10자리 이상 또는 3종류 이상을 조합하여 최소 8자리 이상의 길이로 구성
   * 영문 대문자(26개)
   * 영문 소문자(26개)
   * 숫자(10개)
   * 특수문자(32개)
3. 시스템마다 상이한 패스워드 사용하고 패스워드를 기록할 경우에는 변형하여 기록한다.
4. 가급적 자주 패스워드를 변경한다.

## (3) 패스워드 최소 길이 설정
### 1) 개요
1. 패스워드 무차별 공격(Brute Force Attack)이나 패스워드 추측 공격(Password Guessing)에 대응하기 위해 패스워드 최소길이 설정 필요

### 2) 보안설정

![image](https://github.com/zzzangmans1/boangisa_practice/assets/52357235/af892608-8467-4b96-a6df-85812d628598)

```
#cat /etc/login.defs | grep "PASS_MIN_LEN"
```

#### (가) 각 OS별 패스워드 정책설정 파일 점검
||OS별 점검 파일 위치 및 점검 방법
|:-:|:-|
|SOLARIS|#cat /etc/default/passwd </br>PASSLENGTH=8|
|LINUX|#cat /etc/login.defs </br>PASS_MIN_LEN 8|
|AIX|#cat /etc/security/user </br>minlen=8|
|HP-UX|#cat /etc/default/security </br>MIN_PASSWORD_LENGTH=8|
1. 패스워드 정책설정 파일을 수정하여 패스워드 최소길이를 8자 이상으로 설정

## (4) 패스워드 최대 사용기간 설정
### 1) 개요
1. 패스워드 최대 사용 기간을 설정하지 않으면 일정 기간 경과 후에도 유출된 패스워드로 접속할 수 있음
2. 악의적인 사용자의 계속된 접속을 차단하기 위해 패스워드 최대 사용 기간을 설정하여 주기적으로 변경할 수 있도록 함

### 2) 보안설정

![image](https://github.com/zzzangmans1/boangisa_practice/assets/52357235/94490af7-294b-42c6-bc59-3b461a27c28b)

```
#cat /etc/login.defs | grep "PASS_MAX_DAYS"
```

#### (가) 각 OS별 패스워드 정책설정 파일 점검
||OS별 점검 파일 위치 및 점검방법|
|:-:|:-|
|SOLARIS|#cat /etc/default/passwd </br>MAXWEEKS=12(단위 : 주)|
|LINUX|#cat /etc/login.defs </br>PASS_MAX_DAYS 90(단위 : 일)|
|AIX|#cat /etc/security/user </br>maxage=12(단위 : 주)|
|HP-UX|#cat /etc/default/security </br>PASSWORD_MAXDAYS=90(단위 : 일)|
1. 패스워드 정책설정 파일을 수정하여 패스워드 최대 사용 기간을 90일(12주)로 설정

## (5) 패스워드 최소 사용기간 설정
### 1) 개요
1. 패스워드 최소 사용 기간을 설정하지 않으면 사용자에게 익숙한 패스워드로 변경할 수 있으며 이를 재사용함으로써 패스워드의 정기적인 변경은 무의미해질 수 있음

### 2) 보안설정

![image](https://github.com/zzzangmans1/boangisa_practice/assets/52357235/569adb37-c343-4d78-98f6-60ac04647b07)

```
#cat /etc/login.defs | grep "PASS_MIN_DAYS"
```

#### (가) 각 OS별 패스워드 정책설정 파일 점검
||OS별 점검 파일 위치 및 점검 방법
|:-:|:-|
|SOLARIS|#cat /etc/default/passwd </br>MINWEEKS=1(단위 : 주)|
|LINUX|#cat /etc/login.defs </br>PASS_MIN_DAYS 1(단위 : 일)|
|AIX|#cat /etc/security/user </br>minage=1(단위 : 주)|
|HP-UX|#cat /etc/default/security </br>PASSWORD_MINDAYS=1(단위 : 일)|
1. 패스워드 정책설정 파일을 수정하여 패스워드 최소 사용 기간을 1일(1주)로 설정

## (6) 패스워드 파일 보호
### 1) 개요
1. 패스워드 정보를 평문으로 저장하는 경우 정보 유출 피해가 발생할 수 있으므로 패스워드를 암호화하여 보호하여야 함
2. shadow 패스워드를 사용하여 "/etc/shadow" 파일에 암호화된 패스워드가 저장되도록 하고 권한이 있는 사용자들만 읽을 수 있도록 제한함

### 2) 보안설정
#### (가) 각 OS별 점검 방법
||OS별 점검 파일 위치 및 점검방법|
|:-:|:-|
|SOLARIS, LINUX|1. /etc/shadow 파일 존재 확인 </br>2. /etc/passwd 파일 내 두 번째 필드가 "x" 표시되는지 확인 </br>#cat /etc/passwd </br>root:x:0:0:root:/root:/bin/bash|
|AIX|AIX 서버는 기본적으로 "/etc/security/passwd" 파일에 패스워드를 암호화하여 저장|
|HP-UX|1.tcb 디렉터리 존재 확인 </br>2. /etc/passwd 파일 내 두 번째 필드가 "x" 표시되는지 확인 </br>HP-UX 서버는 Trusted Mode로 전환할 경우 패스워드를 암호화하여 "/tc/files/auth" 디렉터리에 계정 이니셜과 계정 이름에 따라 파일로 저장|
