#토스 #toss #gateway 

> [!tip]
> 해당 문서는 https://toss.tech/article/slash23-server 내용의 요약 문서입니다.

#### Gateway란?
라우팅 및 프로토콜 변환을 담당하고, 마이크로 서비스의 중개자 역할을 하는 서버이다. 보안과 모니터링을 위한 ==단일 제어 지점==을 제공한다. *(예: Netflix Zuul)*

서비스가 많고 트래픽이 많아지다 보니 Gateway 패턴이 생겨나게 되었고, ==필요한 유저 정보==와 ==보안 정책==, ==요청의 전처리 및 후처리== 등을 처리하고 이를 업스트림 서버로 넘겨준다.

요청이 오면 Route 단위로 설정된 것에 따라 처리되며, Route는 아래와 같이 둘로 나뉜다.
- Predicate : Path, Method, Host 등으로 요청을 구분한다.
- Filter : 요청의 전처리와 후처리를 담당한다.

#### 토스의 Gateway 스펙
- Spring Cloud Gateway
	- Webflux, Reactor-Netty
- Kotlin Coroutine
- Istio
	- Ingress / Egress Gateway
	- Envoy 필터

#### Sanitize
유저가 올바르지 않은 요청을 했을 때 이를 지우거나 올바른 값으로 바꿔주는 것을 말한다.

#### Passport
기존 서비스들은 기기 및 유저 정보가 필요할 때 유저 API를 호출해서 사용했었다. 하지만 이러한 구조는 중복 요청 및 리소스 낭비를 발생시켰다. 이를 개선하기 위해 Netflix의 Passport 구조를 채택하였다. ==Passport는 기기 및 유저 정보를 담은 토큰==이며, 트랜잭션 내 서비스들은 해당 토큰을 사용하여 기기 및 유저 정보를 얻는다.
![패스포트](toss_passport.jpg)

#### 종단간 암호화(E2EE)
토스 앱은 서버와 약속된 key를 사용해서 데이터를 암호화한다. 그리고 Gateway는 이를 복호화한다. 복호화 과정에서 인증/인가 과정을 거친다. 
![e2ee](e2ee.jpg)

#### Dynamic Security
토스앱은 내부적으로 매 요청을 서명할 아주 짧은 유효기간의 key와 변조되지 않은 오직 토스앱만 알 수 있는 정보를 가지고 각 요청을 서명하고 나서 Gateway로 보낸다.

Gateway는 그 값이 토스앱에서 만든 요청인지, 중복된 값인지, 유효기간이 만료되지는 않았는지 등을 검증한다. 앱 위변조, delayed request, replaying attack을 방지하고, 의심스러운 요청이 있을 때 FDS(Fraud Detection System)를 통해 계정을 비활성화한다.
![dynamic security](dynamic_security.jpg)

#### 인증서를 이용한 인증/인가
Gateway는 토스 앱 뿐만 아니라 토스 개발자, 외부 회사로부터의 접근도 허용해야 한다. 이는 클라이언트의 인증서를 이용한 mTLS API를 이용한다.

> [!todo] 아직 잘 모르는 내용이나 일단 쓴다
> Istio에서 제공하는 mTLS flow위에 Gateway 앱을 두어 인증/인가를 처리하고 있다. Istio만을 이용해서 할 수도 있지만, Gateway 앱처럼 코드 베이스로 하면 Istio의 matching rule보다 자유도도 높고, Auditing 로직도 처리할 수 있고, 카나리 배포의 이점을 누릴 수 있기 때문이다.

#### Circuit Breaker
내부 서비스 간에 서킷 브레이킹을 거는 것도 중요하나, 근원적인 트래픽을 발생시키는 클라이언트에게 백프레셔를 빠르게 주기 위해서는 Gateway에 서킷 브레이킹을 거는 것이 중요하다. 

> [!todo] 아직 잘 모르는 내용이나 일단 쓴다
> 서킷 브레이킹은 두 레이어에서 걸 수 있다. 인프라 레이어와 애플리케이션 레이어다. 먼저 Istio를 활용한 인프라 레이어의 서킷 브레이킹이 있으며, Resilience4J나 Hystrix와 같은 라이브러리를 이용한 앱 레이어의 서킷 브레이킹이 있다.
> 
> Istio를 활용하면 호스트 단위로 쉽고 빠르게 전체 적용이 가능하다. 하지만 호스트 단위로만 서킷 브레이킹을 걸 수 있고, 설정도 제한적이다. 토스는 각 애플리케이션이나 Gateway에 서킷 브레이킹을 적용하여 호스트나 Route 단위 혹은 기능 단위로 정교하게 서킷 브레이킹을 걸고 있다.

#### 모니터링
토스는 Gateway에 모니터링의 세 요소인 Logging, Metric and Tracing을 적용하고 있다. 

- Logging
	- Gateway에 지나는 모든 요청 및 응답의 Route id, method, URI, 상태 코드 등을 모두 Elasticsearch에 남기고 있다. 
- Metric
	- Metric에는 시스템에서 수집하는 메트릭과 애플리케이션에서 수집하는 메트릭이 있다. 두 메트릭 모두 Prometheus가 수집을 한다. Node Exporter를 통해 시스템 메트릭을, Spring actuator를 통해 애플리케이션의 메트릭을 수집하여 Grafana로 시각화하고 슬랙으로 알림을 보내고 있다.
	- 시스템 메트릭에는 CPU, memory, 네트워크 RX, TX 트래픽 등이 있고, 애플리케이션 메트릭에는 JVM 쓰레드블록 상황이나 세대별 메모리 할당, Full GC 발생 여부 감지 등이 있다.
	- Spring Cloud Gateway에서는 Route별 메트릭도 제공한다. 토스는 여기에 Path 값을 더해 API Path별로 Route 메트릭을 확인한다.