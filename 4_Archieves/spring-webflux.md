## Spring Webflux

### What is Spring Webflux?

- 기존 Spring MVC는 대표적인 blocking 코드로 구현되어 있음.
- '리퀘스트 당 하나의 쓰레드'(one thread per request one)의 디자인
  - 비효율적인 리소스 사용. 하나의 쓰레드의 작업 중에는 중간에 유휴시간이 존재하는 구조.
  - 더군다나 해당 작업이 다 끝나야 다른 리퀘스트 작업을 진행할 수 있음.

- webflux 구축은 공홈 가이드를 보고 했으나, 여기 블로그 내용도 많이 참고함.
  - How to Build High Performance API Client Using Reactive Feign: https://blog.devgenius.io/how-to-build-high-performance-api-client-using-reactive-feign-bae4b5053d12
  - Uplift Your Java Spring Boot To WebFlux Non-Blocking Application: https://blog.devgenius.io/uplift-your-java-spring-boot-to-webflux-non-blocking-application-7b207999e669

- 쉽게 놓칠 수 있는 부분
  - Webflux로 구축해도 DB 연결이나 3rd 파티 연동시 혹은 그밖에 내부 로직 상에서 blocking 코드가 있다면 Webflux를 쓰나마나다.
  - 특히 내가 해볼 것은 OpenFeign을 통한 교통공단 API 콜이다.
  - 기존 OpenFeign은 Reactive가 지원되지 않기 때문에 오히려 Spring MVC가 더 유리한 상황이다.
  - 때문에 Webflux에서는 Reactive를 지원하는 OpenFeign 라이브러리가 필요했다.
    - PlaytikaOSS/feign-reactive: https://github.com/PlaytikaOSS/feign-reactive
    - Spring에서 공식적으로 지원하기 전까지는 해당 라이브러리를 사용하도록 추천했다.

### 간단한 예제

- 두번의 Select 행위로 '가격'과 '할인' 데이터를 가져오고, 이 두 값을 계산하는 로직이 필요하다.
- Spring MVC에선 보통 이런 코드로 구현한다. 모든 코드는 blocking이다.
```
Product product = productRespository.findById(productId);
Promotion promotion = promotionRepository.findByid(promotionId);
Double productPrice = product.getPrice() — promotion.getDiscount();
```
- 같은 코드를 reactive로 구현하면 이렇다.
```
Mono<Product> product = productRepository.findById(productId);
Mono<Promotion> promotion = promotionRepository.findById(promotionId);
Mono<Double> productPrice = Mono
	.zip(product, promotion)
	.map(tuple -> 
		tuple.getT1().getPrice() - tuple.getT2().getDiscount())
	.subscribe(p -> { 
    	System.out.println(p.doubleValue());
	});
```
- You might observe that the return objects are wrapped inside a class Mono, it is a publisher which returns retrieved data to subscribers.
  - zip()은 두 Mono 객체 이상의 결과를 하나의 Mono로 Combine하는 함수다.
- We can form a data flow to calculate price using the publishers as data source.

### NHN FORWARD 2020 내가 만든 WebFlux가 느렸던 이유

- 영상: https://www.youtube.com/watch?v=I0zMm6wIbRI 
- 뷰 포인트
  - 성능 테스트 참조하기
  - 실수가 나왔던 부분 참고하기
  - 기본적인 non blocking 및 webflux 구조에 대한 설명 참고하기