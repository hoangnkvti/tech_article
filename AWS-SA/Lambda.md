
## Lambda:
 - 1 Lambda function nên thực hiện 1 activity, time limit là 15p
 - Lambda function lúc runtime sẽ được operate trong sandbox (độc lập với các sandbox khác)
 - Cold start (thời gian lúc đầu tạo sandbox, môi trường run lambda code) and warm start (reuse)
 - Default: outside VPC
 - Có thể move lambda vào trong VPC, tuy nhiên sẽ có 1 vài hạn chế:
    + Cần ENI attach vào sandbox của worker -> mất thêm thời gian start -> hiện tại aws đang cải thiện bằng Remote NAT, cho phép nhiều function dùng chung 1 ENI
    + Cần NAT gateway hoặc internet gateway để access internet
 - Có thể tăng performance của Lambda bằng cách khai báo các biến resuse ở ngoài function lambda handle.
 - Bộ nhớ ở sandbox là tạm thời, k phải persistence
 - Lambda layer:
     + Add thêm custom runtime support và add dependencies, library cho lambda -> save vào /opt
     + Tối đa 5 layer, max 250Mb
     + Có thể share được với account khác hoặc public
