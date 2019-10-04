# IAM:
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
 
 
 # ORGANIZATION:
  - Có thể switch sang các role con
  - SCP: khác với policy bthg, quy định cái gì làm được và cái gì k làm đc -> overlay với IAM policy (intersect)
  - DENY luôn win ALLOW
  - Master account k bị ảnh hưởng bởi SCP
