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
    
## VPC
 - AWS Public Zone: S3, DynamoDB, CWLogs,...
 - Mỗi subnet thì có 5 reserved IP: .0, .1, .2, .3, .255, và giữa những subnet thì sẽ k có chuyện overlap IP CIDR

# Route table:
 - Mỗi subnet dùng 1 IP cho router (.1)
 - IP nào cụ thể hơn thì sẽ đc ưu tiên (/32 > /16)
 - Có thể tạo custom RT -> attach vào subnet: 1 subnet chỉ đc associate với 1 RT, 1 RT có thể associate với nhiều subnet
 - VPC luôn có 1 default RT
 
 # NACLs:
  - Stateless: config inbound và outboud (send request -> inbound, response -> outbound)
  - Subnet level
  - Thứ tự priority: giá trị # của rule
  - Có thể DENY explicit traffic (SG k làm đc)
  
