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
   
