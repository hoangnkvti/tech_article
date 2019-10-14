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

# RAM:
  - Sharing VPC resource between account, orgranization
  - Create resource share -> can share subnet -> account được share toàn bộ các vpc resource cùng với subnet: vpc, NACL, route table,.. Tuy nhiên k thể edit những resource này (có thể refer)
  - Phân biệt AZ và AZ ID
  - K support 1 vài service: Glue, Aurora serverless, EMR, cloud HSM, ELB
  - Có thể unshared subnet dù participant đang dùng, nhưng k thể delete 
  -> resource đang dùng subnet đó của participant vẫn dùng subnet đó bthg, nhưng k thể tạo thêm resource mới liên quan đến subnet đó

# VPC Endpoint:
  - Use case: Connect to aws public zone và k connect ra internet
  - Có 2 loại:
    - Gateway Endpoint: 
     - Ảo gắn vào vpc, có thể ở nhiều AZ, cho phép access các service public zone (s3, dynamodb). Dùng policy để giới hạn permission
     - High avalability trong 1 region, k thuộc 1 AZ cố định
     - Có thể tạo private S3, chỉ dùng riêng cho VPC
     - K thể extend outside vpc
    - Interface endpoint: 
     - Physical (network level device), gắn vào subnet, mỗi AZ có 1 cái, dùng SG để giới hạn permission
     - Mỗi AZ sẽ có 1 unique DNS name riêng để connect đến service public zone (SQS) bằng private IP, tuy nhiên có thể setting để connect đến SQS public DNS cũng bằng private IP
     - Có thể extend outside vpc: VPN, Direct connect, VPC peering
     - For each interface endpoint, you can choose only one subnet per Availability Zone.
      
# VPC Peering:
  - Connect với VPC khác (region khác, account khác)
  - 2 vpc peering thì k đc overlap CIDR
  - Khi connect -> gửi request -> bên kia sẽ accept request (cùng account cũng sẽ có bước này)
  - Sau khi peering: khi setting rule của SG VPC này thì có thể refer SG của VPC kia (trong cùng region)
  - K có tính chất bắc cầu: VPC A<->B, B<->C thì A không thể access trực tiếp đến C
  - Có thể resolve public ip của resource -> private IP nếu dùng resolver
  - K thể extend với Direct connect, VPN: nếu connect với on-premise thì cũng k có tính chất bắc cầu

# Site-to-side VPN
  - VPN: connect đến internet across public internet
  - Customer Gateway: is the anchor on your side of that connection. It can be a physical or software appliance
  - Vitual private gateway: the anchor on the AWS side of the VPN connection
  - If your VPN device supports Border Gateway Protocol (BGP), specify dynamic routing when you configure your Site-to-Site VPN connection. If your device does not support BGP, specify static routing
  - Recommend: vpc có dùng BGP, vì sẽ support detection failover
  - Full HA VPN: dùng >=2 tunel, mỗi tunel ở 1 AZ + dynamic route. Ví dụ: 2 customer 
  - 
  - 
