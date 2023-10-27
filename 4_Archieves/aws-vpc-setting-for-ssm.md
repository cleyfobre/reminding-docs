## vpc setting for ssm

### 목표
- vpc 내 람다 함수를 만들었다.
- 해당 함수는 rds에서 select, s3에서 delete를 한다.
- 이제 ssm의 parameter store에서 필요한 value들을 가져오려 한다.
- 그런데 계속 접근이 안된다. 타임아웃이 뜬다.

### 해결
1. vpc endpoint 생성
- 앤드포인트 생성하여 vpc 내 리소스들이 ssm에 접근할 수 있도록 한다.
- vpc 내 람다함수는 이 앤드포인트를 통해서 ssm에 접근한다.
- 앤드포인트에서 서브넷 연결할 때, 호출할 람다 함수가 속해있는 서브넷들만 연결한다.
2. security group 생성
- 람다에서 이 vpc endpoint를 호출할테니, 인바운드에 같은 security group을 추가한다. 
- 그럼 해당 그룹의 리소스에서의 요청만 허가된다.
3. 람다함수에 role 수정
- 람다함수의 iam role에는 현재 s3, rds에 대한 policy만 있을 것이다.
- ssm에 접근을 위해 관련된 정책(policy)을 추가해준다. 
- ssm으로 검색하면 몇개 나올 것이다. (이번엔 SSMReadOnly어쩌구를 선택했다)
- 이렇게 하면 ssm 정보를 가져오는 데 문제없을 것이다.
4. rds security group의 inbound 수정
- 혹시라도 rds 쪽에서 접근이 안되면 
- rds security gruop의 inbound에다가
- 이 람다 함수가 가지고 있는 security group을 넣어주면 된다.

### 혹시 안되면
- 이거 다 해도 안되면 내가 뭔가 쓴걸 빠트린 것이다.
- 중요한 건 네트워크 상에서 하나하나 걸리는 요소를 점검하는 것이다.
- iam role, security group, vpc 등등
- 하나 하나 따져보는 게 중요하다.

### ref
- 겁나 찾아 봄