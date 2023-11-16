
#mybatis #entity #jpa #embeddedid

Entity에서 두 개 이상의 필드를 id로 정하고 싶을 때 주로 아래와 같이 EmbeddedId를 적용한다.

```java
@Entity
@Table(name = "Table")
public class Table {

	@EmbeddedId
	private TableId id;
	  
	@Column(name = "subject")
	private String subject;
}

@Embeddable
public class TableId {

	@Column(name = "name")
	private String name;
	
	@Column(name = "phone_number")
	private int phoneNumber;
	
}
```

이렇게 Entity를 구성했을 때 EmbeddedId는 JPQL에서 그냥 쿼리를 할 때 알아서 잘 적용되지만, MyBatis를 사용하여 Mapper를 설정할 때에는 아래와 같이 직접 매핑해줘야 한다. 

```java
@SelectProvider(type = TableSql.class, method = "getMyTables")
@Results(value = {
@Result(property = "id.name", column = "name"),
@Result(property = "id.phoneNumber", column = "phone_number"),
@Result(property = "subject", column = "subject")
})
List<Table> getMyTables();
```

