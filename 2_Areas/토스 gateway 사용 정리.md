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