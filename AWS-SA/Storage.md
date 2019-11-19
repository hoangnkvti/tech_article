
## S3
 - Storage type: ![image](https://user-images.githubusercontent.com/40649408/68846778-f0db5a80-0710-11ea-8d06-d0f08297f8ee.png)
 - Intelligent tiering: Chia làm 2 tier, 1 cho frequent access và 1 cho infrequent access (giá giống như Standard và Standard-IA)
 - Versioning: default: overwrite -> khi enable versoning, mỗi khi put object sẽ có 1 version ID tương ứng. Các version thì độc lập, có thể khác storage type. Khi delete -> add delete marker
 - Locking: có 2 loại:
   - Retention period: K cho update hoặc delete object trong 1 khoảng thời gian 
   - Legal hold: Giống Retention period nhưng k có thời gian expire
   - Không support cross-region
 - Permission:
   - Bucket policy (recommend): quy định bởi các policy. Có thể đánh tag -> setting user nào thì có thể access tag nào....
   - S3 ACL: legacy method, có thể grant permission cho account khác,.... có thể apply cho object
 - Cross-Region Replication:
   - Replicate object từ bucket này sang bucket khác ở region khác, account khác.
   - One way only and no retroactive
   - Chỉ replicate non-system object, Lifecycle event không replicate
   - 
