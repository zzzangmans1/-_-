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
