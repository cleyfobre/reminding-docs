#sdkman #jdk 

sdkman은 여러개의 sdk를 설치한 후 필요에 따라 환경을 바꿔가며 사용하게 해주는 유틸리티이다.

```sh
# 설치
curl -s "https://get.sdkman.io" | bash

# 설치 완료 후 sdk 초기화
# 나의 경우 $HOME은 /Users/youngmin
source "$HOME/.sdkman/bin/sdkman-init.sh"

# version 확인
sdk version

# 설치 가능한 Java SDK 확인하기
sdk list java
```

위 명령어를 실행하면 아래와 같이 설치 가능한 java 리스트들을 볼 수 있다.

```sh
================================================================================

Available Java Versions for macOS ARM 64bit

================================================================================

 Vendor        | Use | Version      | Dist    | Status     | Identifier

--------------------------------------------------------------------------------

 Corretto      |     | 21.0.1       | amzn    |            | 21.0.1-amzn         

 ...        

 Zulu          |     | 21.0.1       | zulu    |            | 21.0.1-zulu         

               |     | 21.0.1.fx    | zulu    |            | 21.0.1.fx-zulu      

               |     | 17.0.9       | zulu    |            | 17.0.9-zulu         

               |     | 17.0.9.fx    | zulu    |            | 17.0.9.fx-zulu      

               | >>> | 11.0.21      | zulu    |            | 11.0.21-zulu        

               |     | 11.0.21.fx   | zulu    |            | 11.0.21.fx-zulu
```

그리고 이 리스트 중에서 나는 Zulu에서 제공되는 jdk 11과 17을 다운 받을 것이다.

```sh
sdk install java 11.0.21-zulu
sdk install java 17.0.9-zulu
```

다시 한 번 `sdk list java` 를 입력하면 내가 설치한 sdk 를 볼 수 있다. 현재 사용중인 sdk 에는 `>>>` 표시가 되어 있고, 설치된 sdk에는 `installed` 가 표시되어 있다. 즉 현재 상태에서는 아래와 같이 `11.0.21` 버전을 사용 중이기 때문에 `java --version` 을 치면 해당 버전이 표시된다.

```sh
 ...
 Zulu          |     | 21.0.1       | zulu    |            | 21.0.1-zulu         

               |     | 21.0.1.fx    | zulu    |            | 21.0.1.fx-zulu      

               |     | 17.0.9       | zulu    | installed  | 17.0.9-zulu         

               |     | 17.0.9.fx    | zulu    |            | 17.0.9.fx-zulu      

               | >>> | 11.0.21      | zulu    | installed  | 11.0.21-zulu        

               |     | 11.0.21.fx   | zulu    |            | 11.0.21.fx-zulu     

               |     | 8.0.392      | zulu    |            | 8.0.392-zulu
 ...
```

그리고 java를 17로 교체하고 싶다면 아래와 같이 작성한다.

```sh
sdk use java 17.0.9-zulu
```
