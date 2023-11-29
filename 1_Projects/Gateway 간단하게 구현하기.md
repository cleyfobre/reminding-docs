#gateway #springcloudgateway

> [!note]
> https://github.com/cleyfobre/gateway-demo 레포지토리의 소스를 참고한다.

매우 간단한 Spring Cloud Gateway 예제이다. Gateway는 Downstream 서비스에 대한 정의를 사전해 해놓고, Client의 요청에 맞게 라우팅해주는 모듈이다. 또한 보안, 인증과 같은 Aspect 역할을 한다.

Downstream 서비스들의 정의는 아래와 같이 한다. Yaml 파일로 정의할 수 있고, Java Fluent API로도 정의할 수 있다.

#### Configure in application.yml

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: myservice
          uri: ${MYSERVICE_ROUTE_URI:http://localhost:6204}
          predicates:
            - Path=/test
          filters:
            - PrefixPath=/api
            - AddResponseHeader=X-Powered-By,Young Gateway
```

#### Configure with Java fluent API

```java
@SpringBootApplication
public class GatewayDemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(GatewayDemoApplication.class, args);
	}

	@Bean
	public RouteLocator routeLocator(RouteLocatorBuilder builder) {
		return builder.routes()
			.route(r -> r.path("/test")
				.filters(f -> f
					.prefixPath("/api")
					.addResponseHeader("X-Powered-By", "Young Gateway")
				)
				.uri("http://localhost:6204")
			)
			.build();
	}

}
```