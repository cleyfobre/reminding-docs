## iam-role

### example: api gateway에 cloudwatch 역할(iam-role) 달기
- iam role (역할) 생성
- 이름 정하기 (ex. apigateway_to_cloudwatch)
- '사용 사례'에서 'API Gateway' 선택하고 생성하면 다음 내용의 역할이 만들어진다
  - "Allows API Gateway to push logs to CloudWatch Logs."
- 생성한 역할 클릭하면 다음 사항을 확인할 수 있다.
  - ARN: arn:aws:iam::787107338149:role/apigateway_to_cloudwatch
  - 정책: AmazonAPIGatewayPushToCloudWatchLogs
    - API Gateway를 사용 사례로 선택하면, Default로 정해진 정책이 자동으로 만들어진다.
    - API Gateway에서 Cloudwatch에 대해 할 수 있는 행위(Action)가 정의되어 있다.
- API Gateway 콘솔에서 이미 만든 API를 선택
- '설정'에 들어가서 방금 만든 'CloudWatch 로그 역할 ARN'을 입력
  - 좀 있으면 cloudwatch의 로그그룹에 방금 API Gateway에 연결된 로그그룹이 하나 생김
  - 해당 로그그룹의 ARN을 복사
- API의 스테이지로 가서 '로그/추적'을 클릭하여 '엑세스 로그 활성화' 체크
- '엑세스 로그 대상 ARN'에 방금 복사한 로그그룹 ARN을 붙여 넣기
- 밑에 '로그 형식'은 맘대로. JSON 예제를 선택함.

### ref
 - https://aws.amazon.com/ko/premiumsupport/knowledge-center/api-gateway-rest-api-lambda-integrations/