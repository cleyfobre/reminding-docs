## docker deploy

### 목표
 
- 젠킨스 이미지를 사용하여 젠킨스 서버를 구축한다.
- 파이프라인 및 프리스타일 프로젝트를 사용한다.
- Github으로부터 클론 및 폴링을 구현한다.
- Credentials, SSH 정보 등을 설정한다.
- 이미지 빌드 및 푸시한다.(Docker Hub)
- 배포할 서버에 SSH 접속한다.
- SSH 접속 상태에서 이미지 풀받고 앱 실행한다.
- 테스트한다. 

### 항상 EC2 메모리 체크

- dh -h

### Dockerfile 작성

- vi Dockerfile
- write code below in Dockerfile
  ```
  FROM nginx
  MAINTAINER Youngmin Jang <cleyfobre@gmail.com>

  EXPOSE 80
  ```

### EC2에서 젠킨스 실행

- 도커 설치
  - 참조: 우분투에 도커 설치 (https://docs.docker.com/engine/install/ubuntu/)
  - docker -v

- 젠킨스 설치
  - docker pull jenkins/jenkins:latest (https://hub.docker.com/r/jenkins/jenkins)
  - docker run -d -p 8080:8080 -p 50000:50000 -v /home/jenkins:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock -u root jenkins/jenkins
  - docker logs {컨테이너id} 를 통해 jenkins 초기 패스워드 찾기
  - suggest plugins 설치 및 계정/패스워드 등 설정
  - 플러그인이 설치 실패할 시 아래 내용을 해본다.
    - Jenkins 관리 클릭
    - Plugins 클릭
    - 밑에 '지금 확인' 버튼 클릭 (젠킨스 업데이트 서버와 싱크하는 기능)
    - 다시 플러그인 인스톨!

- Git 설치
  - sudo apt-get install git
  - git 경로 확인: git --exec-path
  - Jenkins 관리 -> tool -> Git installations로 이동하여 위에서 확인한 git 경로를 입력 (/usr/lib/git-core/git)

- 도커 사용 권한
  - sudo chmod 666 /var/run/docker.sock

### 방법1. 젠킨스 파이프라인

- Jenkins 관리 -> 플러그인에 Docker, Docker pipeline 검색 및 설치
- Jenkins 관리 -> Global credentials 등록
- new Item -> pipeline 프로젝트 선택
- '이 빌드는 매개변수가 있습니다' 클릭
  - String Parameter 클릭
  - 매개변수 명: BUILD_NUMBER
  - Defualt Value: 0.0.1
- Pipeline 클릭
  - Pipeline script form SCM 클릭
  - github repository 주소 넣기 (https://xxx.xxx.git)
  - Credentials 아까 만든거 넣기
  - 맨아래 Jenkinsfile 입력
- 내 github repo 프로젝트 root 폴더에 Jenkinsfile 내용
  ```
  node {
	stage('========== Clone repository ==========') {
	  checkout scm
	}
	stage('========== Build image ==========') {
	  app = docker.build("demo2")
	}
	stage('========== After Build ==========') {
	  docker.withRegistry('YOUR_REGISTRY', 'YOUR_CREDENTIAL') {
	    echo "hello world"
	  }
	}
  }
  ```
- docker command not found
  - 삽질1: docker.sock 작동 안함. 젠킨스도 컨테이너라서.
  - 삽질2: jenkins 컨테이너 내 docker 설치도 지금 헤매는중. 위랑 똑같이 했는데 apt-get udpate 에서 docker 패키지를 못찾음
  - 삽질3: deprecated라고는 하지만 작동은 하는 방법.
    ```
	apt-get update && \
	    - apt-get -y install apt-transport-https \
	     ca-certificates \
	     curl \
	     gnupg2 \
	     software-properties-common && \
	curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg > /tmp/dkey; apt-key add /tmp/dkey && \
	add-apt-repository \
	   "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") \
	   $(lsb_release -cs) \
	   stable" && \
	apt-get update && \
	apt-get -y install docker-ce
    ```
- Jenkinsfile에서 push
  - 삽질
    - 이미지를 내 레지스트리에 push한다.
      - ec2에 레지스트리를 구축하거나 private cloud에 구축
    - docker hub 스터디 필요.
      - docker.io에 image 한 개에 대해서는 private 가능
  - Build 해서 파일을 test-web ec2에 보내고 ssh 접속까지 했으나
  - 들어가서 명령어를 쓰는게 안됨
  - 들어갔다가 다시 jenkins container로 복귀함..


### 방법2. 젠킨스 프리스타일

- 프로젝트에서 사용할 Credentials 등록
  - 젠킨스 설정에서 할 수 있음. 예를 들어
  - Docker Hub 로그인 계정
  - SSH 접속하는 private key
  - SSH 호스트 주소 및 접속 디렉토리 등
  - 등을 미리 저장해둔다.
- 젠킨스 프리스타일 프로젝트 생성
- Github repository 연결하고
- 소스푸시 하면 자동으로 배포하기 위해
  - GitHub hook trigger for GITScm polling 도 선택하기
- [빌드환경] 에서 'Use secret text(s) or file(s)' 선택
  - 위에서 설정한 도커 계정 선택하고
  - Specific credentials 셀렉트박스 선택해준다.
  - 그러면 내가 username, password에 해당하는 변수 설정해놓을 수 있음.
  - 예를 들어 DOCKER_USERNAME, DOCKER_PASSWORD
- [Build Steps]에서 그레들 빌드하고 도커 로그인해서 푸시도 할꺼임
  - Execute shell 추가해서 그래들 빌드
    ```
    chmod +x gradlew
    ./gradlew clean build
    ```
  - 또 Execute shell 추가해서 도커 로그인 (아까 만든 변수 사용) 하고
  - 소스파일에 있는 Dockerfile로 이미지 만들고
  - docker hub에 푸시하고
  - 로컬에 있는 이미지 삭제하는 명령어임.
    ```
    docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD docker.io
    docker build -t cleyfobre/test-web:0.0.1 .
    docker push cleyfobre/test-web:0.0.1
    docker rmi cleyfobre/test-web:0.0.1
    ```
- [빌드 후 조치]에서 SSH로 접속, 도커 설치, 이미지 풀, 앱 실행
  - 'Send build artifacts over SSH' 선택해주고
  - 젠킨스 설정에서 저장해뒀던 SSH 접속할 서버 불러오고 (ex. test-web)
  - 'Exec command'에 아래와 같이 작성.
  - 첫번째 단락은 기존에 있는 도커관련 정보들 다 삭제하고
  - 두번째 단락은 도커를 인스톨하고
  - 세번째 단락은 도커 로그인해서 이미지 풀받고,
  - 같은 이름의 컨테이너 있으면 삭제하고 앱 컨테이너 실행
  ```
  sudo rm -f /etc/apt/keyrings/docker.gpg
  sudo apt-get purge -y docker-engine docker docker.io docker-ce docker-ce-cli docker-compose-plugin
  sudo apt-get autoremove -y --purge docker-engine docker docker.io docker-ce docker-compose-plugin
  sudo rm -rf /var/lib/docker /etc/docker
  sudo rm -rf /var/run/docker.sock

  sudo apt-get update
  sudo apt-get install \
      ca-certificates \
      curl \
      gnupg
  sudo curl -fsSL get.docker.com -o get-docker.sh
  sudo sh get-docker.sh

  sudo docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD docker.io
  sudo docker pull cleyfobre/test-web:0.0.1
  sudo docker ps -q --filter name="test-web" | grep -q . && docker rm -f $(docker ps -aq --filter name="test-web")
  sudo docker run -d --name test-web -p 8080:8080 cleyfobre/test-web:0.0.1
  ```
- 배포 완료되었으면, API 테스트하기.
  - HTTP GET 52.79.251.44:8080/main
    - Hello, World