# AWS Config
 - Theo dõi được config ở các resource: SG, ....
 - Intergrate với CloudTrail, SNS, tracking mỗi khi config ở resource thay đổi
 - Tạo rule -> có 2 trạng thái: complicant và no-compliant
 
# AWS Catalogue Service
 - Tạo ra product (từ cloudformation template) và portforlio để deploy -> các user khác có thể deploy thông qua aws catalogue mà không cần phải có quyền access resource
 - Các portfolio sẽ có role để deploy resource
 - Mỗi portfolio có thể có 1 hoặc nhiều product, quy định user nào có thể launch, và constrain khi run
 - User khi launch product cần có ProvisionProduct permission
 
 
