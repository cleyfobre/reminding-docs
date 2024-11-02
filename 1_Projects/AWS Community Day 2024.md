#aws #awscommunityday2024 #awscommunityday 


#### Platform Engineering
- 시작
	- **플랫폼**을 **제품**처럼 다룬다.
	- 셀프 서비스로 제공한다.
- 사례
	- spotify backstage: 오픈소스 개발자 포탈
	- NASA 데이터 분석 플랫폼

#### CloudFormation & StackSets

1. CloudFormation
	1. AWS가 제공하는 IaC도구. 사용자가 template를 정의해서 CloudFormation에 업로드하면 AWS가 Resource를 생성해준다.
2. StackSets
	1. Resource들의 묶음. Organization 계정에서 동시 여러 계정에 리소스를 배포할 때, StackSets를 사용하면 한번에 배포할 수 있다.
	2. 하지만 동시에 여러 계정에 배포할 일은 현재 없기에 개념만 알고 간다.

> [!note] 세션 요약
> 해당 세션에서는 여러 계정에 StackSets로 묶어 배포하고 나서, 배포가 완룔된 EC2의 IP를 S3 버킷에 넣는 일련의 과정을 소개해주는 내용이었다. 과정 중에 Lambda 함수도 사용되었다.






