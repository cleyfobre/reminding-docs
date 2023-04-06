## nginx

### 목표

- Nginx로 Reverse Proxy 구축하기

### 과정

- Springboot 앱을 도커 이미지로 빌드
- Nginx 웹서버를 도커 이미지로 빌드
- docker-compose로 컨테이너 실행

### 상세 내용

- 현 프로젝트 Root 폴더에서 시작
- Springboot app
  - ./Dockerfile
    ```
    FROM openjdk:11-jre
	COPY build/libs/*SNAPSHOT.jar app.jar
	ENTRYPOINT ["java" , "-jar", "app.jar"]
    ```
  - ./docker build -t test-web:0.0.1 .
- Nginx 웹서버 
  - ./Dockerfile
    ```
    FROM nginx:stable-alpine3.17-slim
	RUN rm -rf /etc/nginx/conf.d/default.conf
	COPY ./deploy/nginx.conf  /etc/nginx/nginx.conf
	VOLUME ["/data", "/etc/nginx", "/var/log/nginx"]
	WORKDIR /etc/nginx
	CMD ["nginx"]
	EXPOSE 80
    ```
  - ./deploy/nginx.conf
    ```
    daemon off;
	user nginx;
	worker_processes auto;
	pid /run/nginx.pid;

	events {
	    worker_connections 768;
	}
	http {
	    proxy_set_header X-Real-IP $remote_addr;
	    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

	    access_log /var/log/nginx/access.log;


	    server {
	        listen       80;
	        listen      [::]:80;

	        server_name  "";

	        access_log off;

	        location / {
	            proxy_pass http://test-web-ondeploy:8080;
	            proxy_set_header Host $host:$server_port;
	            proxy_set_header X-Forwarded-Host $server_name;
	            proxy_set_header X-Real-IP $remote_addr;
	            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	        }
	    }
	}

    ```
  - ./docker image build -t test-nginx:0.0.1 -f Dockerfile_nginx .
- 컨테이너 실행
  - ./docker-compose up
- 브라우져에서 테스트
  - http://localhost/main
    - hello, world

