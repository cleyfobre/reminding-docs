## mysql thread

### why have to see thread
- webapp에서 mysql로의 transaction에 대해서 알아보다가 mysql에서 transaction 처리를 어떻게 하는지 궁금해짐. 
- 그래서 찾아보니, "show processlist"를 통해서 현재 일하고 있는 thread를 확인할 수 있었고, thread에 대해 더 궁금해짐.


### thread_cache_size
- 근데 mysql에서 thread의 수가 무한정으로 가능하진 않을 터. 
- 그래서 찾아보니, "show variables"에서 "thread_cache_size" 값을 보면 mysql이 cache에 thread를 몇개까지 운영할 지에 대해서 알 수 있음.
	- 즉 "thread_cache_size" 변수 값에 해당되는 수 만큼 thread를 운영할 수 있다는 의미임.
- 근데 "show processlist"를 보면 rows가 100개가 넘음.
	- 그래서 찾아보니, mysql은 thread pool에 idle thread(놀고 있는 thread)를 유지하고 있다가 사용자 connection이 생기면 thread를 assign 해놓음. 즉, 실제 assign되어 일하는 thread의 수가 "thread_cache_size" 변수 값임.
- 그래서 "show processlist"의 대부분 thread는 sleep 상태임.

### 컴퓨터 리소스에 맞게 thread_cache_size 정하기
- ChatGpt에 물어보니 의문이 가는 답변을 받았음
	```
	thread_cache_size = X

	Replace "X" with the desired number of threads to cache. The default value is 0, which means that thread caching is disabled.

	You should start with a relatively small value, such as 8 or 16, and monitor the performance of your server. If you notice that the server is having to create new threads for each new connection, you may want to increase the thread cache size. Conversely, if you notice that the thread cache is not being used much, you may want to decrease the size to free up memory.
	```

##### thread_cache_size의 default 값은 0이다.
	캐시를 안쓴다는 말이고, connection이 있으면 그때그때 thread를 만들고 지우는 행위를 한다. 이건 불필요한 메모리를 안쓰기는 하지만 보통 thread pool도 유지를 하고, 캐시 사이즈도 정해놓음으로써 thread의 재사용을 가능케 하고 성능도 높이는 방법을 쓴다.

_이 내용은 뭔가 확신이 들지 않는다. 그럼 pool에 있던 thread가 사용중이면 cache의 값이 0이던 아니던 상관이 없어지는지..? 헷갈린다. 하지만, 이 이상 찾아보는 건 너무 deep한 것 같고, (dba라면 알아야겠지만..) 무튼 성능에 연관된 두가지 요소, '캐시' 및 '풀' 등을 조절할 줄 알게 되었다는 것에 만족하려고 한다._

##### 일단 작게 설정해놓고 모니터링 하면서 값을 조절하라는데, 어떻게 모니터함?
	mysql 모니터링 툴로서 Nagios, Zabbix 등이 있다고 함. 
	최대 동시 커넥션 수를 도달했는지 체크가 가능하고,
	쿼리 처리량(query throughput)이 얼마나 되는지(부하가 얼마나 되는지),
	활성화된 스레드가 얼마나 되는지.. 그에 비해 캐시는 너무 적게 세팅되어있는건 아닌지.. 체크해볼 수 있다고 함. AWS에서 대체될만한 게 있는지와 다른 유용한 툴 있는지 찾아보는 게 필요할 듯


### mysql 서버 성능을 모니터링하기 위해 필요한 지식들
- SHOW VARIABLES LIKE 'max_connections'
	- 클라이언트로부터 '최대 동시 연결 수'를 의미한다.
- webapp에서 hikariCP 등을 이용해 'Connection Pool'을 조절할 수 있음. 사실 이건 정확하게는 mysql 서버 성능을 위한 거라기 보다는 webapp에서 효율적으로 mysql을 사용하는 테크닉이라 할 수 있음. 허나 커넥션 자체를 효율적으로 맺는다는 것은 곧 mysql에게도 성능 향상을 불러일으킬 수 있음.
	- `max-lifetime`: the maximum lifetime of a connection in the pool before it is closed and removed
	- `connection-timeout`: how long to wait for a connection to be established before timing out
	- `idle-timeout`: how long a connection can be idle (not used) before being closed and removed from the pool
	- `wait-timeout`: how long to wait for a connection to become available when the pool is at its maximum capacity
	- `maximum-pool-size`: the maximum number of connections to create in the pool.