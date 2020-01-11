# Application Intergration

## SQS
  - Short polling: Poll only visible message
  - Long polling: 1-20seconds, wait until a message is available in a queue
  - Max 256Kb each message
  - Types:
    - Standard queue: Đảm bảo mỗi message delivery at least one nhưng k đảm bảo theo thứ tự
    - FIFO queue: Đảm bảo thứ tự message khi gửi, nhưng lose ability to scale infinitely
  - Visibility timeout: how long message is invisible after polled -> nếu sau thời gian này k có delete action thì sẽ pop back lại queue
  - Apply queue giúp hệ thống lose coupled/decoupled
  - Flow: add message to queue -> work instance hoặc lambda poll message từ queue và perform action    
  - Dead letter queue: xử lý những message mà fail to processing
  - Message Group Identifier: chia thành nhiều group trong queue. Khi kết hợp với FIFO queue --> đảm bảo gửi message parallel, trong cùng 1 group thì sẽ theo order FIFO
  
## SNS:
  - Topic: 
    - Object to which publish message (<=256kB)
    - Subscribers subscribe to topic to receive message
  - Subscribers:
    - An endpoint to a message is sent: Http, Https, email, SQS, lambda,...
  - Publisher: trigger sending message: application, s3 event, cloudwatch alarm,...
  - Fan-out: 
    - SQS k support multiple consumer (dùng Kinesis), nhưng có thể kết hợp SNS với SQS: tạo multiple queues -> all subcribes to SNS topic
  - A durable, HA service
  
## Amazon MQ
 - Managed messaging broker by AWS, base on Apache ActiveMQ (open source)
 - Use when you want to use standard compliant message broker: JMS API, AMQP, MQTT, STOMP, OpenWire
 - Khác với SNS và SQS là public zone, AmazonMQ nằm trong VPC
 - Support Topic, Queue, Virtual topic -> support 1 to 1, 1 to many
 - K phải sản phẩm của aws -> K intergrate nhiều với aws service
 
## Step Functions and 
 - A workflow orchestration
 - Serverless product,  k cần manage infra
 - State machine: control flow of states
 - State có 7 types: Task (perform a task), Choice (make decision, loop), Parallel, Wait, Fail, Succeed, Pass
 - Có thể tương tác với ng dùng. VD: Task execute lambda -> gửi email đến user qua SES -> trong mail có button trigger API Gateway -> execute lambda -> invoke fail or succeed state
 - 
 
## Simple Workflow Service
 - A workflow orchestration: Simple set of action
 - Deprecated by aws -> support but not recommend
