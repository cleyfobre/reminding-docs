#jpa #nplus1 #eager #lazy #fetchtype

> [!note]
> 다음 블로그를 정리한 문서입니다. https://velog.io/@jinyoungchoi95/JPA-%EB%AA%A8%EB%93%A0-N1-%EB%B0%9C%EC%83%9D-%EC%BC%80%EC%9D%B4%EC%8A%A4%EA%B3%BC-%ED%95%B4%EA%B2%B0%EC%B1%85


JPA N+1 문제는 원래 얻고자 했던 쿼리 하나에 대해 N개의 쿼리가 발생할 수 있는 문제를 말한다. 예를 들어 아래와 같은 Entity가 있다고 하자.

```java
@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(length = 10, nullable = false)
    private String name;

    @OneToMany(mappedBy = "user", fetch = FetchType.EAGER)
    private Set<Article> articles = emptySet();

}
```

User는 여러 개의 Article을 가지고 있을 수 있기 때문에 하나의 User를 얻기 위해서 User 테이블에 대한 쿼리와 Article 테이블에 대한 N개의 쿼리를 발생시킨다. 특히 컬럼에 FetchType을 Eager로 설정했을 경우, User에 대한 조회가 일어나는 즉시 Eager 타입으로서의 Article에 대한 조회가 N번 일어난다. 아래와 같은 코드를 작성했을 때 말이다.

```java
List<User> users = userRepository.findAll();
```

> [!danger] XToMany에서 즉시 로딩(EAGER)은 절대절대 쓰지 말자!

그렇다면 LAZY 로딩은 안전할까? 일단 LAZY는 실제 코드 상에서 해당된 컬럼을 사용하기 전까지는 쿼리를 진행하지 않는다는 점에서 EAGER 보다는 안전하다. 다만 실무를 하다보면 해당 컬럼을 언제든지 사용할 수 있는 상황에 놓여지고, 이는 곧 N+1 문제를 야기한다. 아래와 같은 코드가 충분히 나올 수 있다.

```java
List<User> users = userRepository.findAll();
for (User user : users) {
	int cnt = user.articles().size();
	...
}
```

