## apigateway-lambda

- write a sample code in lambda
```
import json

def lambda_handler(event, context):
    
    transactionId = event["queryStringParameters"]["transactionId"]
    transactionType = event["queryStringParameters"]["type"]
    transactionAmount = event["queryStringParameters"]["amount"]
    
    print("transactionId: ", transactionId)
    print("transactionType: ", transactionType)
    print("transactionAmount: ", transactionAmount)
    
    transactionResponse = {}
    transactionResponse["transactionId"] = transactionId
    transactionResponse["transactionType"] = transactionType
    transactionResponse["transactionAmount"] = transactionAmount
    transactionResponse["message"] = "Hello Lambda World!"
    
    responseObject = {}
    responseObject["statusCode"] = 200
    responseObject["headers"] {}
    responseObject["headers"]["Content-Type"] = "application/json"
    responseObject["body"] = json.dumps(transactionResponse)
    
    return responseObject
```
- in api gateway
  - 작업 
  - 리소스 생성 (/transactions)
  - 메소드 생성 (GET)
  	- 결과: arn:aws:execute-api:ap-northeast-2:787107338149:w4w00u5z88/*/GET/transactions
  - API 배포 (스테이지 생성)
- do test (포스트맨)
- cloudwatch 로그스트림 확인

### ref
- https://www.youtube.com/watch?v=uFsaiEhr1zs
