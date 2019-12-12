# Migration

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
  - Snowmobile:
    - Là mobile datacenter. 
    - Use for data center migration or >10Pb
    - Sau khi request -> drive đến location -> transfer data -> drive back to aws
