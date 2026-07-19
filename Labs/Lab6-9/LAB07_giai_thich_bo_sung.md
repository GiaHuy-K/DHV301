# LAB07 — Giải thích bổ sung cho Baseline Workflow

File này bổ sung phần diễn giải bằng văn bản còn thiếu trong `lab7.ipynb`, để đi kèm với phần code (train/test split, Logistic Regression, metrics) đã có sẵn trong notebook.

## 1. Biến mục tiêu và các biến tính năng (Target & Features)

**Bài toán:** Phân loại nhị phân (Binary Classification) — dự đoán sinh viên **Pass** hay **Fail**.

**Biến mục tiêu (target):** `result`, được mã hóa nhị phân: `Fail = 0`, `Pass = 1`.

**Biến tính năng (features):** thử nghiệm 2 phương án để so sánh:

| Phiên bản | Features | Lý do |
|---|---|---|
| Version A | `attendance_rate`, `quiz_avg`, `assignment_avg`, `final_score` | Dùng đầy đủ các chỉ số đánh giá quá trình học |
| Version B | `attendance_rate`, `quiz_avg`, `assignment_avg` | Bỏ `final_score` để kiểm tra xem mô hình có còn dự đoán tốt nếu không biết trước điểm cuối kỳ |

Cột định danh `student_id` và `class` **không** được đưa vào làm feature vì đây là thông tin định danh/nhóm lớp, không mang ý nghĩa dự đoán và có nguy cơ gây nhiễu mô hình.

## 2. Giải thích phương án Train/Test split

- Tỉ lệ chia: `test_size = 0.3` (70% train / 30% test).
- Sử dụng `stratify=y` để đảm bảo tỉ lệ Pass/Fail trong tập train và tập test giữ nguyên tỉ lệ như tập gốc — quan trọng vì dữ liệu có kích thước rất nhỏ (chỉ 12 sinh viên) nên nếu không stratify, tập test rất dễ bị lệch hoàn toàn về một lớp.
- `random_state=42` để đảm bảo kết quả tái lập được (reproducible).
- Với n=12, tập test chỉ còn 4 dòng — đây là một giới hạn quan trọng cần lưu ý ở phần 4.

## 3. Tóm tắt các bước tiền xử lý dữ liệu

- **Giá trị thiếu (missing values):** Dữ liệu `LAB07_student_performance_sample.csv` không có giá trị thiếu ở các cột được sử dụng, nên không cần áp dụng median imputation hay loại bỏ dòng nào.
- **Mã hóa biến phân loại:** Không có biến phân loại dạng chuỗi nào được đưa vào tập feature (`class` bị loại bỏ), nên không cần one-hot encoding cho bước này.
- **Loại bỏ định danh:** Đã loại `student_id` khỏi tập feature.
- **Kiểm tra rò rỉ dữ liệu (data leakage):** xem chi tiết ở mục 4.

## 4. Kết quả, giới hạn mô hình và đề xuất cải tiến

**Kết quả:** Cả Version A và Version B đều đạt Accuracy = 1.0 trên tập test (4/4 dự đoán đúng), với confusion matrix hoàn hảo.

**Diễn giải bằng ngôn ngữ thực tế:** mô hình dự đoán đúng toàn bộ 4 sinh viên trong tập kiểm thử ở cả hai trường hợp — có và không có điểm cuối kỳ làm feature.

**Giới hạn quan trọng cần nêu rõ:**
- Kết quả Accuracy = 1.0 **không nên được hiểu là mô hình hoàn hảo**. Tập test chỉ có 4 sinh viên, nên chỉ cần 1 dự đoán sai đã làm accuracy giảm xuống 0.75 — cỡ mẫu quá nhỏ để kết quả này có ý nghĩa thống kê đáng tin cậy.
- Về rò rỉ dữ liệu: `final_score` (Version A) có tương quan rất chặt với `result` vì kết quả Pass/Fail nhiều khả năng được suy ra trực tiếp từ `final_score` (ví dụ theo một ngưỡng điểm đậu). Nếu đúng như vậy, đưa `final_score` vào làm feature không hẳn là "rò rỉ" theo nghĩa thời gian (điểm này có thật ở thời điểm dự đoán), nhưng nó gần như làm cho bài toán trở thành tra ngược một ngưỡng đã biết, khiến mô hình mất giá trị dự đoán thực tế. Đây là lý do Version B (bỏ `final_score`) là phép so sánh hợp lý và cần thiết để đánh giá xem các chỉ số quá trình (`attendance_rate`, `quiz_avg`, `assignment_avg`) có đủ sức dự đoán kết quả hay không.
- Baseline này chưa thực hiện cross-validation, nên chưa đánh giá được độ ổn định của mô hình qua nhiều lần chia train/test khác nhau.

**Đề xuất cải tiến tiếp theo:**
- Thu thập thêm dữ liệu sinh viên để tăng cỡ mẫu, giúp kết quả đánh giá đáng tin cậy hơn.
- Áp dụng k-fold cross-validation thay vì chỉ một lần train/test split.
- So sánh thêm với các baseline khác (ví dụ Decision Tree) để đối chiếu.
- Nếu mục tiêu là dự đoán sớm (trước khi có điểm cuối kỳ), nên ưu tiên báo cáo theo Version B, vì Version A gần như "biết trước đáp số".
