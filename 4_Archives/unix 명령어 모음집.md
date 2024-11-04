#유닉스 #리눅스 #우분투 #unix #linux #ubuntu #cli #command #ssh

### sudo su -
현재 접속된 user가 super user로 등록되어 있을 시, 현재 user 접속 정보로 root 권한을 얻는다.
- 예제
	- 현재 유저는 dev1인 상황
	- root의 권한을 얻기 위해 sudo su - 입력
	- dev1의 패스워드를 입력
	- 성공!

```sh
dev1@gazua-1**:**~**$ sudo su -
[sudo] dev1 암호: 
root@gazua-1:~#
```

#### 서버 상태 알기

- ps -ef | grep "java"
	- java 실행 중인지 확인
- ps aux
	- 현재 돌고있는 프로세스들의 cpu, memory 사용량 등을 표시해줌
- df -h
	- 디스크 폴더별 사용량을 보여줌
- ps aux --sort=-%mem | head
	- 메모리 순으로 많이 사용 중인 프로세스

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

```sh
% redis-cli -h hello.com get myKey | jq 'to_entries | sort_by(.key) | from_entries'
{
	"a": "first",
	"b": "second"
}
```

- 특정 단어가 몇번째 줄에 있는지 보기

```sh
youngmin@gazua % cat history.txt | grep -n "120059"
50025:120059,76400,76800,76000,76100,0,0.00,+5
50026:120059,76400,76800,76000,76000,-100,-0.13,-46
50027:120059,76400,76800,76000,76100,0,0.00,+10
50028:120059,76400,76800,76000,76100,0,0.00,+1
50029:120059,76400,76800,76000,76100,0,0.00,+5
50030:120059,76400,76800,76000,76000,-100,-0.13,-284
```

- 실시간 명령어 0.1초 주기로 실행해서 보기

```
watch -d -n 0.1 ‘redis-cli --user system_api GET e005930 | jq .price,.mdvolume,.redisDate’
```

# SSH 치트키

#### 일일이 치지말고 미리 세팅해놓자.

1. `.ssh` 폴더에 `config` 파일을 만들자.

```sh
.ssh % sudo vi config
```

2. 대충 아래처럼 작성해놓자. `pem` 파일이 있는 경우와 그냥 패스워드로 들어가는 경우다.

```sh
Host stage
    HostName stage.hello.co.kr
    User ubuntu
    IdentityFile ~/.ssh/hello.pem

Host dev
    HostName dev.hello.co.kr
    User ubuntu
```

