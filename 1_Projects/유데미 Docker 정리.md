#docker #container #image #udemy #유데미 

### 먼저 Dockerfile을 만들어야지
아래 간단한 도커파일이 있다. 그동안 복붙만 해와서 제대로 인지하지 못하고 사용했던 부분을 정리하려 한다.

```Dockerfile
FROM node
WORKDIR /app
COPY . /app
RUN npm install
EXPOSE 80
CMD ["node", "server.js]
```

- `FROM node`
	- 우리는 node 이미지에서 나만의 이미지를 만들 것이다.
- `WORKDIR`
	- 앞으로 우리가 사용할 명령어들의 기본 path를 명시한다. 예제에서는 /app에서 모든 명령어가 실행됨을 의미한다. /app이 없으면 알아서 만든다.
- `COPY . /app`
	- 모든 파일들을 첫번째 path에서 두번째 path로 복사하겠다는 것이다. Dockerfile의 path가 첫번째 인자(.)다. 그리고 두번째 인자(/app)는 이미지 안에 있는 path다.
	- 여기서는 `COPY . .`와 같이 써도 무방하다. 왜냐면 위에 WORKDIR를 /app으로 설정해놨기 때문이다. 하지만 /app라고 굳이 명시하면 오히려 가독성이 좋기 때문에 이렇게 쓰는 것이 좋다.
- `RUN npm install`
	- 이미지를 생성할 때 npm install을 한다.
- `EXPOSE 80`
	- 80포트로 노출시킬 것이다.
- `CMD \["node", "server.js"]`
	- RUN과 같은 명령어이나, 이 명령어는 이미지를 생성할 때 사용되는 명령어가 아니라 생성된 이미지가 나중에 컨테이너를 실행할 때 사용하는 명령어다. 그래서 문법도 조금 다른가보다. 마치 배열처럼 들어가 있다.

### 만든 Dockerfile을 사용해야지
위에서 만든 Dockerfile을 이미지로 만들기 위해 아래와 같이 한다.

```
docker build .
```

`.`의 의미는 현재 위치에 Dockerfile이 존재함을 의미한다. 근데 만약에 html 파일 하나가 변경되었다면? build를 다시 해야 한다. 근데 보통 Dockerfile 명령을 할 때 변경사항이 없으면 캐시를 이용하기 때문에 build 시간이 짧아진다. 하지만 html 파일이 변경되었기 때문에 해당 파일과 관련된 명령어 이후 부터는 캐시를 사용하지 않는다. __도커에서 이 캐시를 레이어라고 한다.__

즉 `COPY . /app` 이 부분을 실행할 때 변경사항이 발생하므로 그 아래에 있는 `npm install`을 다시 실행해야 한다. 이 명령어는 굉장히 오래 걸린다는 것을 누구나 안다. 그러므로 아래와 같이 변경하자.

```Dockerfile
FROM node
WORKDIR /app

COPY package.json /app
RUN npm install

COPY . /app

EXPOSE 80
CMD ["node", "server.js]
```

위와 같이 하면 `npm install`이 먼저 실행이 되고 그 이후에 `COPY . /app`이 실행되기 때문에 html 파일 하나가 변경되더라도 다시 build 했을 때 `npm install` 명령어는 기존 캐시를 사용할 것이다. 그러므로 이미지 빌드가 빨리 될 것이다.

### Attached & Detached
`docker run` is set 'attached' mode as a default but `docker start` is set 'detached' mode as a default.
If you use `docker run` with detached mode, you can do right here

```
docker run -p 80:80 abc -d
```

### 이미지에 Argument 적용
아래와 같은 파이썬 프로그램을 보자. 유저로부터 파라미터를 받아야 하는 프로그램이다. 

```python
from random import randint

min_num = int(input("작은 숫자를 입력하세요.: "))
max_num = int(input("큰 숫자를 입력하세요.: "))

if (max_num < min_num):
	print("잘못된 인자값입니다.")
else:
	rnd_num = randint(min_num, max_num)
	print(rnd_num)
```

해당 프로그램은 유저와 상호작용해야 하기 때문에 이미지를 실행할 때 `-i -t`를 넣어주어야 한다. 이것은 터미널로 상호작용할 수 있다는 것을 의미한다. `-i`는 interactive(상호작용)를, `-t`는 tty(터미널 입력)를 의미한다.