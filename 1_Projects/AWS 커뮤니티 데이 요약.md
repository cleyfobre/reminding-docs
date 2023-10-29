
AWS SAM
- code base aws infra settiing

Amazon bedrock 
- select 'text'
	- model as 'AI21 labs Jurassic-2 Ultra'
- copy the code for request
- paste that code in boto3 python code

Application composer
- Set infra as ppt
- It also provides template as aws same code

sam vs cdk
- ref pic

#### Session 1. AWS Infra

처음 vpc를 설계하고 만들 때
ipv4 cidr block: 10.0.0.0/16
이건 큰 대역이며 65535의 ip가 필요했음..

ipv4.ipv6 cidr 계산기 사이트에서 ip 몇개 가능한지 계산해주는 사이트
dev/prod 구분하게 설계
eks쓴다면 x2 배 대역

subnet구성?
- pub: nat
- private: eks worker
- private: db
- private: vpc endpoint

az를 많이 사용하면 비용 증가

eks 노드 그룹 선택할 때 fargate는... 사진참조
eks노드 사이즈 선택하기... 사진참조

eks oidc (openid connect)

eks대신 사용한다면?
- elastic beanstock or api gateway+lambda

#### Session 2. 카오스 엔지니어링 Fault Injection Simulator

넷플릭스는 2008년 데이터센텅  spof를 겪으며 3일간 dvd 배달을 못하는 상황 발생. 클라우드로 시스템 이전하도록 노력. 일부러 장애를 일으켜 spof에 대한 대응하도록 카오스 엔지니어링 시도.

- 안정 상태 정의
	- 사진 참고
- 운영 환경에서 실험.. 꼭 필요한건아님

#### Session 3. 서버리스 부하테스트 목적 및 사례

부하테스트
- 로드테스트 (일정 이상 부하 에서)
- 스파이크 테스트
- 스트레스 테스트 (점진적)
- 속(soak) 테스트

서버리스 부하 테스트 이유
- 서버리스 비용 미리 파악. 서버리스는 종량제이기 때문에 하루 비용을 알면 한달 비용을 알 수 있고, 한달 비용을 알면 1년 비용을 알 수 있음

AWS lambda 버스트 리밋 조정 가능

툴
- jmeter
- serverless artilerry
- distrubuted load test on aws
- locust

일본 방송 서비스: hod
- 5000tps의 스파이크를 버텨야 하는 목표

#### Session 4. Amazon EventBridge

이벤트 드리븐 아키텍쳐: 이벤트 기반으로 분리된 서비스를 연결 및 실행. MSA의 기본.
- aurora 에서 이벤트 발생 가능: 사진참조

이벤트? 명령이 아닌 관찰한 내용 다:다

이벤트 버스
- 이벤트를 받고 전달하는 라우터

클라우드워치 로그를 통해 이벤트를 발생시키는 것도 가능: 사진참고

ec2의 상태변경 -> 이벤트 브릿지.. 예를 들어 ec2 라지 인스턴스가 갑자기 켜진다거나하면 large 이상일 때 강제 중지하고 이메일 보내는 기능도 가능

s3파일 업로드 사진참고

파이프
- 1:1 이벤트
- 스트림 서비스들과 연동 가능
- 이벤트 강화.. 중간에 주소 api를 호출하고 다시 이벤트 리턴 가능

스케쥴 Cron
sqs delayseconds 큐가 들어간 후 몇 초 뒤 처리한다는 파라미터...

람다가 api gateway 소켓 통해 s3 업로드