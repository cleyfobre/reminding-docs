## docker registry

### private registry

- registry image
  - docker run -d -p 5000:5000 --restart always --name myregistry registry:2
- 태그 달기
  - docker tag myimage localhost:5000/myimage
- docker push localhost:5000/myimage
- docker pull localhost:5000/myimage
  - docker pull {ip address}:5000/myimage
- 내 registry 확인하기
  - curl -ik http://localhost:5000/v2/_catalog
  