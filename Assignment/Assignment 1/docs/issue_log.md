## PART 4: DATA QUALITY DIAGNOSIS 

### 4.1. Kết quả chẩn đoán thực tế trên hệ thống
Sau khi triển khai các hàm kiểm tra độc lập trên tập dữ liệu `performance.csv` ($36,468$ dòng), hệ thống đã ghi nhận các số liệu thực tế sau:
* **Lỗi khuyết thiếu:** Không có giá trị khuyết thiếu khuyết bề nổi (`NaN = 0`). Tuy nhiên, phát hiện **3,551 dòng** dữ liệu văn bản bị bỏ trống (chỉ chứa khoảng trắng) tại cột `Teacher_Comments`.
* **Lỗi trùng lặp logic:** Phát hiện **7,732 dòng** bị trùng lặp cặp thuộc tính (`Student_ID`, `Subject`), chiếm tỷ lệ khá cao ($21.20\%$).
* **Lỗi dải giá trị vượt ngưỡng:** Phát hiện **5,139 dòng** dính lỗi điểm thi vượt quá 100 (`Exam_Score > 100`), chiếm tỷ lệ $14.09\%$. Các mức điểm lỗi cụ thể ghi nhận được gồm: `101, 102, 103, 104, 105, 106, 107, 108, 109, 110`.
* **Lỗi nhãn không nhất quán:** Cột `Homework_Completion_%` bị nhiễu định dạng nghiêm trọng khi đan xen giữa nhãn chuỗi chứa ký tự `%` (`100%`: 7296 dòng, `80%`: 7283 dòng), số thô (`95`: 7453 dòng, `90`: 7060 dòng) và đặc biệt là dính **7,376 dòng** mang giá trị âm vô lý (`-5`).

### 4.2. Nhật ký lỗi chất lượng dữ liệu (Issue Log)

| issue_id | field | issue type | example | count | percentage | impact level | suggested treatment |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **ISS01** | `Homework_Completion_%` | Nhãn không nhất quán & Giá trị âm | Dính ký tự chuỗi (`100%`, `80%`) đan xen số thô (`95`, `90`) và dính cả số âm (`-5`) | 7,376 | 20.22% | **Cao (High)** | Loại bỏ ký tự `%`, ép toàn bộ về kiểu số (Numeric), và xử lý giá trị âm `-5` bằng cách điền giá trị trung vị (Median). |
| **ISS02** | `Student_ID`, `Subject` | Trùng lặp logic (Logical Duplicates) | Một mã học viên xuất hiện nhiều dòng điểm cho cùng một môn học | 7,732 | 21.20% | **Cao (High)** | Sử dụng hàm `groupby` hoặc `drop_duplicates` để giữ lại bản ghi có điểm số cao nhất, loại bỏ dữ liệu rác gây nhiễu. |
| **ISS03** | `Exam_Score` | Giá trị ngoài phạm vi (Out-of-range) | Điểm thi vượt quá ngưỡng tối đa cho phép (xuất hiện điểm từ 101 đến 110) | 5,139 | 14.09% | **Vừa (Medium)** | Thực hiện kỹ thuật chặn trần (clipping), đưa tất cả các giá trị lớn hơn 100 về mức trần chuẩn là 100. |
| **ISS04** | `Teacher_Comments` | Giá trị thiếu ẩn (Hidden Missing) | Ô dữ liệu bị bỏ trống bằng ký tự khoảng trắng chuỗi rỗng (`''`) thay vì nhãn `NaN` | 3,551 | 9.74% | **Thấp (Low)** | Chuẩn hóa dữ liệu văn bản bằng cách thay thế các chuỗi rỗng/khoảng trắng này thành nhãn mặc định `'No comment'`. |