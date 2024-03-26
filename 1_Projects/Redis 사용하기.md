#redis #acl

#### Install and Start

```sh
brew install redis

# start&stop in background
brew services start redis
brew services info redis
brew services stop redis

# run CLI
redis-cli
```

#### Command examples

2. String

a라는 key에 다양한 값을 넣는다. nx(False)와 xx(True)와 같이 파라미터를 추가로 넣을 수도 있다. 코딩할 때 유용할 듯 하다. 특정 조건을 분기처리하는 것이 아니라, 조건의 True/False 값을 넣어 Redis가 필터링 하도록 말이다.

```
set a a
set a b
set a:1 c nx
set a:1 c xx
get a:1
```

`mset` 을 통하여 여러개의 key를 한번에 세팅할 수 있다.

```
mset a:1 a a:2 b a:3 c
```

2. List

bikes 라고 key를 정한다. a와 b를 넣는다. 그리고 pop을 통해 마지막 element를 꺼내어 출력한다. pop을 하면 꺼냄과 동시에 list에서 제거된다. llen을 통해 list의 길이를 확인한다.

```
lpush bikes a
lpush bikes b
lpop bikes
llen bikes
```

3. Sets
4. Hashes
5. Sorted sets
6. Streams
7. Bitmaps

> [!note] Other types
> 데이터 타입은 이 밖에도 Geospatial, Bitfields, Probabailistic and Time series 등이 있다.

#### 자주 사용하는 명령어

- redis 명령어의 결과를 파일로 옮기기 #redis

```sh
echo "keys *" | redis-cli -h hello.co.kr -p 6379 > test.txt
redis-cli -h hello.co.kr -p 6379 keys "k??????:0118" > ss.txt
```

- redis 특정 키 삭제

```sh
redis-cli -h hello.co.kr -p 6379 keys "k??????:0118" | xargs redis-cli -h hello.co.kr -p 6379 del
```

- redis-cli 사용하면서 필터링 적용하기

```sh
redis-cli -h hello.co.kr monitor | grep 192.168.88.37 | cut -d. -f1 | uniq -c
```

- 모니터링 필터링 적용해서 보기

```sh
redis-cli -h hello.co.kr monitor | grep 192.168.88.00 | cut -d. -f1 | uniq -c
```

### redis acl
유저 설정하는 옵션이다. 

1. redis.conf 내에 aclfile /etc/redis/users.acl 추가(주석해제)

```sh
# aclfile /etc/redis/users.acl
# 부분 주석 해제

aclfile /etc/redis/users.acl
```

2. aclfile /etc/redis/users.acl 생성

```sh
# 빈파일을 생성하여 저장할 수 있도록 처리
vi /etc/redis/users.acl
```

3. redis 재시작

```sh
systemctl restart redis-server
```

4. 유저 추가 후 저장

```sh
#redis 서비스에 접속
[root@ip-10-0-0-10 redis]# redis-cli

#기존 환경설정에 저장해둔 초기 유저로 접속(6버전 이상은 초기 사용자가 default)
127.0.0.1:6379> auth default me1234
OK

#유저 추가 
#유저명 : oil, 패스워드: me1234, 접근권한 memanager로 시작하는 모든키에 모든 권한 부여
127.0.0.1:6379> ACL SETUSER oil on >me1234 ~memanager* allcommands
OK

#유저 정보 저장
127.0.0.1:6379> acl save
OK

127.0.0.1:6379> exit
```

위 행위를 한 뒤 미리 생성했던 users.acl 을 열어보면 위에서 생성한 유저가 추가되어 있다.