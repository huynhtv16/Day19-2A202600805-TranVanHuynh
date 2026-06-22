# Reflection

Anti-pattern nhóm tôi dễ gặp nhất là **small-file problem**. Dữ liệu quan sát LLM thường được đẩy liên tục theo micro-batch; nếu mỗi batch tạo một file, số file sẽ tăng nhanh dù tổng dung lượng chưa lớn. Metadata và thao tác mở file khi đó có thể chiếm nhiều thời gian hơn chính việc đọc dữ liệu, làm dashboard chậm và tăng chi phí truy vấn.

NB2 cho thấy rõ rủi ro này: 200 lần append tạo 200 file nhỏ. Sau compaction và Z-order theo `user_id`, bảng còn 55 file; truy vấn điểm nhanh hơn 8,6 lần và chỉ một file trong 55 file cần được đọc. Với dữ liệu của nhóm, tôi sẽ theo dõi kích thước/số lượng file, chạy compaction định kỳ theo SLA, chọn khóa clustering dựa trên mẫu truy vấn thực tế và tránh partition theo cột có cardinality quá cao. Tôi cũng sẽ benchmark trước khi tối ưu để không biến OPTIMIZE thành một tác vụ tốn chi phí nhưng ít giá trị.
