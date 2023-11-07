## s3 to sns connection

- set sns topic access policy like below
```
{
  "Version": "2008-10-17",
  "Id": "example-ID",
  "Statement": [
    {
      "Sid": "example-statement-ID",
      "Effect": "Allow",
      "Principal": {
        "AWS": "*"
      },
      "Action": "SNS:Publish",
      "Resource": "<SNS ARN>",
      "Condition": {
        "ArnLike": {
          "aws:SourceArn": [
            "<S3 버킷 ARN>"
          ]
        }
      }
    }
  ]
}
``` 
- the key point is that you should set 'Condition' like that.
- in s3 bucket, 
  - 속성 탭 클릭
  - add new event notification(이벤트 알림 탭)
- select your sns topic and complete
- do test