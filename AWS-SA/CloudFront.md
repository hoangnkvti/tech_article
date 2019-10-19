# Cloudfront:
 - CloudFront is a Content Delivery Network (CDN)
 - Unit of configuration của Cloudfront là Distribution
 - Tạo distribution để delivery content:
   - Web distribution: delivery static and dynamic file (.css, .js, .html, graphic), using http or https
   - RMTP: Khi dùng Adobe Flash Media với RMTP protocol (* must store media file on S3)
 - 1 distribution thì có thể có nhiều behavior, luôn có 1 behavior default map all request: setting TTL (thời gian cache file ở edge location), có thể restrict access setting (by signed URL or signed cookie):
   - Khi dùng custom SSL: 
     - Với 1 vài browser cũ: có thể dùng dedicated IP cho mỗi edge location, mất thêm phí
     - Với browser mới hơn: dùng option support SNI (no charge)
   - Recommend: TLS1.1_2016
 - Có thể kết hợp với WAF
 - Regional Cache: act as bigger version. Khi request -> check edge location. Nếu k có -> Check ở regional cached. Nếu có thì fetch về edge location -> trả về user. Nếu k có nữa thì sẽ fetch origin ở cả edge location và regional cache.
 - Có thể assicate với lambda function
 - By default: k có geo-restriction. 
 - Add nhiều Custom Origin với nhiều source khác nhau (s3, onpremise, elb,...) -> dùng behavior setting để điều hướng với từng loại resource
 - Khi dùng custom origin -> có public IP address
 - Cloudfront có thể ở trạng thái private
 
 ## Security:
  - Có 2 layer: WAF (phải setting manually) -> Cloudfront
  - SSL: Cloudfront phải có certificate về https (public certificate)
  - Restrict origin access: Dùng OAI (Origin Access Identity) để resitrct access chỉ cho phép cloudfront access file S3, k thể truy cập public file đó
  - SignedURL:  giống như signedURL của S3, khi enable -> behavior sẽ trở thành private
  - Cookies: extend, có thể cho phép access object type hoặc area, do not work with RMTP distribution
  - Geo Restriction: 
     - whitelist and blacklist, based on location of IP address
     - Khi dùng signedURL -> xử lý ở code lúc generate signedURL (third party). Ngoài ra có thể check đc nhiều thông tin khác
  - Field-level encryption: Phía cloudfront có public key, encrypt -> send đến Custom origin có private key
 
 ## Optimize:
  - Có thể view statistics về cache hit and miss
  - Query String forward: Default: None -> nếu với query string khác nhau resource khác thì nên enable
  - Có thể custom performance based on cookie, header
  - 
