#aws #awscommunityday2024 #awscommunityday 

#### Platform Engineering
- 시작
	- **플랫폼**을 **제품**처럼 다룬다.
	- 셀프 서비스로 제공한다.
- 사례
	- spotify backstage: 오픈소스 개발자 포탈
	- NASA 데이터 분석 플랫폼

> [!check] AIMatics에서 사용했던 모듈이 생각남.
> Name 서버였음. 사용중인 모듈의 Name을 가져와서 요청하는 것. 살아있는지 체크가 가능하고, 연결할 수 있도록 하는 것인데, Service Discovery와 비슷한 거였음.

#### CloudFormation & StackSets

1. CloudFormation
	1. AWS가 제공하는 IaC도구. 사용자가 template를 정의해서 CloudFormation에 업로드하면 AWS가 Resource를 생성해준다.
2. StackSets
	1. Resource들의 묶음. Organization 계정에서 동시 여러 계정에 리소스를 배포할 때, StackSets를 사용하면 한번에 배포할 수 있다.
	2. 하지만 동시에 여러 계정에 배포할 일은 현재 없기에 개념만 알고 간다.

> [!note] 세션 요약
> 해당 세션에서는 여러 계정에 StackSets로 묶어 배포하고 나서, 배포가 완룔된 EC2의 IP를 S3 버킷에 넣는 일련의 과정을 소개해주는 내용이었다. 과정 중에 Lambda 함수도 사용되었다.

#### 올리브영 KafkaCDC 활용한 DB 마이그레이션

오프라인 매장 DB를 온라인 매장 DB에서 팔 수 있도록 DB를 구축하는 일. 오프라인 DB에서 중간에 배치를 통해 온라인 DB에 잘 넣는 일이 필요하다. 
- EAI 툴을 사용. (https://velog.io/@ryuhyewon/EAI%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)

| 구분                     | JDBC Source Connector                       | Debezium Source Connector                 | Confluents / Oracle GoldenGate       |
|--------------------------|---------------------------------------------|-------------------------------------------|--------------------------------------|
| **장점**                 | 구축시간 단축                                | 오픈소스 및 다양한 DB 지원                  | 성능 및 안정성                         |
|                          | 안정성 (Query-based)                       | Community 활성화 / Version Release       |                                      |
| **단점**                 | DB 부하 (= Batch)                           | 구성 및 검증 시간                         | 높은 라이센스 비용                      |
|                          | DELETE 추적 불가                           | Oracle Live DB 적용 사례 부족               | 솔루션 의존적                           |

올리브영은 Debezium Connect를 사용했다. 온라인몰에서 사용할 DB는 AWS Athena를 통해 쿼리해서 사용. Aurora DB와 Document DB 다 사용한 것 같음. 

> [!danger] SanpShot 수행시 DB Lock이 발생할 수 있어서 권한 제외 추천(Connect Config 구성)

#### GroundX AWS 장애 알림 서비스

- 장애가 났을 때
	- AWS Connect(전화 서비스)
	- AWS End User Messaging(문자서비스)
- 장애 알람이 발생해서 Slack 메시지를 받아도 다시 인프라팀에게 무슨 문제인지 다시 물어보는 불필요한 상황이 발생함.
	- AWS Bedrock을 이용한 장애 원인 및 조치 방안 분석
	- 장애가 발생하면 AWS Bedrock에 문제점과 프롬프트가 전달되고, LLM 답변이 전달된다.
	- blossomops, rds도 같이 활용
	- 초동조치에 좋은 활용도가 있음. ![[Pasted image 20241102155515.png]]
- Finops says, Showback란 데이터를 활용하여 이해관계자들에게 '거의 실시간'으로 확인가능한 프로세스를 제공
	- 모든 관계자들에게 보여주도록. 사무실 정면에 누구나 볼 수 있도록 모니터를 설치하여 제공. 장애에 대한 거부감 감소. 결국 주도적인 시스템 이상 확인과 적극적인 조치
	- 장애 Showback을 통한 사내 인식 개선 캠페인

#### 백패커 보안 위협 방어하기

OWASP API Security TOP 10
- link: https://owasp.org/API-Security/editions/2023/en/0x11-t10/

추천하는 AWS 툴
- Amazon Q Developer: 비즈니스 로직을 짜더라도 학습을 안한다.
- Amazon CodeGuru Reviewer
- Amazon CodeGuru Security





