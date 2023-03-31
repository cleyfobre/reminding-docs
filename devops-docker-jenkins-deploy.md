## docker deploy

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

### 젠킨스에 Docker image build

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