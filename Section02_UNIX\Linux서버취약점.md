# 계정 관리
## (1) root 이외의 UID가 0 금지
### 1) 개요

![image](https://github.com/zzzangmans1/boangisa_practice/assets/52357235/8578b049-7b7c-4e26-9775-22b02cd7ad00)

1. root(UID=0)와 동일한 UID를 가진 계정 존재 시 root 권한으로 시스템 접근이 가능하므로 root의 UID를 가진 계정이 존재하지 않도록 확인
2. 위 예에서는 algisa 계정의 UID와 GID가 모두 0로 설정되어 있으며 Telnet을 통해 algisa 계정 접속 시 root 계정으로 접속한 것과 동일한 권한을 가지고 있음을 확인할 수 있다.

정보보안기사_실기
