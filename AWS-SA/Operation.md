## Cloudwatch:
 - Data point: data metric tại 1 thời điểm time point
 - Mỗi monitoring data của service publish tại cloudwatch có namespace riêng. (có thể tạo custome namespace)
 - Có thể tạo alarm base on status of metric -> khi qua threshold thì trigger action: Notification SNS, Autoscaling action or Ec2 action
 - Có thể push data từ IoT device to cloudwatch thông qua cloudwatch endpoint
 - Có thể tạo custom metric bằng cách push log từ on-premise server, EC2 (install cloudwatch agent). Ngoài ra có thể tạo filter cho log
  - Cloudwatch log group có thể intergrate với Elasticsearch (searching data) hoặc lambda (processing real-time data)
 
 ## Cloudtrail:
  - Log tất cả tương tác API với aws
  - Enable by default (90 days)
  - Có thể intergrate với nhiều service khác như S3, Cloudwatch log,... (tạo custom trail)
  - Có thể apply trail với all region, hoặc all organization
  - Advanced configuration:
    - Management events: Log cả những management action đặc biệt mà traditional trail k log (vì k hẳn là api): Login, setup logging,...
    - Data events: Log certain object or resource level activities: object-level events ở S3, hoặc function-level events ở lambda
    - Khi setting destination -> S3: có thể config prefix, encryption, log validation...
  - Khi apply với all organization: Account member sẽ được kế thừa, nhìn đc config của single trail nhưng k sửa được
  - Apply với Cloudwatch, assign role to cloudtrail -> gửi trail log đến cloudwatch log stream -> tạo filter metric với action tương ứng
  
## Route53 Logging:
  - Intergrate với cloudwatch log -> assign role to route53 logging -> send log to cloudwatch log
  - Log chứa thông tin: timestamp, DNS zone, query, ....

## System Manager:
  - Managed Instance: Manage Ec2 instance hoặc on-premise instance (SSM agent sẽ được install ở instance và đc assign role, cần activation nếu là on-premise)
  - Inventory: SSM collect inventory data information about instance -> display in inventory dashboard
  - SSM Document: Define what action SSM perform, hoặc là state mà bạn mong muốn (desired state) -> khi attach vào managed instance, các instance có thể perform những aciton này
  - Action:
    - Automation: Allow automation set of action, like workflow. 
    - Run command: Execute document
    - State manager: is a desired state engine, automates the process of keeping your Amazon EC2 and hybrid infrastructure in a state that you define.
    - Patch Manager: patching managed instances (ec2 or on-premise) with both security related and other types of updates
  - Parameter Store:
    - Provide secure storage for configuration data and secrets, sensitive data
    - Store hierarchically
    - Data có thể lưu ở string, secure string, string list
    - Các resource như lambda, ec2, ecs,.. có thể access để lấy data từ parameter store
    - Serverless, resilient, scalable
    
## Cloud Formation:
  - Template: json hoặc yaml format, define resource and architecture
  - Stack: Khi run 1 template -> stack -> update, delete resource. Có thể intergrate stack với SNS
  - Update stack: 3 loại:
    - Update with no interruption: Update mà k change resource physical ID
    - Update with some interruption: Retain physical ID nhưng có 1 vài interruption. VD khi change thuộc tính của EC2 instance
    - Replacement: Recreate resource with new physical ID. VD: change engine của RDS,...
  - Change set:
    - Describe toàn bộ config sẽ đc change, difference giữa stack mới và cũ
    - Show toàn bộ action mà update behavior sẽ làm
    - Có thể setting để member junior upload -> senior review, approve và execute change set thông qua stack policy
    - 
