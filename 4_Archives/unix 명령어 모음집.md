#유닉스 #리눅스 #우분투 #unix #linux #ubuntu #cli #command

#### 서버 상태 알기

- ps -a
- ps aux
	- 현재 돌고있는 프로세스들의 cpu, memory 사용량 등을 표시해줌
- df -h
	- 디스크 폴더별 사용량을 보여줌

#### 디스크 용량 점검하기

- root 기준으로해서 제일 많이 디스크 잡아먹는 폴더 파악하기
	- sudo du -h --max-depth=1 .
- 어떤 디렉토리가 용량 많이 차지하는지 파악
	- sudo du -hsx /* | sort -rh | head -n 40

#### 로그 분석하기

아래와 같이 한줄 한줄 일정 포맷에 맞게 로그를 쌓았다.

```json
{'localtime': '11:04:42', 'name': '삼성전자', 'chetime': 110441, ...
{'localtime': '11:04:42', 'name': '삼성전자', 'chetime': 110441, ...
```

- 특정 컬럼으로 group by 하여 count하고자할 때 아래와 같이 사용했다.

> [!tip] sort 하고 uniq를 해야 완벽하게 group by 된다. 

```sh
cat log.txt | grep 'chetime' | cut -d':' -f6 | cut -d',' -f1 | sort | uniq -c | sort -n
```

- 특정 문자열이 포함된 열을 카운드할 때 아래와 같이 했다.

```sh
grep "'chetime': 11" log3.txt | grep "삼성전자" | wc -l
```

- 쌓이고 있는 로그를 분석하고자 할 때, 아래와 같이 본 떠서 작업한다.

```sh
# unix
cat hello.txt > world.txt

#windows 
type hello.txt > world.txt
```

- jq로 json 형식 데이터면 키를 abc 순으로 정렬하여 보기. 갸꿀팁 
- 
```sh
% redis-cli -h hello.com get myKey | jq 'to_entries | sort_by(.key) | from_entries'
{
	"a": "first",
	"b": "second"
}
```