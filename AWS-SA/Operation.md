# Operation

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
  - Có thể config CloudTrail của nhiều account lưu vào 1 bucket (refer: https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)
  
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
  - Session manager: access vào ec2 instance (1-click ở console hoặc CLI) mà không cần open inbound ports and manage bastion hosts. (phải cài ssm agent ở ec2-instance). Có thể dùng policy để phân quyền user nào đc start session ở instance nào.
  - Maintainance window: chạy schedule các SSM Document ở target instance. Ví dụ như xóa folder temp ở ec2,....
    
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
  - Template:
    - Có thể reuse
    - Khi dùng hard code -> sẽ k reuse được, bởi vì khi tạo có thể xảy ra lỗi already exist -> non portable -> fix: có thể dùng parameter store, refer, add default value, pseudo paramenter, intrinsic function, k specify resouce name -> cloudformation sẽ gắn thêm random string
    - Có thể share với các account khác
  - Cross Stack Ref: Có thể cross stack (refer tới stack khác) bằng việc dùng Output ở stack kia => refer đến output đó
  - Nested Stack: 
    - Reuse block of other template. Có thể ref trực tiếp = link từ S3 -> not reusing stack, just reuse template
    - Có thể ref đc parameter từ nested stack
  - Khi tạo stack cần assign role to stack
    - Nếu k chỉ định role -> permission sẽ là account đang sử dụng
    - Có thể tạo role -> edit trust policy để cloudformation có thể assume đc role đó
  - StackSet:
    - Có thể deploy cloudformation ở multiple region ở multiple account
    - Tạo 2 role: 1 role ở account administrator, và 1 role ở account execute -> tạo trust relationship
  - Disable recovery: trade off -> sử dụng kết hợp với Cloudformation để tăng speed, perfomance
    - Backup and restore: slowest to recovery
    - Pilot light: keep minimal set of infrastructure ready to go (replica, copy file server) -> khi xảy ra disater thì switch sang -> với aws thì tốn ít cost, vì aws đã support
    - Warm standby: extend pilot light, run 1 hệ thống standby có thể nhanh chóng start khi có disater (shutdown instance, ec2 với cấu hình thấp hơn...) -> additional cost for additional infra
    - Running in multiple region
  - Custom resource: 
    - Cloudformation gửi 1 request đến lambda hoặc SNS -> thực hiện build custom resource, sau đó response về URL đc defined

## Elastic Beanstalk
 - Based on Cloud Formation
 - Application: container chứa environment, versions
 - Environment: có 2 tier
   - Worker tier:
   - Web server tier: có thể chọn platform
 - Trong 1 application có thể swap environment URL 
 - Deploy policy:
   - Immutable: tạo 1 env mới với new version -> swap URL sang
   - All at once: overwrite với new version
   ![image](https://user-images.githubusercontent.com/40649408/74605832-ea44f080-510e-11ea-8c24-0a32e121f97f.png)

 - Có thể save lại config -> restore lại version config đó
 - Có thể custom config ở file .ebextensions trong source code
 - Có thể tạo được custom platform
 
## OpsWorks
 - OpsWorks is a deployment and infrastructure management system based on Chef available in AWS
 - 1 Stack có 1 hoặc nhiều layer (gồm recipes, setting,...), mỗi layer có 1 hoặc nhiều instance
 - Auto healing: dettect the failure of instance -> re-provision this instance
 - Có 3 loại instance:
   - 24/7
   - Time-based: chỉ định time
   - Load-based: respond to load characteristics (%CPU,..)
 - After you attach a load balancer to a layer, AWS OpsWorks Stacks does the following:
   - Deregisters any currently registered instances.
   - Automatically registers the layer's instances when they come online and deregisters instances when they go offline, including load-based and time-based instances.
   - Automatically starts routing requests to registered instances in their Availability Zones.
 - We recommend that you use one of the following to update your online instances.
   - Create and start new instances to replace your current online instances. Then delete the current instances. The new instances will have the latest set of security patches installed during setup.
   - On Linux-based instances in Chef 11.10 or older stacks, run the Update Dependencies stack command, which installs the current set of security patches and other updates on the specified instances.
