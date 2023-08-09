### 삽질

- 환경
  - 람다, rds(postgresql), python

- 문제
  - postgres 사용가능한 layer 추가 후 연동 시도했으나 안됨...

- 체크한 사항
  - iam role 다 체크함
    - AWSLambdaBasicExecutionRole-b3d17307-8054-4a0f-b16e-0a7e65e0eef0
    - AmazonEC2FullAccess
    - AmazonRDSFullAccess
    - AWSLambdaVPCAccessExecutionRole
  - vpc도 같은지 체크함
  - 인바운드도 체크함
    - 같은 시큐리티 설정해놓고, 인바운드에 자기 시큐리티그룹 5432 포트에 설정해놓음.

- 위에 사항 다 체크했는데도 안됨...

- 해결
  - 아웃바운드가 특정 포트에 걸려있었음.
  - 보통 아웃바운드 0.0.0.0/0으로 열려있어서 확인안했었음. 혹시나 하고 봤는데 역시나 체크해줘야 했음
  - 람다에 다른 시큐리티 그룹으로 세팅해놓고, rds의 시큐리티그룹에 인바운드로 추가해줬음.