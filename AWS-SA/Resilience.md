# Resilience

## Service:
 - EC2: take snapshot và save ở S3 -> spread across multiAZs. Có thể copy snapshot sang region khác
 - Stateless application: lưu session ở dynamoDB -> dùng cho ASG và ELB
 - Cách chọn instance:
   - Reserved: know the base load, consitent
   - On-demand: variable load, thời điểm peak thất thường
   - Spot: analytics work, có thể kiểm soát đc lỗi, hoặc khi instance fail
   - Chú ý: On Demand Capacity Reservation, Scheduled Reservation

## ASG:
 - Baking AMI: k dùng cho flexible deployment, dùng khi cần deploy nhanh, ít thời gian
 - Có thể pass bootstrapping config vào user-data -> flexible deployment but lose speed
 - Launch configuration: Immutable, can not edit after created
 - Launch Template: có thể reuse, modularization -> flexible (recommend as best practice)
 - Khi mà 1 instance fail, hoặc unhealth check -> ASG tự động tạo mới instance (= số intansce đc setting ở Desire)
 - Có thể add scale policy: 
   - With step: Take action (add, remove) instances step by step (có thể có nhiều step) khi mà 1 alarm đc trigger
   - Target policy: tăng hay giảm instance dựa vào target value
 - Có thể setting scale by schedule
 - Có thể setting suspend process của ASG (launch, health check,..)
 
## ELB:
  - ELB có DNS -> dùng route 53 trỏ đến DNS này
  - Có thể cross-zone for cross AZ
  - Có thể là public facing ELB hoặc internal 
  - Classic LB:
    - Not recommend (unless using EC2 classic)
    - Chia các request theo Round-robin
    - Stickiness session: khi enable, ELB hoặc application (tùy theo setting) sẽ generate cookie -> từ đó những request sau từ 1 client sẽ vào lại instance đã request lần đầu
    - SSL Offloading: kết nối https đến elb, nhưng kết nối http từ elb đến instance -> k phải care đến setting SSL ở phía instance
    - K hiểu được layer 7 -> k thể routing path cụ thể (../images)
    - Support: TCP, SSL, HTTP, HTTPS
    - K gắn public IP cho ELB, chỉ dùng A Record (DNS name)
  - Application LB:
    - Có thể hiểu đc layer 7 -> điều hướng base on specific path
    - Recommend to use within VPC 
    - Tạo các LB note ở các subnet, và đc assign private IP
    - Target group: 
       - Chia làm các group (có nhiều type: instance, IP, lambda function)
       - ASG sẽ intergrate với target group
       - Có thể setting action với port và protocol: forward, redirect, return, authenticate
    - Có thể điều hướng theo content-based rule:
       - Host-based: host field on HTTP header
       - Path-based: URL path on HTTP header
    - Có thể dùng với nhiều certificates (dùng với Server Name Indication (SNI))
    - Support EKS, ECS, HTTP/2, HTTPS, Sticky session, WAF, access log
  - Network LB:
    - Operate at layer 4 (TCP)
    - Design for `extreme performance`
    - Suppport static IP address
    - Uninterupted end-to-end encryption: Network LB. Network LB cũng k thể làm đc Offloading SSL
  - https://aws.amazon.com/elasticloadbalancing/features/#compare
  - Support Proxy Pro
