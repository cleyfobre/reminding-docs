## tutorial for sns, sqs and lambda

- create lambda function
- use existing role or make new role with a template related to sns, sqs and cloudwatch
- create sqs queue
- for now, disable encryption (암호화 비활성화)
- in queue detail page, click a 'lambda trigger'
- in lambda funtion detail page, you can see sqs trigger connected
- create sns topic
- in topic page, click subscription tab(구독) and create subscripion 
- use sqs queue endpoint and select queue you made before
- send a message from sns topic and check log stream from lambda function