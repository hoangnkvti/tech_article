# Storage

## S3
 - Storage type: ![image](https://user-images.githubusercontent.com/40649408/68846778-f0db5a80-0710-11ea-8d06-d0f08297f8ee.png)
 - Intelligent tiering: Chia làm 2 tier, 1 cho frequent access và 1 cho infrequent access (giá giống như Standard và Standard-IA)
 - Versioning: default: overwrite -> khi enable versoning, mỗi khi put object sẽ có 1 version ID tương ứng. Các version thì độc lập, có thể khác storage type. Khi delete -> add delete marker
 - Requester Pays Buckets: 
   - Owner trả phí lưu trữ, còn requester trả phí request data
   - Khi request, requester sẽ gửi thêm header `x-amz-request-payer` (những request không có header hoặc request khác sẽ đều bị tính cho owner)
   - Không support anynomous request, SOAP, BitTorrent
   - Nếu dùng assumeRole rồi access -> account mà có role đó sẽ phải trả phí
 - Locking: có 2 loại:
   - Retention period: K cho update hoặc delete object trong 1 khoảng thời gian 
   - Legal hold: Giống Retention period nhưng k có thời gian expire
   - Không support cross-region
 - Permission:
   - Bucket policy (recommend): quy định bởi các policy. Có thể đánh tag -> setting user nào thì có thể access tag nào....
   - S3 ACL: legacy method, có thể grant permission cho account khác,.... có thể apply cho object
 - Cross-Region Replication:
   - Replicate object từ bucket này sang bucket khác ở region khác, account khác.
   - One way only and no retroactive
   - Chỉ replicate non-system object, Lifecycle event không replicate
 - Encryption:
   - SSE-C: dùng customer key -> customer full responsibility cho key
   - SSE-S3: default, s3 manage key
   - SSE-KMS: dùng kms service quản lý key -> chỉ loại này mới support CRR
 - Optimize:
   - Multiple upload: single max 5Gb -> multi can up 5Tb in parallel
   - Transfer Acceleration: Dùng cloudfront edge location (từ edge location -> s3 dùng backbone transit -> tăng tốc độ) -> access đến location gần nhất để lấy data
   - Partition + Object naming: 1 partition: 3500P - 5500G per second -> nếu cần nhiều hơn thì sử dụng prefix khi naming object
 - Glacier:
   - Gồm nhiều vault (unique in region), mỗi region có khoảng 1000 vault
   - Archive thì đc lưu ở vault -> gồm unique ID và description(optional, chỉ đc set khi upload, và k thể modify)
   - K có user-defined metadata với archive
   - Có thể request list inventory -> nhận đc notify sau khi inventory xong
   - Archive k thể edit, chỉ có thể add và delete. K có name, chỉ có id

## EFS:
 - Shared file system
 - Có thể mount vào file system
 - NFS -> cần có Mount target -> nên tạo mỗi target ở mỗi AZ trong vpc, mỗi mount target có IP và SG
 - Có thể enable encrypt by KMS or custom CMK -> both at rest and at transis
 - Permission có thể quản lý theo 2 cách: permission của linux và của aws
 - Có thể dùng AWS Backup để backup data
 - Có thể dùng AWS DataSync để migration từ on-premise -> EFS
 - 2 performance mode  -> k thể edit sau khi create
   - Gerenal
   - Max I/0
 - Throughput mode:
   - Bursting Throughput: scales as the size of your file system in the standard storage class grows.
   - Provisioned Throughput: instantly provision the throughput of your file system (in MiB/s) independent of the amount of data stored.
 - Greate for big data and analytic, content sharing
 
## FSx:
 - Manage 3rd-party file system: window file server -> connect to MicrosoftAD, Lustre,...
 - Mỗi FSx ở 1 AZ -> để HA thì mỗi AZ setting 1 FSx, có thể đồng bộ = DFS replication
 - Có thể access từ EC2, Workspace, VMWare on cloud

## Storage gateway:
 - Dùng để migration data từ on-premise -> aws hoặc extend on-premise storage
 - Tape gateway:
   - Khi ở on-premise dùng VTL (vitural tape library) để quản lý backup -> integrate với Tape gateway
   - Sync tape file đến S3 -> nếu k dùng thì move đến glacier
 - File Gateways:
   - Khi migration file từ on-premises lên S3: mapping 1-1 file với object S3 -> greate for migration
   - Support SMB, NFS
 - Volume Gateways:
   - Volume có thể mapping vào on-premise server như là EBS
   - Cached volume:
     - Primary storage là S3
     - Frequently access file thì sẽ đc cache locally
   - Store volume:
     - Dùng S3 để lưu snapshot
     - Primary is local
   - In the cached mode, your primary data is written to S3, while retaining your frequently accessed data locally in a cache for low-latency access.
   - In the stored mode, your primary data is stored locally and your entire dataset is available for low-latency access while asynchronously backed up to AWS.
