## docker private registry 구축하기

### 목표

- docker hub가 아니라 on-premise에 적합한 private registry 구축하기

### 단계
- 로컬에 registry image 풀받고 실행
  - docker run -d -p 5000:5000 --restart always --name myregistry registry:2
- 내 이미지에 registry 태그 달기
  - docker tag myimage localhost:5000/myimage
- 로컬 registry에 내 이미지 푸시
  - docker push localhost:5000/myimage
- 로컬 registry에 푸시한 이미지를 풀받기
  - docker pull localhost:5000/myimage
  - docker pull {ip address}:5000/myimage
- 내 registry 확인하기
  - curl -ik http://localhost:5000/v2/_catalog
  