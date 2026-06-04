# PHẦN 1: ĐẶT VẤN ĐỀ (PROBLEM FRAMING)

### 1.1. Bối cảnh thực tế (Practical Context)
Trong môi trường giáo dục hiện đại, việc quản lý dữ liệu học tập không chỉ dừng lại ở việc lưu trữ điểm số mà còn là công cụ để thấu hiểu hành trình của người học. Hiện tại, bộ dữ liệu được thu thập từ các hệ thống quản lý học tập (LMS) và nhập liệu thủ công, dẫn đến tình trạng thông tin bị phân mảnh và không nhất quán. Việc làm sạch bộ dữ liệu này là bước đi tiên quyết để xây dựng các mô hình dự báo giáo dục hiệu quả.

### 1.2. Xác định bài toán dữ liệu (Data Problem)
Dữ liệu thô đang gặp các vấn đề về chất lượng như: thiếu hụt giá trị (missing values), nhãn danh mục không đồng nhất (inconsistent labels) và lỗi định dạng dữ liệu. Mục tiêu của dự án là chuẩn bị một tập dữ liệu "sẵn sàng cho phân tích" (analysis-ready) để hỗ trợ ban quản lý đào tạo đưa ra các quyết định dựa trên dữ liệu.

### 1.3. Câu hỏi phân tích (Analytical Questions)
Để giải quyết bài toán trên, dự án tập trung vào 3 câu hỏi chính:
1. **Mối tương quan:** Tỷ lệ chuyên cần (Attendance) có ảnh hưởng trực tiếp đến kết quả điểm số cuối kỳ hay không?
2. **Phân loại học lực:** Dựa trên điểm số thành phần, làm thế nào để phân nhóm học viên thành các mức năng lực khác nhau?
3. **Dấu hiệu rủi ro:** Những đặc điểm hành vi nào (giờ học, giới tính, học vấn phụ huynh) là chỉ báo sớm cho việc học viên có kết quả kém? 

### 1.4. Các chỉ số đo lường dự kiến (Tentative KPIs)
* **Attendance Rate (%):** Tỷ lệ phần trăm số buổi tham gia lớp học.
* **Average Assignment Score:** Điểm trung bình của các bài tập trong quá trình.
* **Final Grade Status:** Trạng thái Đạt/Không đạt dựa trên ngưỡng điểm chuẩn.

---

# PHẦN 2: MÔ TẢ BỘ DỮ LIỆU (DATASET DESCRIPTION)

