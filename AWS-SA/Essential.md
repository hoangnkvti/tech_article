 ## AWS Account:
 
  - 3 domain riêng biệt: Billing, Authorize và Authenticate
  - A blast radius is the distance from the source that will be affected when an explosion occurs
  - High Availability: có phương án recovery nhanh chóng
  - Fault Tolerance: dự phòng thêm phướng án
  - Disater recovery
    - RPO: Là thời gian recovery từ backup gần nhất. Data giữa thời gian hiện tại và thời gian backup sẽ bị mất
    - RTO: Là thời gian recovery từ lúc xảy ra disater -> lúc hệ thống hồi phục
  - Data persistence:
    - Ephemeral: là data localy với resource, và bị mất khi resource bị tắt: ElasticCache, 
    - Transient: SQS
    - Persistence: EFS, EBS
    
    
## AWS RAM:
 - AWS RAM: có thể share resource đến các account khác hoặc trong organization
 - Thực ra AZ name ở các account có thể khác nhau, refer đến AZ ID không giống nhau
 - K thể thay đổi những config, element ở VPC level (route table, ...) nhưng có thể refer đến shared VPC (tạo instance,..)
 - K thể share subnet ở default VPC, chỉ có thể share đc ở custom VPC
 - Nếu subnet đang có resource ở 1 paticipant nào khác -> k thể delete
