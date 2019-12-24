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
