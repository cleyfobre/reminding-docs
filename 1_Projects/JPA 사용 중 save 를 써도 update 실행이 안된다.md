#jpa #영속성 #persistence #save #update #insert

### 문제

일단 문제의 코드는 아래와 같이 두개의 Java 파일이 있다.

```java
@Service
@Transactional
public class MyService {
	...
	public MyDto save(MyDto myDto) {  
		    
	    My my = myMapper.toEntity(myDto);  
	    my = myRepository.save(my);
	    yourRepository.go(my.getId());  
	  
	    return myMapper.toDto(my);  
	}

}
```

```java
@Repository  
public interface YourRepository extends JpaRepository<Your, Long>, JpaSpecificationExecutor<Your> {

    @Modifying(clearAutomatically = true)  
    @Query(value = "update Your y set y.cnt = y.cnt + 1 where y.id = :id")  
    void go(@Param("id") Long id);
    
}
```

첫번째 코드는 새로운 dto를 insert하거나 update에 사용될 save 함수이다. insert할 때는 save도 잘 실행되고, 그 밑에 go 함수도 잘 실행된다. 근데 update를 실행할 때에는 이상하게 save 에서 날렸어야 할 update 쿼리가 안날아간다. 

### 문제 원인

원인은  `yourRepository.go()` 였다. 이 코드가 없으면 update 쿼리가 잘 실행된다. 근데 이 쿼리를 넣으면 실행이 안된다. 원인은 찾았으나 그래서 이게 무슨 이유 때문에 문제를 일으켰는지 알아야 한다.

go() 함수에는 `@Modifying(clearAutomatically = true)` 어노테이션이 붙어있다. 이 어노테이션은 해당 쿼리를 실행시킨 뒤 영속성 컨텍스트를 자동으로 flush 한다. clearAutomatically 속성이 true 이기 때문이다. 이는 DB와 엔터티의 상태를 일치하기 위함이다. 이 과정에서 앞선 update 쿼리가 커밋되지 않은 상태에서 영속성이 초기화되어버려 update 쿼리가 날아갔을 가능성이 크다. 

### 해결

@Modifying의 clearAutomatically 속성을 false로 할까했으나, go 함수를 다른 곳에서 쓰일 것을 대비하여 이 함수를 수정하는 것은 좋지 않은 방법인 듯 하다. 대신 Controller 에서 save를 하고, 그 다음에 go를 호출하면 다른 트랜잭션에서 실행될테니 문제가 해결된다.

```java
@PutMapping("/my")
public ResponseEntity<MyDto> updateMy() {
	...
	MyDto result = myService.save(myDto);  
	yourService.go(result.getId());
	...
}
```