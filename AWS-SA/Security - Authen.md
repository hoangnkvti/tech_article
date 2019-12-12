# Security - Authen

## IAM:
 - Real identity: Role, User (has ARN)
 - DENY always overwrite ALLOW
 - Evaluate: Explicit DENY -> Explicit ALLOW -> implicit DENY
 - Có thể chèn thêm variable: `"Condition": {"StringLike": {"s3:prefix": ["${aws:username}/*"]}}`
 - Có thể add other conditions
 - Permissions Boundaries: Setting allow or deny một hoặc nhiều action nào đó cho IAM role hoặc user -> permission của role hoặc user sẽ là phần giao của Permission boundaries, identity-based policy và resource-based policy
 
## Identity Federation:
 - Thông qua Identity Federation: không cần phải viết code authen, có thể sử dụng sẵn các identity provider (fb, gg,...), authen thông qua đó
 - Khi muốn cấp quyền cho 1 user của identity provider access resource aws -> dùng STS với api: `assumeRoleWithWebIdentity`
 - Dùng AssumeRoleWithWebIdentity: k access đc vào console, còn nếu dùng AssumeRoleWithSAML thì có thể truy cập đc vào console

## ROLE:
 - Has 2 components: `trust` policy + `permission` policy
 - `trust` policy: quy định entity nào có thể gọi đc sts:AssumeRole (service, arn,...)
 - `permission` policy: quy định role đó có những permission gì
 - VD: khi attach role vào ec2: ec2 request temporaty token -> gắn token đó vào ec2 -> các request từ ec2 sẽ gắn kèm token
 - Revoke session: thu hồi toàn bộ session đang có (add thêm 1 policy deny với toàn bộ role có TokenIssueTime < now)
  -> cannot revoke or invalidate temporary credential, they valid until expriration time -> you only can do revoke session
 - User ở organization dùng role để put object lên s3 -> s3 đó có owner là identity của role
 
 
 ## ORGANIZATION:
  - Có thể switch sang các role con
  - SCP: khác với policy bthg, quy định cái gì làm được và cái gì k làm đc -> overlay với IAM policy (intersect)
  - DENY luôn win ALLOW
  - Master account k bị ảnh hưởng bởi SCP
  - K xóa đc Organization có account
  - Có thể tạo account hoặc invite account khác (nếu email k tới thì cancel rồi tạo lại)

## KMS:
  - CMK: 
    - Key có thể dùng encrypt hoặc decrypt data (max 4Kb) đc generated bởi KMS hoặc import
    - Represent of one or more backing key 
    - Never leave KMS and region 
    - Có thể tạo alias đến CMK -> same alias with different region
    - Default: only give key permission for root account
  - Flow: Request KMS provide Data encryption key from CMK -> return 2 thing, plain text (data encryption key - DEK) và cyphertextblob (encrypted version of DEK)
   -> Encrypt data = DEK, giữ file key cùng nơi với data để biết đc mình dùng key nào, xóa DEK
   -> Khi decrypt, gửi cyphertextblob lên KMS -> trả về DEK -> decrypt
  - KMS k maintain, manage Data encryption key
  - Envolop Encryption: process, encrypt data by DEK, then encrypt DEK by another CMK -> data key, discard original plaintext version
  - Khi rotation -> add thêm 1 backing key, inactive backing key cũ. Những backing key cũ k thể encryption nhưng có thể decrypt
  - Đạt chuẩn FIPS 140-2
  
## CloudHSM
  - Là hardware security module -> có thể generate key, perform cryptographic operation. Aws chỉ mannage, chứ k access được vào key material bên trong
  - Interaction via industry standard APIs, no normal APIs: PKCS#11, JCE, CNG (KMS k support những loại này)
  - Inject vào VPC qua ENI -> resource connect đến cloudHSM qua ENI
  - Đạt chuẩn FIPS 140-3
  - Có thể access từ outside vpc (qua vpn, direct connect, vpc endpoint,..)
  - Nếu muốn control physical hardware -> có thể dùng on-premise HSM
## ACM: AWS Certificate Manager
  - Provide X509 v3 SSL/TLS certificate
  - Native integrate with ELB, Cloudfront, Elastic Beanstalk, API Gateway
  - Add SSL certificate -> phải verify ownership of domain
  - Private key thì sẽ đc store ở KMS -> các integration service sẽ tương tác với KMS để lấy private key (k bh ở trạng thái plaintext) -> ACM k dùng được với service mà k integrate (ec2,..)
 
 ##  AWS Directory Service
   - Là 1 service group các product: Amazon Cloud Directory, Simple AD, Microsoft Active Directory, AD Connector và Cognito
   - 2 group: Amazon Cloud Directory + Cognito và còn lại
   - Simple AD: open source, is not a true MAD, but it is subset of MAD,
   - Cognito: web application, federation (GG, Fb, ...), mobile -> k dùng với kiểu directory truyền thống
   - Amazon Cloud Directory: graph-based, phù hợp với store information on relationship (ví dụ như Graph DB), k phù hợp với workload, user/group
   - Simple AD, Microsoft Active Directory, AD Connector: HA, deploy ở nhiều AZ, có IP address (Giống rds). Có thể dùng để provide access to aws service, console.
  <img width="804" alt="Screen Shot 2019-10-20 at 1 35 08" src="https://user-images.githubusercontent.com/40649408/67148384-08721f80-f2da-11e9-88b3-61da9e6d4805.png">

##  AWS WAF
   - Control traffic reach to ELB, Cloudfront, API Gateway -> traffic is filtered by WAF before connect to these services
   - Unit of WAF: web ACL: Condition, rule contains conditions, web acl contains rules
   - Có 2 loại rule: regular rule, rate-based rule: base on frequency (VD: request 2000 lần trong 1 phút...)
   - Có nhiều loại condition: dựa vào địa lý, cross-site scripting, IP address,....

##  AWS Shield
  - Protect DDos, đứng trước WAF
  - Có 2 version: 
     - Standard: protect common DDos attack, always on
     - Advanced: extra cost (3k$/month) -> có nhiều feature hơn, có analytics, report và team support, cost protection (refund lại cost phát sinh bởi DDos), k mất cost cho AWS WAF, protection with Elastic IP

## AWS GuardDuty
  - Can access data source from cross account (VPC Flow logs, Cloudtrail, R53 DNS Query, Threat intelligency: finding ra các potential security issue)
  - Account có thể enable GuardDuty -> invite những account khác, và trở thành Master Guard
  - 1 Account chỉ là member của 1 GuardDuty
  - Trusted IP: IP mà k generate finding
  - Finding: k real time
  - Dựa vào data -> dùng ML để tìm ra sự bất thường -> tạo ra cloudwatch event
