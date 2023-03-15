## Kafka

### Concept(영상1-2)

- 기본 구조
  - 프로듀서: 데이터 보내는 자
  - 카프카 서버(= 카프카 브로커): 데이터 저장하는 자
  - 컨슈머: 데이터 사용하는 자

- 핵심 용어
  - 주키퍼
    - 카프카 실행 환경 및 리소스들의 관리를 담당하는 어플리케이션

  - 토픽
    - 브로커가 가지고 있는 데이터의 '카테고리'
    - rdb에서 테이블과 비슷한 개념임

  - 파티션
    - 토픽은 여러 파티션으로 나뉜다.
    - 파티션이 여러개로 나뉜다는 건 카프카 클러스터 안에 있는 데이터들은 여러 노드(서버 혹은 디바이스)에 저장되어 있다는 것을 의미한다.
    - 이 때문에 카프카가 분산 시스템이라고 하는 것이다.

  - 오프셋
    - 데이터의 id. 시퀀셜하다.


### 설치 및 실행(영상 3-4)

- 카프카 시작 (자세한 사항은 카프카 도큐먼트 참고)
  1) 다운로드 in ubuntu (영상에 아래 내용 X)
    - sudo apt-get update
    - sudo apt-get install default-jre
    - sudo apt-get install zookeeperd
    - curl "https://archive.apache.org/dist/kafka/3.2.1/kafka_2.13-3.2.1.tgz" -o ./kafka.tgz
    - tar -xvzf ~/Downloads/kafka.tgz --strip 1
      - '–strip' 1 is used to ensure that the archived data is extracted in '~/kafka/.'
  2) 주키퍼 실행
    - sh ./bin/zookeeper-server-start.sh config/zookeeper.properties
  3) 카프카 서버 실행 (기본 9092 포트에서 실행됨)
    - sh ./bin/kafka-server-start.sh config/server.properties
  4) cmd로 토픽 생성
    - sh ./bin/kafka-topics.sh --create --topic TOPICNAME --bootstrap-server localhost:9092
  5) 토픽 리스트 확인
    - sh ./bin/kafka-topics.sh --list --bootstrap-server localhost:9092
    - sh ./bin/kafka-topics.sh --describe --topic TOPICNAME --bootstrap-server localhost:9092
  6) 내장된 프로듀셔로 이벤트 전송
    - sh ./bin/kafka-console-producer.sh --topic TOPICNAME --bootstrap-server localhost:9092
  7) 내장된 컨슈머로 순서대로 메시지 읽기(cmd상 --from-beginning)
    - sh ./bin/kafka-console-consumer.sh --topic TOPICNAME --from-beginning --bootstrap-server localhost:9092

### 여기 까지 테스트

- 환경
  - 우분투 도커 컨테이너
  - 로컬 스프링 어플리케이션
- 이슈
  - 상황: 도커에 9092 포트포워딩을 했으나, 로컬에서 접속이 안되는 상황
  - 해결
    - 방화벽 상관 없음 (어차피 내부망 문제)
    - 도커 컨테이너 네트워크랑 상관 없음
    - 카프카 서버 실행할 때 필요한 config/server.properties를 수정해야 함.
    - vi server.properties 실행
    - advertised.listeners=PLAINTEXT://127.0.0.1:9092 로 변경
    - 로컬에서 테스트하면 잘 됨!!

### 스프링부트 + 카프카 프로젝트 구성(영상5-6)

- 디펜던시에 Spring for apache kafka 추가
- 스프링부트가 아닌 스프링을 사용할 시, 카프카에 대한 config 추가가 많을것임
- 예제에서는 한 어플리케이션에서 프로듀서랑 컨슈머 둘 다 정의하는 것인듯.



### Ref
 - https://www.youtube.com/playlist?list=PLGRDMO4rOGcNLwoack4ZiTyewUcF6y6BU
 - https://hevodata.com/blog/how-to-install-kafka-on-ubuntu/
 - https://itstudy402.tistory.com/59