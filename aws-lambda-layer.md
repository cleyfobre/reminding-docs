## aws lambda layer

- 목표: aws에 python3.9 가상환경의 lambda function을 만들었는데, pymysql이 설치가 안되어 있어서 이를 해결하고자 함.
- 과정: 
  - 로컬에서 docker로 pymsql을 설치하여 zip파일로 만든다.
  - 해당 zip파일을 lambda에 새로운 layer(계층)로 추가한다.
  - 이미 만든 lambda function에 나의 layer를 적용시킨다.
  - code에서 pymysql을 사용할 수 있다.

- pymysql layer zip파일 만들기
  - ubuntu 컨테이너 실행
    - docker run -it ubuntu:22:04
  - ubuntu 컨테이너 접속
    - apt update
    - apt install software-properties-common -y
    - add-apt-repository ppa:deadsnakes/ppa
    - apt install zip -y
    - apt install python3.9 -y
    - apt install python3-pip -y
    - mkdir -p layer/python/lib/python3.9/site-packages
    - python3.9 -m pip install pymysql -t layer/python/lib/python3.9/site-packages/
    - cd layer
    - zip -r mypackage.zip *
  - 컨테이너를 빠져나옴  
    - 작업한 ubuntu 컨테이너 id 확인하기
      - docker ps -a
      - 그 결과 예를 들어 abcd가 나왔다면,
      - 아래와 같은 명령어로 zip파일을 host로 복사해온다.
      - docker cp abcd:/layer/mypackage.zip .
  - 이제 AWS lambda 콘솔에서 레이어 추가하면 된다.