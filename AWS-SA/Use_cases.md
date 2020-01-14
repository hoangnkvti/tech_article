## Some use case need to remember for exam:

### Central Logging in Multi-Account Environments
 - Refer: https://aws.amazon.com/blogs/architecture/central-logging-in-multi-account-environments/
 https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2018/02/13/log-destination-1-1024x629.png
 - Khi cần centralize logging từ nhiều account: send log từ cloudwatch log -> kinesis stream -> dùng kinesis firehorse save log lại vào S3 của account logging
