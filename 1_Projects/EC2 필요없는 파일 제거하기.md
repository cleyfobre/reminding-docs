#### 문제
- 젠킨스 서버 disk usage가 95%인 상황

#### 해결 과정
- /var/log/journal 밑에 로그들  
- docker system prune 실행
	- 빌드할 때마다 /var/lib/docker/overlay2 에 파일들이 쌓임 
	-  삭제되는 항목  
		- all stopped containers
		- all networks not used by at least one container
		- all dangling images
		- all dangling build cache
#### 결과
-  disk usage 95% -> 63%

