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
    - Leader Node: Take data, and distribute to compute node.
    - Compute node: execute query. Trong compute node chưa 
    - Compute node: execute querynhi
    - Compute node: execute query
