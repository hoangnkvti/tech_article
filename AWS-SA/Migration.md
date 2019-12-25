# Migration

## The 6 R’s:
 - Rehosting: lift-and-shift. Move server --> host server in aws
 - Replatform: lift-tinker-and-shift. Optimize bằng cách chuyển đổi platform, nhưng vẫn giữ architect. VD: sql move lên rds
 - Repurchase: Moving to a different product --> dùng các dịch vụ trả phí thay thế
 - Refactoring / Re-architecting: đổi lại architect của hệ thống hướng tới cloud -> most expensive but most benificial
 - Retire: bỏ đi các phần hệ thống k còn dùng
 - Retain: Giữ nguyên đó và hiện tại k làm gì cả

## Data Pipeline:
 - Serverless product, create workflow-style data migration and data transformation pipeline
 - Có thể kết hợp với EMR -> xử lý song song nhiều luồng (parallel compute)
 - Phù hợp với large scale data migration, transformation
 
## Migration:
  - Snowball: 
    - Storage appliances provided by aws can store 50Tb or 80Tb data: dụng cụ có thể chứa 50, 80Tb data.
    - Dùng để import data to S3 hoặc export from S3 hoặc local storage and compute
    - Phải order thông qua aws console -> ship đến thông qua local area service -> cài Snowball agent transfer data -> ship 
  back to aws
    - -> Khi transfer >10Tb thì nghĩ đến snowball. Nếu <10Tb thì có thể dùng StorageGateway
    - Data đc encrypt at rest and transit
  - Snowball Edge:
    - Add compute power than snowball
    - Use to run local compute function, run lambda function
    - 3 version: Storage optimized, compute optimized, Compute optimized with GPU
    - Khi migrate DB Large Data Stores -> dùng kết hợp DMS + Snowball Edge ( vì phải dùng AWS Schema Conversion Tool (AWS SCT) extract local data) + S3 là nhanh nhất
  - Snowmobile:
    - Là mobile datacenter. 
    - Use for data center migration or >10Pb
    - Sau khi request -> drive đến location -> transfer data -> drive back to aws

## Applicaiton Discovery Service:
  - Gathering information about their on-premises data centers.
  - Collects and presents server specification information, performance data, and details of running processes and network connections
  - Provides protection for the collected data by encrypting it both in transit to AWS and at rest within the Application Discovery Service data store.
  - Integrated with AWS Migration Hub (which simplifies your migration tracking as it aggregates your migration status information into a single console) -> All discovered data is stored in your AWS Migration Hub home region
  - Có 2 cách để collect data:
    - Agentless discovery: deploying the AWS Agentless Discovery Connector (OVA file) through your VMware vCenter.
    - Agent-based discovery: deploying the AWS Application Discovery Agent on each of your VMs and physical servers
  ![image](https://user-images.githubusercontent.com/40649408/70844237-98b78580-1e81-11ea-8377-65e062587236.png)
 - Nếu k dùng đc 2 loại agent -> upload trực tiếp data của server lên Migration Hub

## Server Migration Service
  - Is an agentless service which makes it easier and faster to migrate thousands of on-premises workloads to AWS.
  - 4 stages: schedule -> uploading (up VMDK to s3) -> converting (convert VMDK to EBS snapshot) -> create AMI
  - Requires a connector that orchestrates the workflow of the migration process. This connector is deployed in the vCenter.
  - Chia làm nhiều group trong application khi migrate application    
  ![image](https://user-images.githubusercontent.com/40649408/70844423-357b2280-1e84-11ea-8c0a-37553bb7677b.png)


## Notes:
  - Migrate VPC using IPv4 to IPv6:
    - Step 1: Associate an IPv6 CIDR Block with Your VPC and Subnets
    - Step 2: Update Your Route Tables
    - Step 3: Update Your Security Group Rules
    - Step 4: Change Your Instance Type
    - Step 5: Assign IPv6 Addresses to Your Instances
    - Lưu ý: NAT gateway k support IPv6 -> dùng Egress-only internet gateway
