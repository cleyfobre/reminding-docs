## AWS 데이터 분석 세미나

- 필요한 데이터
  - 트랙젝션 데이터(DB) 
  - 엑셀 및 클라우드(ex. Google Analytics)
  - 로그(Web/Was)
  - 외부 디바이스 데이터

- 목표
  - 개인화추천, BI, 푸시타게팅, 유의미한 SQL을 위한 데이터레이크 구축
  - AWS QuickSight 구축

- 데이터들은 분산되어 쌓이고는 있으나, 그것들을 합쳐 볼 수 있는 상황이 필요
  - 'S3 데이터레이크' 구축으로 해결
  - 데이터레이크? 데이터를 모으는 테크닉
  - 활용안: 개인화 추천, 발주 예측, 재고 예측, 주문 전환율, 고객 여정 행동 패턴 등등
  - 사진 찍은 거에서 가운데 (Athena, Glue and S3) 있는 것들이 데이터레이크 아키텍처
  - S3
    - 버킷 생성 (버킷이 곧 데이터레이크)
    - like HDD
  - Glue
    - Json, CSV, Parquet format 등등의 데이터 등에 검색이 쉽도록 테이블, 컬럼 등을 정의
    - 'Create a database' 진행
    - like dbms
  - Athena
    - Glue에서 정의된 메타데이터를 가지고 쿼리로 가져올 수 있음
    - like dbbeaver

- 케이스1: WEB/WAS 로그
  - WAS/WEB 서버의 EC2에 웹 로그가 계속 쌓이는 상황
  - Kinesis Firehorse 생성
  - 해당 EC2에 키네시스 Agent 설치 (like 파일비트 오픈소스)
  - Agent가 로그의 내용을 bucket에 저장
  - Athena에서 쿼리

- 케이스2: 트랙젠션 데이터(DB)
  - Lambda Connector 생성
  - Athena에서 쿼리

- 케이스3: GA 데이터
  - GA 빅쿼리 테이블 정보
  - 구글 인증키 AWS SM에 저장
  - Glue Connect 설정
    - Glue studio 에서 jobs 생성
  - S3 저장
  - Glue 테이블 설정 및 Athena 쿼리

- 케이스4: 엑셀 등 수작업 데이터
  - 구글 Sheet ID 확인
  - Lambda 함수에서 해당 Sheet 데이터 읽어서 Bucket에 저장
    - python 경우 gspread의 layer를 추가해서 'import gspread'
  - Glue 테이블 설정 및 Athena 쿼리
 
 - Data Lab 
   - AWS 오피스에서 직원들의 지원(?) 받아서 하나의 프로젝트를 (예를 들어, 데이터레이크 구축) 진행해볼 수 있고,
   - 바로 운영 배포한 경우도 있다고 한다.