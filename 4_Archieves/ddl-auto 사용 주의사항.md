#ddlauto #appplicationyml #jpa #hibernate 

ddl-auto는 무조건 none으로 한다. create나 update를 하면 인생 끝날 수 있다. 애플리케이션을 실행할 때 모든 테이블을 drop and create를 하는 행위이기 때문이다. 주의해야 한다.

```yml
# application.yml
spring:
	jpa:
		hibernate:  
			ddl-auto: none
```

