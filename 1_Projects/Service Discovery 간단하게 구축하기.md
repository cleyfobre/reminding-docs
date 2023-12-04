#eureka #servicediscovery #springcloudeureka

> [!note]
> https://www.youtube.com/watch?v=-gLLeoS1m6s 이 영상을 요약한 문서이다.
> Git repo: https://github.com/cleyfobre/eureka-demo

MSA에서 8080 port의 A서비스가 8081 port의 B서비스를 호출하는 구조라고 가정해보자. 근데 8081 port의 B서비스가 너무 바빠서 10개의 인스턴스까지 스케일아웃을 했다. 그리고 그 중에 하 나가 포트 8085에 있다. 그러면 A서비스는 B서비스의 모든 인스턴스를 알고 있어야만 할까? 여기서 Service Discovery의 필요성이 생긴다.

#### Service Discovery 아키텍처

![service_registry](service_registry.png)

Service Discovery를 하는 것이 Service Registry라고 보면 될 듯 하다. 서비스들이 자신들의 주소를 다 등록하고, 다른 서비스를 호출하고 싶을 때 Registry를 통해서 주소를 얻어 호출한다.

> [!warning]
> 물론 쿠버네티스로 넘어가면 Eureka는 필요없어진다. 쿠버네티스가 대신 해주기 때문이다. 다만 이 개념을 미리 알고 구축해보는 경험은 필수라고 생각한다.

#### application.yml 세팅시 참고사항

eureka 서버든 클라이언트든, 아래와 같은 코드를 작성하게 될 것이다. 아래의 코드는 eureka server의 설정이다.

```yml
eureka:  
  client:  
    register-with-eureka: false  
    fetch-registry: false  
    service-url:  
      defaultZone: http://localhost:8080/eureka
```

- register-with-eureka
	- 해당 application은 eureka 서버에 등록될지 말지(registered or not)를 의미한다. false면 등록하지 않는다는 것을 의미한다. eureka 서버는 등록될 이유가 없기 때문에 false다. client들은 true로 설정한다.
- fetch-registry
	- eureka 서버에서 서비스 레지스트리를 받아올지(fetch)에 대한 값이다. 다른 클라이언트들의 정보를 받아올 application이라면 이 값을 true로 하며 eureka 서버는 다른 클라이언트 값을 받지 않으므로 (본인이 곧 레지스트리 이므로) false로 설정한다.



