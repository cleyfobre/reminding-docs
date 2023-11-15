#jpa #criteria #hibernate

코드로 쿼리를 구성하는 API이며, JPQL 대신 자바 코드를 사용한다.
```
CriteriaBuilder cb = em.getCriteriaBuilder();
CriteriaQuery<Review> cq = cb.createQuery(Review.class);
Root<Review> root = cq.from(Review.class);
cq.select(root);
cq.where(cb.equal(root.get("writer"), "Youngmin"));
cq.orderBy(cb.asc(root.get("id")));

TypedQuery<Review> query = em.createQuery(cq);
List<Review> reviews = query.getResultList();
```

#### 검색 조건
검색조건은 CriteriaQuery#where()로 지정한다. 파라미터로 들어갈 Predicate는 cb를 이용해서 생성한다. 검색 조건에 사용할 엔티티 속성은 Root#get() 메서드로 구한다.
```
Root<Review> root = cq.from(Review.class);
Predicate predicate = cb.equal(root.get("writer"), "Youngmin");
cq.where(predicate);
```

아래와 같이 여러 개의 Predicate를 조합할 수 있다.
```
Predicate p1 = cb.equal(root.get("writer"), "Youngmin");
Predicate p2 = cb.greaterThan(root.get("createdDate"), LocalDateTime.now());
Predicate predicate = cb.and(p1, p2);
cq.where(predicate);
```

#### 정렬
정렬은 CriteriaQuery#orderBy()로 지정한다. 파라미터로 들어갈 Order는 cb를 이용해서 생성한다. 한개 이상의 정렬이 지정 가능하다.
```
Order idAsc = cb.asc(root.get("id"));
Order createdDateDesc = cb.desc(root.get("createdDate"));
cq.orderBy(idAsc, createdDateDesc);
```

#### Root#get()과 Generic Type
In절을 만들고자 할 때 미리 Generic Type을 지정해놓자. 런타임 때 오류나는 것 보다는 컴파일 때 오류나는 것이 차라리 나으니 말이다. 일단 아래와 같이 하면 컴파일 때 오류가 나지 않는다.
```
QueryBuilder.In<Object> statusIn = cb.in(root.get("status"));
statusIn.value(1).value("2"); // 런타임 시점에 오류
```

아래와 같이 Generic Type을 명시하면 컴파일 때 오류를 잡을 수 있다.
```
QueryBuilder.In<Integer> statusIn = cb.in(root.get("status"));
statusIn.value(1).value("2"); // 컴파일 시점에 오류
```

#### Specification
Spring Data JPA에서는 Criteria를 활용하여 쿼리를 비교적 깔끔한 코드로 만들 수 있게 해준다. [[Spring Data JPA Specification 정리]] 를 참고한다.