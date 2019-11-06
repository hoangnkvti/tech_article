
## Service:
 - EC2: take snapshot và save ở S3 -> spread across multiAZs. Có thể copy snapshot sang region khác
 - Stateless application: lưu session ở dynamoDB -> dùng cho ASG và ELB
 - Cách chọn instance:
   - Reserved: know the base load, consitent
   - On-demand: variable load, thời điểm peak thất thường
   - Spot: analytics work, có thể kiểm soát đc lỗi, hoặc khi instance fail
   - Chú ý: On Demand Capacity Reservation, Scheduled Reservation
