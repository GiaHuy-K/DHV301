# Ghi chú Bảo mật Dữ liệu (Data Privacy Note)

## 1. Đánh giá mức độ nhạy cảm của dữ liệu

Bộ dữ liệu `performance_cleaned.csv` sử dụng cho dashboard này **không chứa thông tin định danh cá nhân trực tiếp**:

- `Student_ID` là mã ẩn danh dạng `SXXXXX` (ví dụ: S00396), không phải họ tên thật, không thể tự nó truy ngược ra danh tính học viên.
- Không có các trường như họ tên, số điện thoại, email, địa chỉ, ngày sinh, hay bất kỳ thông tin liên hệ nào.
- Cột `Teacher_Comments` — trường duy nhất chứa văn bản tự do — đã được xác định (qua phân tích Assignment 2) là **văn bản sinh ngẫu nhiên, vô nghĩa** (không phải nhận xét giáo viên thật), và đã bị **loại bỏ hoàn toàn** khỏi mô hình dữ liệu ngay tại bước Power Query trước khi nạp vào Power BI.

## 2. Biện pháp xử lý đã áp dụng

| Biện pháp | Mô tả |
|---|---|
| Loại bỏ trường không cần thiết | Xóa `Teacher_Comments` tại bước Power Query, không xuất hiện trong mô hình dữ liệu cuối cùng. |
| Gom tụ dữ liệu (Aggregation) | Toàn bộ visual trên dashboard hiển thị số liệu ở mức tổng hợp (theo môn học, theo học lực), không hiển thị bất kỳ dòng dữ liệu cấp học viên cá nhân nào trên giao diện chính. |
| Giới hạn phạm vi bàn giao | File `.pbix` và dữ liệu chỉ chia sẻ trong phạm vi học thuật (giảng viên chấm bài), không công khai rộng rãi. |

## 3. Row-Level Security (RLS)

Trong khuôn khổ bài đánh giá nhập môn này, **không cấu hình RLS thực tế** vì dữ liệu không phân theo vai trò/khu vực người dùng cụ thể và không có yêu cầu riêng từ giảng viên.

**Thảo luận lý thuyết (mở rộng):** nếu triển khai trong môi trường doanh nghiệp/giáo dục thực tế với nhiều vai trò truy cập (ví dụ: giáo viên bộ môn chỉ được xem dữ liệu môn mình dạy, quản lý cấp trường chỉ xem dữ liệu trường mình), có thể áp dụng RLS bằng cách:
1. Tạo bảng ánh xạ vai trò (ví dụ `User_Subject_Mapping`: Email người dùng ↔ Subject được phép xem).
2. Viết DAX filter tại **Modeling → Manage roles**, ví dụ: `[Subject] = LOOKUPVALUE(User_Subject_Mapping[Subject], User_Subject_Mapping[Email], USERPRINCIPALNAME())`.
3. Gán từng người dùng vào vai trò tương ứng khi publish lên Power BI Service, đảm bảo mỗi giáo viên chỉ thấy dữ liệu môn học được phân công, còn quản lý cấp cao thấy toàn bộ.

## 4. Kết luận

Dữ liệu sử dụng trong dashboard này ở mức rủi ro thấp về quyền riêng tư do đã ẩn danh mã học viên và loại bỏ trường văn bản tự do duy nhất. Không cần thêm biện pháp masking/hạn chế truy cập bổ sung cho phạm vi bài tập học thuật hiện tại.
