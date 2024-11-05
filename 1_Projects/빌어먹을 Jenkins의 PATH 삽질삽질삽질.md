#path #jenkins #ssh 

2023년에도 겪었던 일. 직접 세션 접속해서는 잘 되는데 Jenkins 통해서 할 때는 안됨.
임의의 sh파일이 있음. 예를 들어, 안에 `pm2 list`를 한다고 가정함.

근데 PATH값이 직접 세션 접속할 때와 Jenkins로 접속할 때가 달라서 sh 실행이 실패함.

1. 직접 세션 접속할 때의 PATH

```
echo $PATH

/home/youngmin/bin:/home/youngmin/.nvm/versions/node/v16.14.0/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

2. Jenkins 통해 접속할 때의 PATH

```
echo $PATH

/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

1과 2의 차이는 아래 두 경로임.

```
/home/youngmin/bin
/home/youngmin/.nvm/versions/node/v16.14.0/bin
```

Jenkins에서 접속할 땐 저 두 경로가 없어서 `pm2 list`가 실행이 안된 것임.

> [!danger] 아 물론 당연히 같은 user명(youngmin)으로 접속함.

그래서 sh을 실행할 때 초반에 아래와 같이 PATH를 수정하는 코드를 넣어줌.

```
export PATH=
/home/youngmin/bin:/home/youngmin/.nvm/versions/node/v16.14.0/bin:$PATH
```

강제로 작동하는 PATH로 만들어 준것임. 근데 이해가 안됨. 
세션에서도 youngmin으로 접속했고, Jenkins에서도 youngmin으로 접속했음.
근데 PATH값이 다름. 왜?

---

#### 왜 Jenkins는 같은 유저로 접속했는데 다른 환경변수를 가져올까?
### 결론: Jenkins는 원래 이렇다.

챗느님이 아래와 같이 말했다.

```
The issue you're encountering is due to how the environment variables, including the `PATH`, are set up when Jenkins runs your shell script. Jenkins typically runs in a more restricted environment, which may not load your user-specific configurations like `.bashrc` or `.bash_profile`, where your NVM path is defined.
```

즉, 원래 Jenkins는 제한된 환경에서 실행한다는 것이다. .bashrc나 .bash_profile 같은 걸 load하지 않는 게 원래가 그렇다. 그래서 위에서 해결한 것 같이 sh 파일에서 직접 PATH를 추가하는 것이 틀린 방법은 아니라는 것.

혹은 sh 마다 이 짓을 하고 싶지 않으면 

1. 'Jenkins 관리'에서 
2. '시스템 설정' 메뉴 들어가면 
3. 'Global properties' 라는 란이 나오는데 
4. 거기서 'Environment variables' 체크 해주고 
5. PATH 키에 원하는 경로를 Value로 넣어주면 된다고 한다.

다만 이것은 모든 인스턴스에 적용이 될텐데, 맞는 방법인진 모르겠다. 

그리고 젠킨스 아이템을 파이프라인 프로젝트로 만들 때 아래와 같이 하면 된다.

```
pipeline { 
	agent any 
	environment { 
		NVM_DIR = "/home/youngmin/.nvm"
		PATH = "${NVM_DIR}/versions/node/v16.14.0/bin:${env.PATH}"
	}
	...
}
```

#### 정리하자면

1. 위와 같이 sh 파일 안에 PATH 값을 `export PATH = ???` 로 재정의 해준다.
2. Jenkins 관리에서 글로벌로 PATH 값을 넣어준다.
3. 파이프라인 아이템이면 PATH 값을 상단에 재정의 해준다.

로 정리할 수 있겠다.