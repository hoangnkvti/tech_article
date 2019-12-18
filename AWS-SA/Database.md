# Database

## DB Model:
 - Relational: 
   - ACID system -> làm chậm tốc độ xử lý và giảm khả năng scale
   - K phù hợp với khi relationship hay bị thay đổi (như mxh)
 - Non-retional (NoSQL)
   - Key-value (DynamoDB, Redis): lưu cache, session,...
   - Document (mongoDB): nested information
   - Column-based (Redshift): data analytics, reporting, warehousing
   - GraphDB (Neo4j, AWS Neptune): social network, dynamic relationship

## RDS:
 - Mỗi instance sẽ có 1 EBS gắn kèm
 - Khi tạo DB:
   - Production: MultiAZ, theo cơ chế Master-Slave (đồng bộ giữa master và slave, bthg k thể access slave, khi master fail -> auto chuyển CNAME sang slave)
   - Dev: tạo 1 instance ở 1 AZ
 - Khi snapshot -> có cả thông tin transaction log -> có thể recovery Point in Time
 - ReadReplica: K thể write, tăng performance read, có thể setting ở region khác
 - Có thể change params config DB ở Parameter group
 
## Aurora:
 - Full managed by aws -> performance up 5x for MySQL, 3x for PostgreSQL
 - Các DB share underlying storage, dùng chung cluster storage volume -> giảm latency, tăng performance. Cluster storage gồm nhiều instance ở nhiều AZ -> khi write vào primary thì sẽ span đến toàn bộ các instance
 - K dùng cơ chế Master-Slave, mà chỉ có Primary Instance và Replica
 - Nhiều read replicas dùng chung 1 read endpoint -> dùng 1 endpoint access
 - Best practice: multiple replicas với nhiều AZ, hoặc region khác
 - K defined upfront storage amount khi start
 - Backtrack: Rollback DB về 1 trc thời điểm nào đó
 - Clone: lúc đầu refer đến original -> khi thay đổi source hoặc clone thì tạo ra version data mới (differential) (copy-on-write protocol) -> tốc độ clone nhanh hơn
 - Có thể multi-writer -> tăng performance và khả năng scale
 - Backup: incremetal and continous -> lưu ở S3
 - Parallel query: tăng performance query 
 - Có thể add auto scaling cho read-replica
 - Aurora Global DB:
   - Tạo Cross-region replica
   - Khi write -> đồng thời sync với region khác qua Replication Server <-> logs are streamed between replication server and agent of remote region
   - Khi 1 region fail -> promote region khác có thể full read và write trong vòng chưa đầy 1p
   
## Aurora Serverless:
 - Phù hợp với serverless architecture
 - 1 ACU (Aurora capacity unit): 2CPU/4Gb RAM ~ 64CPU/122Gb
 - Tính tiền giống lambda: charge theo capacity sử dụng
 - Aws maintain warm instance pool -> khi có connect thì instance từ đó sẽ đc allocated, attach vào cluster storage volume. Connection được quản lý bởi shared proxy layer
 - Limit: k có multi-AZ -> fail thì sẽ tạo instance ở AZ khác
 - Get data thông qua API
 
## Athena
 - Serverless product query on S3
 - Có thể query nhiều loại data structure
 - K import data mà chỉ cần tạo bảng -> query trực tiếp từ data ở S3
 - Phù hợp với việc query log data
 
 ## DynamoDB:
  - Primary key(unique): chứa partition key (hash key) + sort key (optional)
  - Read opreration:
    - Query: Phải có điều kiện single PK -> chỉ có PK, sortkey là có thể dùng để search. Dù dùng filter thì bản chất capacity sử dụng vẫn k thay đổi.
    - Scan: Consume capacity để quét toàn bộ table (khác với query, chỉ tính với PK và sort key) -> có thể search toàn bộ field
  - Data được lưu theo partition:
    - 1 partition max là 1000 WCU và 3000 RCU -> khi quá giới hạn thì sẽ partition sẽ tự tăng. 
    - Khi write thì aws sẽ hash partition key -> xác định partition storage để lưu vào -> cùng 1 partition key (khác sort key) thì sẽ ở gần nhau -> nhiều khi việc truy xuất partition sẽ k đều -> vì vậy nên để partition key càng khác nhau càng tốt.
  - Ví dụ khi muốn search = 1 field k phải partition key -> dùng đến Scan. Tuy nhiên scan sẽ tốn nhiều capacity -> tạo *Index*:
    - Local secondary index: create khi tạo table, share capacity với main table. Cùng partition key với main table nhưng khác sort key
    - Global secondary index: Khác partition key và sort key với main table -> có thể dùng query
  - Consistency model:
    - Eventual consistency: Khi add 1 item, query có thể chưa lấy đc ngay vì phải mất 1 thời gian đồng bộ...
    - Có 2 loại read: eventually và strong -> strong sẽ lấy latest data, sẽ tốn 2RCU, còn eventually lấy data hiện tại, tốn 1RCU (với mỗi 4Kb size)
  - Có backup và snapshot lưu ở s3
  - Có thể enable restore point-in-time
  - Đã support transaction
  - Có thể trả theo kiểu reversed capacity
  - Adaptive capacity: Khi mà 1 partition đc truy cập quá nhiều và vượt qua hard limit -> enable chức năng này giúp partition này có thể mượn capacity từ các partition khác
  - DynamoDB stream: store changes to DynamoDB tables. Có thể list Key only, new image (data mới), old image (data cũ) hoặc cả new và old image -> có thể dùng lambda để trigger
  - TTL: có thể set thời gian expire cho item ở DB
  - Global table: multi region -> tạo replica ở các region khác. K thể add thêm region sau khi populate data
  - Fined-grained access: restrict permission đến Dynamo cho IAM user. (dùng cho mobile app, web)
  - DAX (Accelerator): Cache for dynamoDB
  
## Neptune:
  - NoSQL Graph database
  - Phù hợp cho Social network, knowledge graph, fraud detection, recommendation engine

## QLDB:
  - Document based DB, có tính ACID
  - Serverless product (giống DynamoDB)
  - Quantum Ledger Database: provides cryptographically verifiable transactions with an append-only ledger
  - Tracking toàn bộ changes trong DB -> phù hợp với ngân hàng, y tế, bầu cử,...
  - Cơ chế: Hash data -> save lại. Khi có thay đổi thì lại hash data mới + hash của data cũ... lần lượt như vậy -> tracking được thứ tự, data có bị thay đổi hay k, k thể thay đổi data cũ...

## DocumentDB (with MongoDB Compatibility):
  - Là Nosql document DB
  - Gồm nhiều cluster, 1 single primary và nhiều replica -> tăng read workload. Muốn tăng write workload thì phải tăng cấu hình instance
  - Support recovery point in time (in retention)
  - Cũng có share storage (cách hoạt động giống Aurora). Khi primary instance fail -> biến 1 replica thành primary có thể write đc
  
## Elastic Cache:
  - Cache service -> improve speed and reduce DB load
  - Can host web session
  - Có thể chọn các loại engine: Memcached, Redis: ![image](https://user-images.githubusercontent.com/40649408/70062104-c2261500-1628-11ea-92bb-6326c2f3d9be.png)
  - Redis dùng với case data type complex, HA, nhiều instance ở multi-AZ, nhưng memcached support multi-thread
