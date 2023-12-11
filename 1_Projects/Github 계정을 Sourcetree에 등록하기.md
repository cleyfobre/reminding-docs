#sourcetree #git #github

Github에 로그인하여 오른쪽 상단의 내 계정을 클릭하면 메뉴들이 나온다. 여기서 **Settings** 를 클릭한다.

![[Pasted image 20231211163449.png]]

캡처하기 귀찮으므로 아래와 같이 순서대로 이동한다.

1. 메뉴 맨 아래 **Developer settings** 클릭
2. **Personal access tokens** 클릭
3. 2023년 12월 11일 기준, Beta 버전으로 Fine-grained tokens가 있으나 기존에 사람들이 사용하던 **Tokens (classic)** 을 클릭한다.
4. **Generate new token** 을 통해 토큰을 생성한다. ==생성된 토큰은 한 번만 화면에 보여지므로 반드시 복사하도록 한다!==

이제 Sourcetree로 돌아와서 상단에 설정을 열면 아래와 같이 계정 리스트를 볼 수 있으며, 새로 추가할 수 있는 화면이 나온다. 이 문서를 작성하기 전에 이미 계정을 하나 등록했기 때문에 아래처럼 리스트에 나온다.

![[Pasted image 20231211164313.png]]

추가 버튼을 클릭한 후, 아래 처럼 필요한 부분을 넣는다. 일단 나는 Github의 계정을 호스트로 선택했고, 인증 방식은 Personal Access Token, 사용자 이름은 나의 Github username, 방금 생성한 토큰을 넣고, 마지막 프로토콜에 HTTPS로 선택해주면 끝이다.

![[Pasted image 20231211164429.png]]

그럼 아래처럼 내 계정이 로그인이 되면서 클론을 받을 수 있는 리포지토리 리스트가 뜬다.

![[Pasted image 20231211164647.png]]