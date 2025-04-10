#docker #dockercompose #deploy #배포 

docker image를 배포할 때마다 build할 필요가 없는 것이었다.
아래 Dockerfile을 보자.

```yaml
FROM amazoncorretto:21-alpine3.19
ADD ./my-api.jar /app/my-api.jar
ENTRYPOINT ["java", "-jar", "/app/my-api.jar"]
```

`/app` 에서 `my-api.jar` 를 실행시키도록 작성했다.
그리고 docker-compose.yml에 아래 내용을 추가한다.

```yaml
	volumes:
      - /home/docker/my-api:/app
```

**BOOM!!!**

jar 파일이 위치한 폴더를 host의 폴더와 volume 매핑한 후,
그 host 폴더에 변경된 jar를 옮기기만 하면 끝이다.
그러면 docker container만 내렸다가 다시 올리기만 해도 변경된 jar로 구동한다.

**BOOM!!! BOOM!!! BOOM!!!**

와 여태 이걸 몰랐지. 그동안 매번 이미지 빌드했음.