#outbox #cqrs 

참고: https://ridicorp.com/story/transactional-outbox-pattern-ridi/

DB 업데이트와 이벤트 발행을 같은 트랜잭션 안에 하기 위한 패턴이다. DB에 outbox 테이블을 만들어서 insert 후, outbox 데이터를 읽어 이벤트 발행하는 것이 전체적인 틀이다.

글에서 공유하는 방법은 Message Relay 구현으로 polling을 통한 이벤트 발행이다.

중간에 참고할만한 전략들이 두개 정도 있다.

1. Redis 분산 lock을 통해 mysql record lock를 획득하는 전략
	1. 릴레이 노드가 2개로 운용되므로 중복된 메시지에 대한 동시성 처리를 위한 것이다.
	2. Redis 분산 lock은 빠르게 분산 트랜잭션을 통제할 수 있다. mysql만 썼을 때 db 서버의 부담을 줄여줄 수 있는 전략이다.
2. Select와의 경합을 줄이는 Delete 전략
	1. 같은 row에 대한 경합을 줄이기 위해 가장 최근 id값과 1000만큼 차이가 나는 row 까지에 대해서만 Delete 하는 전략이다.
	2. 