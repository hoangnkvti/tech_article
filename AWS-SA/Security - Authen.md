# IAM:
 - Real identity: Role, User (has ARN)
 - DENY always overwrite ALLOW
 - Có thể chèn thêm variable: `"Condition": {"StringLike": {"s3:prefix": ["${aws:username}/*"]}}`
 - Có thể add other conditions
 - 

## ROLE:
 - Has 2 components: `trust` policy + `permission` policy
 - `trust` policy: quy định entity nào có thể gọi đc sts:AssumeRole (service, arn,...)
 - `permission` policy: quy định role đó có những permission gì
 - VD: khi attach role vào ec2: ec2 request temporaty token -> gắn token đó vào ec2 -> các request từ ec2 sẽ gắn kèm token
 - Revoke session: thu hồi toàn bộ session đang có (add thêm 1 policy deny với toàn bộ role có TokenIssueTime < now)
  -> cannot revoke or invalidate temporary credential, they valid until expriration time -> you only can do revoke session
 - User ở organization dùng role để put object lên s3 -> s3 đó có owner là identity của role
 
 
 # ORGANIZATION:
  - 
