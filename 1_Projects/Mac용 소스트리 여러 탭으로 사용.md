#mac #sourcetree #맥북 #소스트리

Mac에서 소스트리 사용하다보면 리포지토리마다 여러개의 윈도우가 열려서 굉장히 불편했다. 소스트리 대체제를 찾아볼 정도로 말이다. 그런데 터미널에서 아래 명령어를 치면 탭으로 사용이 가능하다.

```sh
defaults write -app SourceTree AppleWindowTabbingMode -string "always"
```

결과 화면은 이렇다. 개꿀이다.

![[Pasted image 20240201112123.png]]
