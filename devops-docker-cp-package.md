## docker cp, python3.9 and so on

- 목표: aws에 python3.9 가상환경의 lambda function을 만들었는데, pymysql이 설치가 안되어 있어서 이를 해결하고자 함.
- 과정: 
  - 로컬에서 docker로 pymsql을 설치하여 zip파일로 만든다.
  - 해당 zip파일을 lambda에 새로운 layer(계층)로 추가한다.
  - 이미 만든 lambda function에 나의 layer를 적용시킨다.
  - code에서 pymysql을 사용할 수 있다.
- 