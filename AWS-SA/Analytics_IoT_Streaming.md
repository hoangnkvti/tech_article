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
   
