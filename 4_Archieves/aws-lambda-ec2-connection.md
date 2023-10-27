## lambda-ec2 커넥션

### object
- I want lambda function to connect to mysql (in my ec2 instance)

### What I did for connecting lambda function and ec2 instance

1. make sure that they are in same VPC

2. the security group (ec2) has it on the inbound list to the security group (lambda)

3. the lambda function has full permissions to ec2

### freaking 삽질

1. wrong host
  - I set public host not private host... holly molly
  - I changed it to private host and it can be connected well

2. wrong password
  - After solving 'wrong host', I got this error
  ```Access denied for user 'plkdev'@'ip-172-**-**-125.ap-northeast-2.compute.internal' (using password: YES) ```
  - But user 'plkdev'@'%' is grant all privileges
  - I just input wrong password 