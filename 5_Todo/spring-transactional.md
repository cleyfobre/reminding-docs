## Spring @Transactional

- 만약 @Transactional이 붙은 메소드가 호출되면
- 내부적으로 메소드에 프록시(실질적으로는 객체)를 생성하고
- 트랜잭션을 시작한 다음에
- 원래 호출하려던 메소드를 호출하고
- 정상 완료되면 커밋, 익셉션 뜨면 롤백을 수행하면서
- 트랙잭션을 끝낸다.
- 해당 어노테이션에 대한 다양한 옵션들이 있지만...
- 격리(isolation)나 전파(propagaion)에 관한 옵션들이 있지만
- 스킵이요.