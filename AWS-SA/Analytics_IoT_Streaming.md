# Analytics + IoT Streaming

## MapReduce EMR
 - Có thể process lượng data rất lớn một cách song song
 - 2 main components: MAP và REDUCE
 - Split data into manageable bits -> perform operation -> combine for reporting
 - Có 3 loại node:
   - Master: manage cluster, control distribution workload, monitor health of other cluster component, như là HDFS Name node. Khi fail -> toàn bộ các node khác sẽ fail -> k dùng spot instance. K thể thay đổi sau khi setting, thường sử dụng lâu dài -> hay dùng reserved instance
   - Core: có từ 0->n node, perform task: mapping task, reducing task... như là HDFS Data node -> khi xảy ra fail sẽ dẫn đến loss data
   - Task: optional, có thể execute task như core node, nhưng k liên quan đến HDFS -> khi xảy ra fail sẽ k ảnh hưởng nhiều -> có thể dùng spot instance
 - Có thể retrieve và output tới S3
 - Ngoài HDFS, còn có EMRFS (s3 backed cluster storage): persistent data store beyond lifetime of cluster
 - EMR run cluster in VPC -> default: các cluster ở cùng 1 subnet, 1 AZ để tăng performance 
 - Có thể chọn để dùng nhiều application. VD: Hive: dùng đc SQL...
 - Scale:
   - Uniform instance group: offer a simplified setup: 1 master instance group chứa 1 Ec2 instance + core instance group (>1) + more task instance group (<=48) --> setting autoscale bằng cách thêm instance ở core và task...
   - Instance fleet: mỗi loại node có nhiều type (up to 5), có thể define target capacity for on-demand instance or spot instance.
 - 1 số tip cho performance:
   - Cluster cùng region với S3
   - Some best practices: ![image](https://user-images.githubusercontent.com/40649408/70156234-f6680700-16f6-11ea-95c5-80d1c962196c.png)
   

## Kinesis:
 - Kinesis: ingest a huge amount of real-time streaming data
 - Producer: device put data to kinesis. Ex: IoT device, Mobile app,...
 - Consumer: entity consume record from kinesis. Ex: Ec2, Kinesis Firehose, lambda
 - Kinesis data stream: 
   - 2 type: video stream and data stream. 
   - Default retention period: 24h -> up to 7days with addition charge
   - Video stream:
      - Có thể playback. Dùng GetMedia (GetMedia API), HLS (Http live streaming, dùng URL ở location bar của safari, Edge), MPEG-HASH
 - Shard: how Kinesis stream scale. 1 shard = 1Mb/s write - 2 Mb/s read - 1k records/s
 - Data records: Gồm sequence number, partition key và blob data.
 - Public zone -> khi kết nối ec2 cần vpc endpoint
 
## Kinesis Firehose:
  - Delivery real time data (from Kinesis or other sources) to supported service
  - Có thể thực hiện transformation data theo 2 cách:
    - Lambda function:
    - Convert format
  - Destination: có thể là S3, Redshift, Elastics Search, Splunk
  - K support encryption at rest:
    - Khi source là Kinesis stream -> encrypt luôn ở Kinesis stream
    - Khi source khác put data vào -> enable server-side encryption
    
## Kinesis Data Analysis:
  - Allow SQL queries to be executed against streaming real-time data passing through Kinesis streams or Kinesis Data Firehose.
  - Có thể Lambda để pre-processing data

## Redshift:
  - Redshift is a petabyte scale data warehousing and data analysis solution
  - Column-based DB
  - Gồm các node:
    - Leader Node: Take data, and distribute to compute node. Khi là 1 node riêng biệt -> free of charge
    - Compute node: execute query. Trong compute node chứa nhiều slices (2-16), các slice này sẽ xử lý data. Ví dụ nếu 6 slices thì số file bắt đầu xử lý từ s3 nên là 6, 12, 18...
  - Deploy inside VPC
  - Support encryption: at rest and in transit
  - Có thể resize cluster:
    - Elastic: chỉ có thể change số node -> nhanh hơn (5-10p)
    - Classic: để toàn bộ cluster -> read only mode -> provision new cluster với new size -> transfer data from old to new -> lâu hơn
  - Khi tạo bảng có 4 type of distribute:
     - Auto: tự động thay đổi loại
     - Even: dữ liệu của bảng được chia đều ra các slides, nodes => dữ liệu ít join
     - Key: phân chia dữ liệu theo điều kiện matching value được xác định bởi leader node => dữ liệu query nhiều, join lớn
     - All: bảng nằm đầy đủ ở tất cả các node => dùng với các bảng demension nhỏ
  - Disater recovery:
     - CÓ thể config snapshot manually or auto
     - Có thể setting cross-region snapshot
     - Có thể restore only table
     - Data ở slice thì đều có replicate copy ở slice khác ở compute node khác
  - Không có replica
  - Khi có 2 process, 1 chậm, 1 nhanh và để k làm ảnh hưởng đến queries của process -> Dùng Workload Management group, tạo 2 group tách biệt

## IoT architecture:
  - IoT shadows: Represent state of real device after device gateway -> sync communication giữa device và bên sử dụng
  - Cơ chế pub-sub, dùng MQTT topic
  - ioT Rule: Trigger event, interact với other resource tùy vào data, message
  - Basic Ingest: Có thể config IoT things có thể interact trực tiếp đến rule mà k qua Topic -> reduce cost

## Quicksight:
  - QuickSight is a BI and data visualization tool available in AWS
  - Intergrate với nhiều service, lấy data -> show data visualization

## Elastic Search:
  - Elasticsearch is an AWS implementation of the ELK stack (Elasticsearch, Logstash, and Kibana) as a service
  - Có thể intergrate với nhiều service, đặc biệt là aws log, Kinesis Firehose
  - ELK stack (Elasticsearch, Logstash, and Kibana)
    - Elasticsearch: Store, search và analyze. Thực ra là document DB -> store information as document
    - Kibana: Data visualization component, có thể share cho ng khác
    - Logstash: Logging work flow system. Có thể transform log information
    - Beats: ingest the log to elasticsearch cluster. Có thể install agent ở OS
  - Master nodes: provide management function. Có thể có nhiều vì HA, resilience
  - Data nodes: execute search -> return data. Là individual server in cluster
  - Có thể setting node của cluster access from vpc hoặc public access. Thực ra nó k nằm ở trong vpc, mà ở 1 network khác nhưng có thể connect vpc qua ENI
  - Có thể enable Cognito to authen vào Kibana
  - Index: collection of related document -> có thể break thành các shard -> speard các shard vào data node để achieve resilience, high throughput
