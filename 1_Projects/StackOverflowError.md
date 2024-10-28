#stackoverflow

아래 코드에서 스택오버플로우(stackoverflow) 에러가 났다.

### 에러 내용

```
Caused by: java.lang.StackOverflowError: null 
	at com.example.MyRepositorySupport.getEntityManager
	at com.example.MyRepositorySupport.getEntityManager
```

### 코드 

```java
@Repository
public class MyRepositorySupport extends Querydsl4RepositorySupport { 

	// something...
	 
	public EntityManager getEntityManager() { 
		return getEntityManager(); 
	} 
	
	// something...
}

@Repository
public abstract class Querydsl4RepositorySupport {

	private EntityManager entityManager; 
	
	@Autowired 
	public void setEntityManager(EntityManager entityManager) {
		this.entityManager = entityManager; 
	}

	protected EntityManager getEntityManager() {  
	    return entityManager;  
	}
	
	// something...
}
```

### 해결책

MyRepositorySupport.java에서 getEntityManager()를 아래와 같이 변경한다. 이렇게 함으로써 entityManger 객체를 리커시브하게 호출하지 않는다.

```java
public EntityManager getEntityManager() { 
	return super.getEntityManager(); 
} 
```

### 왜 StackOverflow?

### 왜 자식의 getEntityManager()에서만 리커시브?

### 왜 부모의 getEntityManager()에서는 리커시브 아님?

