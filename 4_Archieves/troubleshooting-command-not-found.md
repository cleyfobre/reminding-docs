## command not found

### 문제

- 젠킨스 통해 app 배포시 pm2: command not found 에러가 뜬다.

### 트러블슈팅

1. 같은 유저가 맞나?
  - shell로 접속시 pm2 list가 정상적으로 작동한다.
  - 그러나 젠킨스를 통해서 pm2 list를 했을 시 pm2 명령어를 못찾는다.
  - 그래서 두 경우가 같은 유저로서의 행위인지 체크해보았다.
  - 둘다 whoami 명령어 입력시 ubuntu라고 출력이 되었다.

2. 환경 변수 확인해보기: echo $PATH
  - whoami는 같았으나, echo $PATH는 달랐다.
  - shell을 통해서 확인했을 때에는 원래 PATH에 node 관련 path가 있었고, which pm2를 통해서 확인해보니, 실제 node 패키지 경로의 pm2를 사용중이었다.
  - jenkins를 통해서 확인했을 때에는 user가 비록 ubuntu라고 할지라도 $PATH가 root와 같았다. 그래서 pm2를 찾을 수 없었던 것이다.

3. pm2 global로 재설치하기
  - 아무래도 sudo 로 pm2를 설치하지 않았던 모양이다.
  - sudo로 설치하면 pm2의 path는 /usr/local/bin/pm2 로 설정된다.
  - 그렇기 때문에 젠킨스에서 접속해도 pm2 명령어를 찾을 수 있는 것이다.

### 의문 및 해결

- 젠킨스로 ubuntu 로그인을 했는데, 왜 PATH는 root 권한처럼 나올까. chatGPT한테 물어봤다.
- I believe your Jenkins job is running a non-interactive shell, which means that it is not executing the same initialization scripts that your interactive shell does.
  - 그니까 원래 정상적으로 로그인하면 interactive shell이 실행되어 초기화 스크립트가 도는데,
  - 나의 젠킨스 아이템은 로그인해도 non-interactive shell이라 초기화 스크립트가 안돌았다고 하는데, shell로 로그인 했을 때와 jenkins 로그인했을 때 어떻게 다른지 물어봤다.
- when you use PuTTY to connect to your EC2 instance via SSH, you are connecting with an interactive shell and the shell initialization scripts such as ~/.bash_profile, ~/.bashrc, etc will be executed. On the other hand, when you use Jenkins to connect to your EC2 instance via SSH, it uses a non-interactive shell and these initialization scripts might not be executed.
  - 그렇다고 한다.. 젠킨스를 통해 로그인하는 행위는 non-interactive shell를 사용하는 것이기 때문에 위에서 언급한 initialization scripts들이 실행되지 않는다.
  - 그래서 환경 변수가 다른 이슈가 발생하는 것이다.