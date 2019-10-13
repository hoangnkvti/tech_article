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
  
# Security Group:
  - Stateful, attach to Network interface of resource
  - K có DENY rule có thể deny explicit address
  - Tất cả rule sẽ được apply same time, k có priority giữa các rule
  - Source thì ngoài IP range có thể là aws resource
  - Các resource share 1 SG (cùng nằm trong 1 SG) thì default có thể connect đến nhau, tránh overhead cost
  
# Subnet:
  - Private subnet là những subnet k connect đc tới internet hoặc aws public zone (ngược với public subnet)
  - Bastion host khi dùng ipconfig để check thì k có mention đến public IP mà đc attach -> bastion host internally k có public IP. Khi connect internet thì internet gateway sẽ mapping private IP và public IP

# NAT Gateway:
  - Dùng khi private subnet connect đến Internet nhưng k muốn internet connect vào
  - NAT Gateway translate private IP -> public IP access internet nhưng k có chiều ngược lại
  
# Egress-Only Gateways
  - Attach vào VPC -> giúp resource trong vpc connect to internet bằng IPv6 (k có chiều ngược lại)
  - Dùng khi NAT Gateway k support IPv6
  - Với aws thì mọi ipv6 đều publicly routable

# DNS in VPC:
  - Có thể truy cập = DNS đến các resource trong VPC
  - Khi dùng 1 ec2 connect đến resource trong cùng VPC = public DNS -> IP đc sử dụng sẽ là internal private IP. Có thể dùng Route53 tạo private hosted zone, map DNS với IP address
  - Tuy nhiên, on-premise network sẽ không thể dùng private IP hoặc private dns dù thông qua VPN
  - Vì vậy, Khi muốn access vpc resource = private IP address thì có thể dùng Route 53 VPC resolver:
      - inbound: connect từ on-premise vào vpc
      - outbound (with forward rule) enpoint: connect từ vpc ra on-premise
# VPC flow log:
  - Retrieve traffic `meta-data`: version, account-id, ip,..., NOT for `actual data`
  - Những action k đc log: DHCP, DNS, get meta-data (trong EC2), license activation request
  - 
