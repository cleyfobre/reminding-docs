#git 

### 브랜치 제거하기

```
git push <remote> —delete <branch>
```

### 용량 때문에 Push가 안됨

```
git config --global http.postBuffer 524288000
```

상황에 따라 `--global` 뺄 지 결정하면 된다.