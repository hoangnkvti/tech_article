# Compute

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
 - Method Request/ Method Response: are API's interface with API's frontend (client)
 - Integration Request/ Integration Response: are API's interface with API's backend (often is lambda)


## EC2
 - Elastic Interface: apply for deep learning
 - AMI:
   - Khi tạo AMI: tạo snapshot của EBS -> AMI sẽ refer đến snapshot đó
   - Regional base -> có thể copy sang region khác (khi đó các snapshot đang đc refer cũng sẽ đc copy)
   - Default: chỉ có account owner có permission -> có thể setting public để share cho mọi ng, hoặc share cho 1 số account
   - Tạo AMI với Instance store volume thì phải bundle lên S3 --> tạo instance từ AMI refer from S3
 - VM: latest: Nitro, near bare metal
 - Các type:
   - Chia theo các dòng
   - General Purpose: dòng T: burstable, tích credit khi CPU usage < baseline, dúng khi CPU usage > baseline
 - Instance store: non persistant -> use for cache, temporary storage, need high IOPS throughput
    -> not use: share storage, durability, elasticity
 - Khi hibernate: 
   - Suspend-to-disk: save content từ RAM vào root volume (EBS) --> khi start sẽ load lại data từ đó vào RAM
   - You cannot enable hibernation on an existing instance (phải config từ lúc tạo instance)
 - Dedicated host: physical host is dedicated for you
   - Host Affinity: Intansce level, Khi set là `Host`: dù có restart hay stop/start thì vẫn dùng same physical drive
   - Auto-placement: Host level, Nếu để là enable thì sẽ launch ở specific host hoặc host khác có same configuration
 - EBS: Network storage product
   - EBS optimized: provide dedicated storage network -> improve speed, reduce contention with traditional data transfer
   - IOPS size: 256Kb chunk -> if operation is 256Kb -> count as 2 IOPS
   - Snapshot: first data is backup data -> next snapshot only save changes
   - Type:
     - General purpose: Good IOPS (100-16000 IOPS/vol)
     - Provisioned IOPS SSD: Need low latency, need higher IOPS throughput. Đảm bảo 3 IPOS/Gb, max 16k IOPS
     - HDD optimized: lower cost, for frequently access
     - Cold HDD: lowest cost, for inferquently access
   - Pattern:  ![image](https://user-images.githubusercontent.com/40649408/67630661-8338cd00-f8ce-11e9-932b-743fae562f1a.png)
   - EBS volumn có thể modified mà k cần detact hoặc restart instance. Chú ý: gp2 root volume k thể modified về st1 hoặc sc1, khi k phải root volume mà modified về st1 hoặc sc1 thì min volume thảo mãn đk min st1, sc1.
   - RAID0: dung lượng = tổng các raid -> focus vào performance. RAID1: dùng lượng = dùng lượng từng RAID -> focus vào fault tolerance 
   ![image](https://user-images.githubusercontent.com/40649408/71156260-faf9f700-2282-11ea-92eb-b6a33bc99e5a.png)

 - Placement group:
   - EC2 sẽ được settup gần nhau về mặt vật lý, same physical location -> tăng performace, giảm latency giữa các instance (k phải type nào của ec2 cũng support)
   - Cluster: Single AZ, k thể span across AZ -> highest speed, lowest latency. Can span peered VPCs in the same Region
   - Partition: Chia ra làm nhiều partition nhỏ hơn, multiAZ -> tăng resilience
   - Spread: Các instance sẽ được bố trí ở những racks khác nhau (1 AZ có tối đa 7 instance), có thể single-AZ hoặc multi-AZ -> phù hợp với dự án nhỏ, ít critical instance, có thể mix đc cái type khác nhau
   - Khi thêm instance, nếu gặp lỗi capacity error -> restart và launch lại instance

## Container:
  - Containerization <> virtualization: ![image](https://user-images.githubusercontent.com/40649408/67635916-e185a000-f90e-11e9-9e0c-6aa4a9c1b3a2.png)
  - Benefit: 
    - Các container chung OS -> chỉ cần quản lý memory
    - Faster to startup (có thể chưa đến 1s)
  - ECS:
    - Cluster: a collection of compute resource to host your container
    - Task definition: a configuration,... how container is created, how this container interact...
    - Container definition: define docker image, CPU, memory,... Task contains many containers
    - Service: allow ECS admin can maintain a specific number of task
  - Run ecs in 2 mode: 
    - EC2: tự manage
    - Fargate: k phải quản lý host, ec2
  - Type of networking: giá trị network mode của task
    - none: k thể interact với các resource khác, k thể port mapping
    - bridge: sử dụng internal network trong cùng 1 host
    - host: maps container ports directly to the EC2 instance's network interface directly -> you can't run multiple instantiations of the same task on a single container instance when port mappings are used.
    - awsvpc: Add ENI to task -> network sẽ giống như ec2
  - Security:
    - Dùng fargate: ENI đc attach vào task container -> setting security thông qua SG của ENI. Chỉ có thể attach role vào container (task role)
    - Với ec2: setting như security bthg (SG, NACL).. Attach role vào ec2 để privde permission để write log, contact với ecs hoặc dùng task role
  -  
