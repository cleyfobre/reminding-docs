#2차캐시 #jpa #spring #hibernate 

### 두가지가 문제가 되었다.
- 첫번째 문제
	- DB에서 데이터를 아무리 바꿔도 (dbeaver에서 강제로 바꿈) API에서 조회할 때 값 변경이 없음... 심지어 App을 껐다 켜도 안됨. 하루 정도 지나면 적용됨. 
- 두번째 문제
	- 원래 테이블에 새로운 컬럼을 추가했는데 계속 Null로 뜸.

> [!tip] 결론부터 말하면
> 첫번째 문제만 JPA 2차 캐시와 관련된 문제였음. 두번째 문제는 entity를 response 타입으로 파싱하는 과정에서 코드리딩을 잘못하여 삽질한 거였음. 결국 getter와 setter를 안해준 문제였음.

### 문제가 되었던 코드

```java
@Entity
@Table(name = "my_entity")
@Cache(usage = CacheConcurrencyStrategy.READ_WRITE)
@Getter
@Setter
@ToString
public class MyEntity {  

	// your codes

}
```

