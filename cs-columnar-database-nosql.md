## columnar(열기반) database

### what is columnar database?

### nosql?
- nosql은 더 범용적인 용어. 디비에서 정보를 가져오거나 수정할 때 전통적인 sql을 쓰지 않는다는 점에 집중한 표현.
- key-value 스토어, 도큐먼트, 그래프, 열기반 db 등등 모두 nosql이다.
- nosql 예시
	- Key-value: Redis, Aerospike, Riak, Voldemort
	- Document: MongoDB, Elasticsearch, Couchbase, Amazon DocumentDB, Apache CouchDB
	- Columnar: Apache Cassandra, Google Bigtable, Apache HBase, Amazon Redshift

### columnar(열기반) vs relatioinal(관계형)
- 자주 업데이트를 한다면 '관계형 db'
- 큰 볼륨을 조회한다면 '열기반 db'

### 왜 열로 저장한 것(columnar)이 행으로 저장한 것(relational)보다 조회할 때 빠른가?
- 행으로 저장한다는 건 각각의 행 데이터를 여러 블록에 걸쳐서 디스크 or 메모리에 저장하는 것을 의미한다. 이것은 특정 행이나 컬럼 정보가 필요할 때 모든 블록을 뒤져야 한다는 것이다.
- 열로 저장한다는 건 각각의 열 데이터를 여러 블록에 걸쳐서 다른 디스크 or 메모리에 저장하는 것을 의미한다. 이것은 그 열만 읽으면 되기 때문에 빠르다.
- 또한 compression 알고리즘을 사용하기 때문에 columnar database는 조회에 더 최적화되어 있다.

### why Redis (key-value) fast
- 인메모리라서. (디스크io가 없음. locking 없음)
- 제한된 단순 키벨류 형태의 데이터세트를 지원. 즉 데이터모델이 간단함.

### why MongoDB (Document) fast
- 도큐먼트 기반 데이터모델. 하나의 작업에서 조회/수정 등이 가능
- 다양한 타입의 인덱싱
- 여러 서버에 걸쳐 샤딩 기능 제공
- 데이터 모델링에서 유연성(flexibility)이나 확장성(scalability) 등이 요구되는 상황이면 관계형db보다 좋은 선택이 될 수 있음
- 근데 많은 조인이나 트랜잭션 처리가 요구되면 관계형db가 더 유리함.