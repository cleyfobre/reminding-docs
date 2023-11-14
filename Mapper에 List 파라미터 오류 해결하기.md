#todo 

아래 코드에서 파마리터를 List 만 넘기면 아래와 같은 에러가 난다. 참고로 SQL Class에는 countModemInfoListDto2와 이름이 비슷한 getModemInfoListDto이 있다. 리스트를 가져오는 쿼리이다. 문제가 되는 쿼리는 이 쿼리의 count 쿼리일 뿐이다.

- 에러 내용
```sh
Parameter 'partnersIds' not found. Available parameters are [collection, list]
```

- 코드
```java
// service
int cnt = modemInfoMapper.countModemInfoListDto2(partnersIds, pageable);

// mapper
@SelectProvider(type = ModemInfoSql.class, method = "countModemInfoListDto2")  
int countModemInfoListDto2(List<Integer> partnersIds, Pageable pageable);

// sql
public String countModemInfoListDto2(List<Integer> partnersIds, Pageable pageable) {  
SQL query = new SQL();  
query.SELECT("count(v.vehicles_id)");  
query.FROM("(" + vehiclesVw(partnersIds) + ") as v");  
query.INNER_JOIN("(" + modemInfo() + ") as mi on v.serial = mi.serial");  
return query.toString();  
}
```

