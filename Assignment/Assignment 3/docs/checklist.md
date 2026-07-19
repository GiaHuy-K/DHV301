# Bảng Kiểm tra Trạng thái Sẵn sàng Phát hành (Publish-Readiness Checklist)

| Hạng mục nội dung kiểm tra chất lượng | Kết quả (Yes/No) | Minh chứng / Ghi chú chi tiết |
|---|---|---|
| Tất cả tên của các hàm đo lường (measures) đã được đặt rõ ràng, chuẩn hóa và dễ hiểu chưa? | Yes | 5 measures: Total Exams, Average Exam Score, Pass Rate, Risk Rate, Risk Level — đặt tên theo chuẩn PascalCase với khoảng trắng, không viết tắt. |
| Các định nghĩa bản chất của từng chỉ số KPI đã được biên soạn bằng ngôn ngữ phổ thông dễ hiểu chưa? | Yes | Xem bảng mục 1, file `dax_kpi_notes.md`. |
| Các hàm đo lường cốt lõi đã hoàn thành bước kiểm thử dưới tác động phối hợp của các bộ lọc/slicers chưa? | Yes | 4 test đã thực hiện: Slicer test, Group total test, Filter context test, Manual spot check — xem `dax_kpi_notes.md` mục 3. |
| Giao diện dashboard đã tuân thủ cấu trúc phân cấp trực quan và có hướng đọc rõ ràng chưa? | Yes | Trang Overview: tiêu đề → 4 KPI cards (hàng trên) → 2 biểu đồ so sánh (hàng giữa: Risk Rate theo môn, Academic_Grade donut) → bố cục đọc trên-xuống, trái-phải. |
| Trạng thái cấu hình mặc định của các bộ lọc và slicers khi mở báo cáo lên có hợp lý không? | Yes | Slicer Subject mặc định ở trạng thái "is (All)" — không lọc sẵn, hiển thị toàn cảnh khi mở báo cáo lần đầu. |
| Các trường thông tin chi tiết thừa hoặc có tính chất nhạy cảm đã được ẩn đi hoặc loại bỏ khỏi danh mục hiển thị chưa? | Yes | Cột `Teacher_Comments` đã bị loại khỏi mô hình dữ liệu ngay tại bước Power Query (không nạp vào Fact_Performance). Xem `privacy_note.md`. |
| Đường dẫn kết nối nguồn dữ liệu (Data Source Path) có tường minh không? | Yes | Dữ liệu import tĩnh từ file CSV cục bộ, đường dẫn được ghi rõ trong `README.md` mục 3, không phụ thuộc gateway hay dịch vụ ngoài. |
| Cơ chế làm mới dữ liệu của báo cáo đã được công bố rõ ràng chưa? | Yes | Ghi rõ trong `README.md` mục 3: import tĩnh, refresh thủ công. |
| File nộp bài phiên bản cuối cùng đã được đặt tên rõ ràng, chuẩn quy cách chưa? | Yes | `student_performance_dashboard.pbix`, đặt trong thư mục `/pbix` theo đúng cấu trúc quy định. |
| Một người duyệt bài độc lập có thể tự đọc hiểu toàn bộ ý nghĩa của dashboard này mà không cần tác giả phải đứng bên cạnh giải thích trực tiếp không? | Yes | Mỗi biểu đồ có tiêu đề mang thông điệp cụ thể (không viết tắt); README + dax_kpi_notes cung cấp đầy đủ ngữ cảnh, giả định và giới hạn dữ liệu. |