### 2.1. Nguồn dữ liệu (Data Source)
* **Nguồn:** [kaggle](https://www.kaggle.com/datasets/devansodariya/student-performance-data).
* **Lý do lựa chọn:** Bộ dữ liệu chứa đầy đủ các nhóm biến từ nhân khẩu học đến kết quả học tập, cung cấp đủ các lỗi chất lượng thực tế để thực hiện chẩn đoán và làm sạch.

### 2.2. Thông tin tổng quan
* **Số lượng dòng:** [395].
* **Số lượng cột:** [33].

### 2.3. Các nhóm biến quan trọng (Key Variable Groups)
Dữ liệu được chia thành 4 nhóm chính:
1. **Nhóm Định danh:** Mã số học viên (Student ID).
2. **Nhóm Nhân khẩu học:** Giới tính, trình độ học vấn phụ huynh, khu vực sống.
3. **Nhóm Hành vi học tập:** Thời gian tự học, tỷ lệ chuyên cần.
4. **Nhóm Kết quả:** Điểm kiểm tra giữa kỳ, điểm cuối kỳ.

### 2.4. Từ điển dữ liệu (Data Dictionary)

Dữ liệu được hệ thống hóa thành 4 nhóm biến chính để phục vụ quá trình chẩn đoán và phân tích:

#### Nhóm 1: Thông tin nhân khẩu học và bối cảnh gia đình
| Tên trường (Field) | Ý nghĩa (Description) | Predicted data types | Ghi chú ( Notes)|
| :--- | :--- | :--- | :--- |
| `school` | Trường học (GP: Gabriel Pereira, MS: Mouraria) | Categorical | Kiểm tra các giá trị viết tắt hoặc sai tên trường. |
| `sex` | Giới tính học viên (F: Nữ, M: Nam) | Categorical | Kiểm tra sự nhất quán của nhãn (M/F/Male/Female). |
| `age` | Tuổi của học viên (15 - 22) | Numeric | Kiểm tra các giá trị ngoài dải tuổi đi học (Out-of-range). |
| `address` | Loại địa chỉ (U: Thành thị, R: Nông thôn) | Categorical | Kiểm tra dữ liệu bị thiếu hoặc không đồng nhất. |
| `famsize` | Quy mô gia đình (LE3: ≤3 người, GT3: >3 người) | Categorical | Kiểm tra tính nhất quán trong cách đặt nhãn. |
| `Pstatus` | Tình trạng chung sống của cha mẹ (T: Ở chung, A: Ở riêng) | Categorical | Kiểm tra các giá trị trống hoặc không hợp lệ. |
| `Medu` | Trình độ học vấn của mẹ (0: Không - 4: Đại học) | Numeric | Kiểm tra thang đo nguyên (Integer) từ 0-4. |
| `Fedu` | Trình độ học vấn của cha (0: Không - 4: Đại học) | Numeric | Kiểm tra thang đo nguyên (Integer) từ 0-4. |
| `Mjob` | Nghề nghiệp của mẹ | Categorical | Kiểm tra lỗi chính tả hoặc các danh mục không xác định. |
| `Fjob` | Nghề nghiệp của cha | Categorical | Kiểm tra lỗi chính tả hoặc các danh mục không xác định. |
| `guardian` | Người giám hộ (Mẹ, Cha, Khác) | Categorical | Kiểm tra giá trị thiếu hoặc nhãn không chuẩn. |

#### Nhóm 2: Các yếu tố hỗ trợ và điều kiện sống
| Tên trường (Field) | Ý nghĩa (Description) | Predicted data types | Ghi chú ( Notes) |
| :--- | :--- | :--- | :--- |
| `reason` | Lý do chọn trường (Gần nhà, Uy tín, Khóa học...) | Categorical | Kiểm tra tính nhất quán của các nhãn lựa chọn. |
| `traveltime` | Thời gian di chuyển  | Numeric | Kiểm tra các giá trị vượt ngoài dải 1-4. |
| `schoolsup` | Hỗ trợ học tập từ nhà trường (yes/no) | Categorical | Kiểm tra tính đồng nhất của nhãn boolean (Yes/No/y). |
| `famsup` | Hỗ trợ học tập từ gia đình (yes/no) | Categorical | Kiểm tra tính đồng nhất của nhãn boolean. |
| `paid` | Có học thêm các lớp ngoài (yes/no) | Categorical | Kiểm tra các dòng bị để trống. |
| `activities` | Tham gia hoạt động ngoại khóa (yes/no) | Categorical | Kiểm tra định dạng chuỗi đồng nhất. |
| `nursery` | Từng học trường mẫu giáo (yes/no) | Categorical | Kiểm tra tính nhất quán dữ liệu lịch sử. |
| `higher` | Mong muốn học lên đại học (yes/no) | Categorical | Biến quan trọng, cần kiểm tra kỹ lỗi logic. |
| `internet` | Có kết nối internet tại nhà (yes/no) | Categorical | Kiểm tra giá trị thiếu. |
| `romantic` | Đang trong mối quan hệ tình cảm (yes/no) | Categorical | Kiểm tra tính đồng nhất nhãn. |

#### Nhóm 3: Hành vi và thói quen sinh hoạt
| Tên trường (Field) | Ý nghĩa (Description) | Predicted data types | Ghi chú ( Notes) |
| :--- | :--- | :--- | :--- |
| `studytime` | Thời gian tự học mỗi tuần (1: <2h - 4: >10h) | Numeric | Kiểm tra tính hợp lệ của thang đo. |
| `failures` | Số lần trượt môn trong quá khứ | Numeric | Phát hiện các giá trị âm hoặc vô lý. |
| `famrel` | Chất lượng quan hệ gia đình (1: Rất tệ - 5: Tốt) | Numeric | Kiểm tra dải giá trị từ 1-5. |
| `freetime` | Thời gian rảnh sau giờ học (1: Ít - 5: Nhiều) | Numeric | Kiểm tra dải giá trị từ 1-5. |
| `goout` | Mức độ đi chơi với bạn bè (1: Ít - 5: Nhiều) | Numeric | Kiểm tra dải giá trị từ 1-5. |
| `Dalc` | Tiêu thụ rượu ngày thường (1: Ít - 5: Nhiều) | Numeric | Kiểm tra dải giá trị từ 1-5. |
| `Walc` | Tiêu thụ rượu cuối tuần (1: Ít - 5: Nhiều) | Numeric | Kiểm tra dải giá trị từ 1-5. |
| `health` | Tình trạng sức khỏe (1: Tệ - 5: Tốt) | Numeric | Kiểm tra dải giá trị từ 1-5. |

#### Nhóm 4: Kết quả học tập (Performance Metrics)
| Tên trường (Field) | Ý nghĩa (Description) | Predicted data types | Ghi chú ( Notes) |
| :--- | :--- | :--- | :--- |
| `absences` | Số buổi vắng học (0 - 93) | Numeric | Phát hiện các giá trị cực biên (outliers) hoặc số âm. |
| `G1` | Điểm số giai đoạn 1 (0 - 20) | Numeric | Kiểm tra dải điểm hợp lệ (0-20). |
| `G2` | Điểm số giai đoạn 2 (0 - 20) | Numeric | Kiểm tra dải điểm hợp lệ (0-20). |
| `G3` | Điểm tổng kết cuối kỳ (0 - 20) | Numeric | Biến mục tiêu, cần kiểm tra kỹ lỗi trống hoặc sai số. |