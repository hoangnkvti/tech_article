## Some use case need to remember for exam:

### Central Logging in Multi-Account Environments
 - Refer: https://aws.amazon.com/blogs/architecture/central-logging-in-multi-account-environments/
 ![image](https://d2908q01vomqb2.cloudfront.net/fc074d501302eb2b93e2554793fcaf50b3bf7291/2018/02/13/log-destination-1-1024x629.png)
 - Khi cần centralize logging từ nhiều account: send log từ cloudwatch log -> kinesis stream -> dùng kinesis firehorse save log lại vào S3 của account logging


### AWS PrivateLink to secure and scale web filtering using explicit proxy
 - Refer: https://aws.amazon.com/blogs/networking-and-content-delivery/how-to-use-aws-privatelink-to-secure-and-scale-web-filtering-using-explicit-proxy/
 ![image](https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2018/07/26/rsz_privatelink-blog-diagram.jpg)
 - Dùng AWS Privatelink + fleet proxy instance làm trung gian để access internet
