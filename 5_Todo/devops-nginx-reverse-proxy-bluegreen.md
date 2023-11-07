## nginx reverse proxy for blue/green deploy

### 목표

- blue/green 무중단 배포
- 쉘스크립트 하나로 배포할 수 있도록 작성
- 젠킨스 구성에 적용

### 쉘스크립트 하나로 배포할 수 있도록 작성

- Dockerfile_nginx
```
FROM nginx:stable-alpine3.17-slim
ARG COMPOSE_COLOR

RUN rm -rf /etc/nginx/conf.d/default.conf
RUN echo "nginx image will be generated for : $COMPOSE_COLOR"
COPY nginx.$COMPOSE_COLOR.conf  /etc/nginx/nginx.conf
VOLUME ["/data", "/etc/nginx", "/var/log/nginx"]
WORKDIR /etc/nginx
CMD ["nginx"]
EXPOSE 80
```

- docker-compose.blue.yml
```
version: "3"
services:
  test-web-ondeploy:
    image: test-web:0.0.1
    container_name: test-web-blue
    environment:
      - LANG=ko_KR.UTF-8
      - UWSGI_PORT=8000
    ports:
      - 8081:8080
```
  - green은 container_name 뒤에 green이고 port가 8082이다.

- nginx.blue.conf
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

        access_log off;

        location / {
            proxy_pass http://host.docker.internal:8081;
            proxy_set_header Host $host:$server_port;
            proxy_set_header X-Forwarded-Host $server_name;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
}

```
  - green은 proxy_pass http://host.docker.internal:8082; 이다.

- deploy.sh
```
#!/bin/bash

# Blue 를 기준으로 현재 떠있는 컨테이너를 체크한다.
NO_BLUE=$(docker-compose -p test-web-blue -f docker-compose.blue.yml ps | grep Up)

# 컨테이너 스위칭
if [ -z "$NO_BLUE" ]; then
    echo "blue up"
    docker-compose -p test-web-blue -f docker-compose.blue.yml up -d
    BEFORE_COMPOSE_COLOR="green"
    AFTER_COMPOSE_COLOR="blue"
else
    echo "green up"
    docker-compose -p test-web-green -f docker-compose.green.yml up -d
    BEFORE_COMPOSE_COLOR="blue"
    AFTER_COMPOSE_COLOR="green"
fi

sleep 10

# check new container is up
IS_UP=$(docker-compose -p test-web-${AFTER_COMPOSE_COLOR} -f docker-compose.${AFTER_COMPOSE_COLOR}.yml ps | grep Up)
if [ -n "$IS_UP" ]; then

    # if no nginx, run new nginx container
    NO_NGINX=$(docker ps | grep test-nginx)
    if [ -z "$NO_NGINX" ]; then
        docker image build -t test-nginx:0.0.1 -f Dockerfile_nginx . --build-arg COMPOSE_COLOR="${AFTER_COMPOSE_COLOR}"
        docker run -d -p 80:80 --name test-nginx test-nginx:0.0.1
        echo "nginx started"
    else
        docker cp ./nginx.${AFTER_COMPOSE_COLOR}.conf test-nginx:/etc/nginx/nginx.conf
        docker exec test-nginx nginx -s reload
        echo "nginx reloaded"
    fi

    # 이전 컨테이너 종료
    docker-compose -p test-web-${BEFORE_COMPOSE_COLOR} -f docker-compose.${BEFORE_COMPOSE_COLOR}.yml down
    echo "$BEFORE_COMPOSE_COLOR down"
fi
```

### 젠킨스 구성에 적용