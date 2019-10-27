
## Lambda:
 - 1 Lambda function nên thực hiện 1 activity, time limit là 15p
 - Lambda function lúc runtime sẽ được operate trong sandbox (độc lập với các sandbox khác)
 - Cold start (thời gian lúc đầu tạo sandbox, môi trường run lambda code) and warm start (reuse)
 - Default: outside VPC
 - Có thể move lambda vào trong VPC, tuy nhiên sẽ có 1 vài hạn chế:
    + Cần ENI attach vào sandbox của worker -> mất thêm thời gian start -> hiện tại aws đang cải thiện bằng Remote NAT, cho phép nhiều function dùng chung 1 ENI
    + Cần NAT gateway hoặc internet gateway để access internet
 - Có thể tăng performance của Lambda bằng cách khai báo các biến resuse ở ngoài function lambda handle.
 - Bộ nhớ ở sandbox là tạm thời, k phải persistence
 - Lambda layer:
     + Add thêm custom runtime support và add dependencies, library cho lambda -> save vào /opt
     + Tối đa 5 layer, max 250Mb
     + Có thể share được với account khác hoặc public

## API Gateway:
 - Support REST API và web-socket-based API
 - Có thể add WAF để protect
 - Liên kết với nhiều service: lambda(serverless), mock-test, dynamoDB,...
 - Logs: có thể intergration với Cloudwatch, Xray
 - Khi update code -> chia ra làm các stage


## EC2
 - Elastic Interface: apply for deep learning
 - AMI:
   - Khi tạo AMI: tạo snapshot của EBS -> AMI sẽ refer đến snapshot đó
   - Regional base -> có thể copy sang region khác (khi đó các snapshot đang đc refer cũng sẽ đc copy)
   - Default: chỉ có account owner có permission -> có thể setting public để share cho mọi ng, hoặc share cho 1 số account
 - VM: latest: Nitro, near bare metal
 - Các type:
   - Chia theo các dòng
   - General Purpose: dòng T: burstable, tích credit khi CPU usage < baseline, dúng khi CPU usage > baseline
 - Instance store: non persistant -> use for cache, temporary storage, need high IOPS throughput
    -> not use: share storage, durability, elasticity
 - EBS: Network storage product
   - EBS optimized: provide đeicated storage network -> improve speed, reduce contention with traditional data transfer
   - IOPS size: 256Kb chunk -> if operation is 256Kb -> count as 2 IOPS
   - Snapshot: first data is backup data -> next snapshot only save changes
   - Type:
     - General purpose: Good IOPS (100-16000 IOPS/vol)
     - Provisioned IOPS SSD: Need low latency, need higher IOPS throughput 
     - HDD optimized: lower cost, for frequently access
     - Cold HDD: lowest cost, for inferquently access
   - Pattern:  ![image](https://user-images.githubusercontent.com/40649408/67630661-8338cd00-f8ce-11e9-932b-743fae562f1a.png)
  - Placement group:
   - EC2 sẽ được settup gần nhau về mặt vật lý, same physical location -> tăng performace, giảm latency giữa các instance (k phải type nào của ec2 cũng support)
   - Cluster: Single AZ, k thể span across AZ -> highest speed, lowest latency. Can span peered VPCs in the same Region
   - Partition: Chia ra làm nhiều partition nhỏ hơn, multiAZ -> tăng resilience
   - Spread: Các instance sẽ được bố trí ở những racks khác nhau (1 AZ có tối đa 7 instance), có thể single-AZ hoặc multi-AZ -> phù hợp với dự án nhỏ, ít critical instance, có thể mix đc cái type khác nhau
