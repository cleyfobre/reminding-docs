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
근데 PATH값이 다름. 왜? 이건 #todo 임....



