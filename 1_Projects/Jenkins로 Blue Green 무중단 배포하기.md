#젠킨스 #jenkins #jenkinsfile #pipeline #파이프라인 #무중단배포 #bluegreen #nginx 

> [!tip]
> 해당 문서는 https://github.com/cleyfobre/blue-green-deploy 의 Readme 문서입니다.

#### jenkinsfile 설정

- 각 App의 Dockerfile을 이용하여 이미지 빌드를 한다.
- ECR에 로그인 후 이미지를 배포한다.
- deploy 관련 파일들을 배포할 서버에 보낸다.
- 배포할 서버에서 deploy.sh를 실행시킨다.
#### 전송된 파일 구성

- setting.sh
	- 도커가 없으면 도커 설치 스크립트
- deploy.sh
	- 아래의 파일들을 이용해 컨테이너 실행 및 종료를 담당하는 실질적 배포 스크립트
- docker-compose.blue.yml, docker-compose.green.yml
	- App 컨테이너가 blue면 green을, green이면 blue를 실행
- Dockerfile_nginx
	- nginx 컨테이너가 없으면 해당 파일을 통해 실행
- nginx.conf
	- 최초 nginx 컨테이너 실행될 때 필요한 부모 conf
- nginx.blue.conf, nginx.green.conf
	- App 컨테이너가 blue면 green을, green이면 blue를 실행
	- nginx.conf의 자식 conf