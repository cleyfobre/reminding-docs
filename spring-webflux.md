## Spring Webflux

### What is Spring Webflux?

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

### NHN FORWARD 2020 내가 만든 WebFlux가 느렸던 이유

- 영상: https://www.youtube.com/watch?v=I0zMm6wIbRI 
- 뷰 포인트
  - 성능 테스트 참조하기
  - 실수가 나왔던 부분 참고하기
  - 기본적인 non blocking 및 webflux 구조에 대한 설명 참고하기