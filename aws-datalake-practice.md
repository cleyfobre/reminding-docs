## Data Lake Practice

> ref. https://catalog.us-east-1.prod.workshops.aws/workshops/19d47d52-4de6-4b4c-be00-8d99c102f74c/ko-KR/150-accesslog

### Setting

1. Add S3 Bucket
  - name: serial-tracking-datalake

2. Add Glue Database
  - name: data-tracking-database

3. Configure Athena
  - move to
    - Amazon Athena -> Query editor -> Manage settings
  - fill {your bucket name} out on 'Location of query result'
    - s3://serial-tracking-datalake 

4. Add Kinesis Firehose 
  - create delivery stream
    - source: 'Direct PUT'
	- destination: 'Amazon S3'
	- name: serial-tracking-deliverystream
	- s3 destination: s3://serial-tracking-datalake
	- bucket prefix: krmalog/
	- bucket error output prifix: krmaerror/
	- buffer size: 1(mib)
	- buffer interval: 60(s)

### install kinesis agent in ec2 instance

1. Make sure for ec2 instance to have permissions below
  - AmazonKinesisFirehoseFullAccess
  - CloudWatchFullAccess

2. Install an agent
  > sudo yum -y install aws-kinesis-agent
  - IF THERE ISN'T YUM, HERE IT IS
    - https://github.com/awslabs/amazon-kinesis-agent.git
    - Instead of installing yum, git clone this.

3. Configure /etc/aws-kinesis/agent.json
```
	{
	    "cloudwatch.emitMetrics": true,
	    "firehose.endpoint": "firehose.ap-northeast-2.amazonaws.com",
	    "flows": [
	            {
	                "filePattern": "/var/log/ecommerce/access.log",
	                "deliveryStream": "serial-tracking-deliverystream",
	                "initialPosition": "START_OF_FILE"
	            }
	        ]
	}
```
  - filePattern: the log file that you'd like to use
  - deliveryStream: name of firehose delivery stream that you made

4. Restart an agent
  > sudo service aws-kinesis-agent restart

5. Check if it works well
  > tail -f /var/log/aws-kinesis-agent/aws-kinesis-agent.log

### Check S3 bucket

1. folder path would be like 'yyyy/mm/dd/hh'