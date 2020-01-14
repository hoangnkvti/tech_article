# Other service

## Glue:
 - Fully managed extract, transform, and load (ETL) service that makes it easy for customers to prepare and load their data for analytics -> discover data, transform it, and make it available for search and querying.
 - Crue crawler: scan data sét and populate yo Glue Data Catalog
 - Glue Data Catalog: serve as a central metadata repository, chứa những metadata table -> immediately available for analysis
 - Glue ETL: schedule jobs, execute script (extract, transform,...)
 - Có thể dùng trong các use case như: query S3 data lake, analyze log data in data warehouse, ...
 
## Rekognition
 - Deep Learning-based image and video analysis
 - Có thể làm đc nhiều chức năng: Face-based user verification, Facial recognition, Text detection,....
 - Provide CreateStreamProcessor có thể detect và recognize faces in a streaming video -> có thể input Kinesis stream and output ra Kinesis stream

## Amazon WorkDocs
  - Amazon WorkDocs is a fully managed, secure content creation, storage, and collaboration service
  - Support share restrict, time-based file sharing

## Amazon Cloudsearch
  - Amazon CloudSearch is a managed service in the AWS Cloud that makes it simple and cost-effective to set up, manage, and scale a search solution for your website or application.
  - Supports 34 languages and popular search features such as highlighting, autocomplete, and geospatial search
## Cognito:
  - Amazon Cognito lets you add user sign-up, sign-in, and access control to your web and mobile apps quickly and easily
  - User pool: User directory, add sign-in, sign-up function
  - Identity pool: is a way to authorize your users to use the various AWS services
  ![image](https://user-images.githubusercontent.com/40649408/71245401-f05d6180-2357-11ea-95b3-73422a99e703.png)

## Amazon Data Lifecycle Manager:
  - Support EBS
  - Dùng để automate snapshot EBS (có thể by tag)

## AWS Schema Conversion Tool
  - The AWS Schema Conversion Tool makes heterogeneous database migrations predictable by automatically converting the source database schema and a majority of the database code objects, including views, stored procedures, and functions, to a format compatible with the target database
  
## Amazon Managed Blockchain
  - Is a fully managed service that makes it easy to create and manage scalable blockchain networks using the popular open source frameworks Hyperledger Fabric and Ethereum
  - Send invitation to add member (same of different aws account)
  - AWS Blockchain Templates provides a fast and easy way to create and deploy secure blockchain networks using popular open source frameworks. Use ECS ở EC2 run docker

## Amazon Mechanical Turk:
  - Crowdsourcing marketplace that makes it easier for individuals and businesses to outsource their processes and jobs to a distributed workforce who can perform these tasks virtually
  - Là nơi outsource các yêu cầu --> send request lên Turk, sau đó có ng sẽ nhận làm request đó
  
## Amazon Appstream 2.0
  - Fully managed application streaming service
  - Một cách truyền phát các ứng dụng máy tính để bàn tới người dùng của bạn trong trình duyệt web. Ví dụ: sử dụng MS Word trong Google Chrome.
## XRays:
  - AWS X-Ray is a service that collects data about requests that your application serves, and provides tools you can use to view, filter, and gain insights into that data to identify issues and opportunities for optimization
  - Dùng để trace inbound và output request (cài agent ở instance)
