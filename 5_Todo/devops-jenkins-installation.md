## Jenkins installation

- jenkins latest 설치
- openjdk1.8 설치
- sudo systemctl start jenkins
- 결과: 안됨...
- 구글링 결과: jdk 버전, 포트 등등 다해봤지만 안됨...
- 그냥 다 안됨.
- 혹시나 해서 jenkins 구버전 설치해보려고 시도.
- su root 상태에서 https://mirrors.jenkins-ci.org/war-stable/2.346.1/jenkins.war 했는데, 그런 파일이 없다고 그럼.
- 있는데 왜 없대?
- su ec2-user로 하니까 됨... 왜지?
- 다운받고 sudo systemctl start jenkins 실행. 안됨.
- 가이드 보니까 yum install로 다시 받고, wget으로 구버전 받은걸 {mv 구버전war 원래path의 war} 해줘야 한다고 함.
- 그래서 다시 yum install 최신 jenkins 받음.
- 근데 아까랑 다운로드될 때의 log가 다르다. 아래와 같다.
```
Dependencies Resolved

========================================================================================================================
 Package                    Arch                      Version                          Repository                  Size
========================================================================================================================
Installing:
 jenkins                    noarch                    2.375.3-1.1                      jenkins                     90 M

Transaction Summary
========================================================================================================================
Install  1 Package
```

- 혹시나 해서 최신버전 그대로 실행해봄. sudo systemctl start jenkins
- 됨.
- 기쁨.
- 5시간 삽질.
- 되긴했는데 이유를 몰라서 히스토리를 쫙 써봄...
- jenkins를 받고 jdk를 받은게 문제인건지?
- 다시 jdk11로 받았는데 적용이 안된건지?
- root에선 안되고 사용자 권한일 땐 다운로드 되는지?
- 등등 의문점이 많지만... 일단 나중을 위해 적어봄