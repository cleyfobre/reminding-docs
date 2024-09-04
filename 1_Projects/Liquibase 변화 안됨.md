#liquibase #database 

나의 API 서버는 Liquibase를 사용 중이다. 현재 젠킨스를 이용하여 Dev와 Stage에 각각 배포하고 있다. 무언가 수정 사항이 있으면 SQL 파일을 만들고, changesql.xml에 한 줄 추가하여 서버 부팅시 Liquibase가 작동하도록 했다. 

그런데 문제가 생겼다. Stage에는 나의 추가된 SQL을 잘 반영했으나, Dev에는 그렇지 못했다. 아래와 같이 쿼리를 실행했다.

```sql
SELECT * FROM DATABASECHANGELOG d ;

-- Dev 결과 (예시)
-- 1 20240901
-- 2 20240902

-- Stage 결과 (예시)
-- 1 20240901
-- 2 20240902
-- 3 20240904
```

나의 최신 SQL이 반영되지 못했음을 확인했다. 위에 결과(예시)를 보면 Stage에는 3이 있지만 Dev에는 2까지 밖에 없다.

나는 아래와 같이 해결했다.

```sql
UPDATE DATABASECHANGELOGLOCK SET LOCKED=0 WHERE ID=1;
```

ChatGPT는 이렇게 이야기를 한다. Liquibase uses a lock mechanism to ensure that only one instance is applying changes at any time. If a lock is held, subsequent changesets won't run.

과거 LOCK이 걸린 히스토리가 있었던 것이고, 그 이후로 업데이트를 진행하지 않았던 것이다.