### gradle wrapper

1. local에 jdk 버전 맞추기
- build.gradle에 있는 jdk 버전에 맞춰서(예를 들면 17이다)
- 그러면 local java도 17로 세팅 해놓기...

- 아래 처럼 나오게
  - java --version
  - 17.0.9 블라블라

2. root 폴더에서 gradle wrapper 하기...
- 하기 전에 gradle --version 해서 내가 원하는 버전에 gradle 버전 맞는지 확인하기....
- .gradle, gradle/wrapper, gradlew, gradlew.bat 등을 생성

### 되는 조합 vs 안되는 조합

- 되는 조합: spring boot 3.4.2, jdk17, gradle 8.12.1 (프로젝트 runtime 도 마찬가지로 jdk17로 설정)
- 안되는 조합: spring boot 2.5.1, jdk11, gradle 7.0.2 (프로젝트 runtime 도 마찬가지로 jdk11로 설정)
- 반드시 아래 사항 체크
  - java --version
  - gradle --version

