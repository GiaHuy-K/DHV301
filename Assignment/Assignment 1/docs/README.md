# PHẦN 1: ĐẶT VẤN ĐỀ (PROBLEM FRAMING)

### 1.1. Bối cảnh thực tế (Practical Context)
Trong kỷ nguyên giáo dục số, việc theo dõi sát sao kết quả học tập và tiến trình hoàn thành bài tập về nhà của học viên là yếu tố then chốt giúp các cơ sở đào tạo tối ưu hóa chất lượng giảng dạy. Hiện tại, trung tâm đang sở hữu một hệ thống ghi nhận kết quả học tập quy mô lớn. Tuy nhiên, do nguồn dữ liệu được đồng bộ tự động từ nhiều phân hệ và kết hợp nhập liệu thủ công từ nhiều giáo viên khác nhau, dữ liệu thô đang ở trạng thái phân mảnh, chứa nhiều sai sót và chưa đồng nhất. Việc xử lý và làm sạch bộ dữ liệu này là bước đi bắt buộc để ban quản lý có một cái nhìn chính xác về bức tranh đào tạo.

### 1.2. Xác định bài toán dữ liệu (Data Problem)
Tập dữ liệu thô `performance.csv` đang đối mặt với các lỗi chất lượng hệ thống nghiêm trọng bao gồm: trùng lặp dữ liệu logic (học viên bị lặp bản ghi cho cùng một môn học), nhãn phân loại không nhất quán (cột tỷ lệ bài tập chứa cả ký tự đặc biệt lẫn giá trị âm vô lý), dải điểm số vượt ngưỡng chuẩn và dữ liệu văn bản bị bỏ trống ẩn. Mục tiêu cốt lõi của dự án là thực hiện chuẩn hóa kiểu dữ liệu, làm sạch các giá trị bất thường để tạo ra một tập dữ liệu "sẵn sàng cho phân tích" (analysis-ready).

### 1.3. Câu hỏi phân tích (Analytical Questions)
Để định hướng cho quá trình khai thác dữ liệu sau này, dự án đặt ra 3 câu hỏi phân tích trọng tâm:
1. **Mối tương quan hiệu suất:** Mức độ hoàn thành bài tập về nhà (`Homework_Completion_%`) có tầm ảnh hưởng như thế nào đến kết quả điểm thi cuối kỳ (`Exam_Score`) của học viên?
2. **Đánh giá chất lượng phân môn:** Học viên tại trung tâm đang gặp khó khăn hay có xu hướng học tốt hơn ở các môn học cụ thể nào (`Subject`)?
3. **Phát hiện bất thường logic:** Làm thế nào để giải quyết triệt để hiện tượng một học viên có nhiều đầu điểm trùng lặp trên cùng một môn học mà không làm mất đi tính khách quan của dữ liệu?

### 1.4. Các chỉ số đo lường dự kiến (Tentative KPIs)
Dự án thiết lập các chỉ số observable indicators cốt lõi để theo dõi:
* **Average Exam Score:** Điểm số thi trung bình của từng phân môn (Đo lường năng lực tiếp thu).
* **Homework Completion Rate (%):** Tỷ lệ hoàn thành bài tập về nhà sau khi đã quy đổi về dạng số chuẩn (Đo lường mức độ tự giác).
* **Student Risk Status:** Trạng thái phân loại rủi ro học tập của học viên dựa trên tổ hợp điểm thi thấp và tỷ lệ bỏ bài tập cao.

---

# PHẦN 2: MÔ TẢ BỘ DỮ LIỆU (DATASET DESCRIPTION)

### 2.1. Nguồn dữ liệu (Data Source)
* **Nguồn:** [Kaggle](https://www.kaggle.com/datasets/marvyaymanhalim/student-performance-and-attendance-dataset).
* **Lý do lựa chọn:** Bộ dữ liệu này hoàn toàn phù hợp với Option B (Education). Dù cấu trúc số lượng cột tinh gọn nhưng số lượng dòng rất lớn và chứa các lỗi phân loại, lỗi logic hệ thống cực kỳ thực tế, tạo không gian lý tưởng để chẩn đoán lỗi và viết code pandas biến đổi nâng cao.

### 2.2. Thông tin tổng quan
* **Số lượng dòng:** 36,468 dòng.
* **Số lượng cột:** 5 cột.

### 2.3. Các nhóm biến quan trọng (Key Variable Groups)
Toàn bộ 5 trường dữ liệu được phân chia thành 3 nhóm chức năng rõ rệt:
1. **Nhóm Định danh & Phân loại hành chính:** `Student_ID` (Mã học viên) và `Subject` (Môn học cụ thể).
2. **Nhóm Chỉ số Hiệu suất & Hành vi:** `Exam_Score` (Kết quả bài thi) và `Homework_Completion_%` (Tiến độ bài tập về nhà).
3. **Nhóm Thông tin Định tính:** `Teacher_Comments` (Lời phê, nhận xét bằng văn bản từ giáo viên phụ trách).

### 2.4. Từ điển dữ liệu (Data Dictionary)
Bảng từ điển chi tiết cấu trúc tệp `performance.csv` kèm ghi chú chẩn đoán lỗi ban đầu:

| Tên trường (Field) | Ý nghĩa (Description) | Kiểu dữ liệu thô (Dtype) | Ghi chú Notes |
| :--- | :--- | :--- | :--- |
| `Student_ID` | Mã định danh duy nhất của từng học viên | Object | Không bị khuyết thiếu, nhưng xuất hiện lỗi trùng lặp logic khi kết hợp với trường môn học (`Student_ID` + `Subject`). |
| `Subject` | Tên môn học học viên tham gia (gồm 6 môn chính: Arabic, Science, Geography, English, Math, History) | Object | Dữ liệu nhãn chữ sạch sẽ, đầy đủ, phân bố đồng đều giữa các môn, không có lỗi chính tả. |
| `Exam_Score` | Điểm số bài thi kiểm tra của học viên | Int64 | Xuất hiện lỗi ngoài phạm vi hệ thống (Out-of-range): Có nhiều dòng điểm vượt quá ngưỡng tối đa, nằm trong dải từ 101 đến 110. |
| `Homework_Completion_%` | Tỷ lệ phần trăm hoàn thành bài tập về nhà | Object | Gặp lỗi nghiêm trọng về tính nhất quán nhãn (Inconsistent Labels): Vừa chứa ký tự chuỗi (`100%`, `80%`), vừa chứa số thô (`95`, `90`), và xuất hiện cả giá trị âm vô lý (`-5`). |
| `Teacher_Comments` | Nhận xét chi tiết bằng văn bản của giáo viên | Object | Gặp lỗi thiếu hụt giá trị ẩn (Hidden Missing Values): Có nhiều bản ghi bị bỏ trống bằng ký tự khoảng trắng chuỗi rỗng thay vì nhãn NaN chuẩn hệ thống. |

# PHẦN 5: BIẾN ĐỔI VÀ CHUẨN BỊ DỮ LIỆU (TRANSFORMATION AND PREPARATION)

### 5.1. Mô tả các bước làm sạch chính (Main Cleaning Steps)
Để đưa tập dữ liệu `performance.csv` ($36,468$ dòng) từ trạng thái thô dính nhiều lỗi hệ thống về một trạng thái hoàn toàn "sẵn sàng cho phân tích" (analysis-ready), quy trình được thực hiện một cách tuần tự và chặt chẽ qua các bước sau:
1. **Xử lý khoảng trắng văn bản ẩn (`Teacher_Comments`):** Tiến hành cắt bỏ khoảng trắng thừa ở hai đầu chuỗi (`strip`). Phát hiện và đồng nhất các ô bị bỏ trống ẩn (chỉ có dấu cách) bằng cách thay thế chúng thành nhãn mặc định `'No comment'` thay vì để trống vô nghĩa.
2. **Chuẩn hóa nhãn và xử lý giá trị âm (`Homework_Completion_%`):** Loại bỏ ký tự chuỗi `%` xuất hiện cục bộ ở một số nhãn, ép toàn bộ cột về kiểu số liên tục (`float64`). Sau đó, tính toán giá trị trung vị (Median) từ các dòng hợp lệ ($\ge 0$) để điền thay thế (imputation) cho các giá trị lỗi hệ thống mang dấu âm (`-5`).
3. **Chặn trần giá trị vượt ngưỡng (`Exam_Score`):** Sử dụng kỹ thuật giới hạn biên (Clipping) để đưa toàn bộ các mức điểm thi bị lỗi hệ thống vượt ngưỡng tối đa (từ $101$ đến $110$ điểm) quay trở về mức trần chuẩn mực cho phép là $100$ điểm.
4. **Giải quyết hiện tượng trùng lặp logic:** Tiến hành sắp xếp bảng dữ liệu theo thứ tự điểm giảm dần, sau đó áp dụng bộ lọc theo cặp định danh (`Student_ID`, `Subject`) để chỉ giữ lại bản ghi có kết quả điểm thi cao nhất (thể hiện điểm thi cải thiện hoặc thi lại tốt nhất của học viên), loại bỏ hoàn toàn các bản ghi thừa gây nhiễu cấu trúc bảng.
5. **Xây dựng các biến phái sinh (Derived Fields):** Thiết lập 4 trường thông tin mới dựa trên các logic toán học và bối cảnh giáo dục nhằm phục vụ trực tiếp cho hoạt động phân tích sâu và xây dựng mô hình cảnh báo sớm.
6. **Kiểm định chất lượng sau cùng (Validation):** Chạy lại toàn bộ các hàm chẩn đoán của Part 4 để đảm bảo bộ dữ liệu mới đã sạch tuyệt đối trước khi kết xuất.

---

### 5.2. Chuyển đổi kiểu dữ liệu và Đồng nhất nhãn (Data Type & Label Standardization)
* Cột `Subject` được ép từ kiểu chuỗi thô (`object`) sang kiểu danh mục (`category`) để tối ưu hóa hiệu năng tính toán và dung lượng bộ nhớ lưu trữ của Pandas đối với tệp dữ liệu lớn.
* Cột `Homework_Completion_%` được chuyển đổi thành công từ kiểu `object` hỗn tạp chứa dấu `%` sang kiểu dữ liệu số thực (`float64`) để sẵn sàng thực hiện các phép toán thống kê như tính trung bình, phân vị.

---

### 5.3. Tạo các biến phái sinh nâng cao phục vụ phân tích (Derived Fields)
Nhằm tối ưu hóa giá trị thông tin khai thác, 4 trường dữ liệu phái sinh đã được lập trình tích hợp vào tập dữ liệu sạch:

1. **`Exam_Status` (Biến phân loại nhị phân - Pass/Fail):** * *Công thức:* Nếu `Exam_Score` $\ge 50$ thì nhận nhãn `'Pass'`, ngược lại nhận nhãn `'Fail'`.
   * *Ý nghĩa phân tích:* Giúp ban đào tạo nhanh chóng thống kê, lập tỷ lệ phần trăm số lượng học viên đạt chuẩn hoặc trượt môn của từng phân hệ mà không cần tính toán thủ công từ dải điểm liên tục.
2. **`Academic_Grade` (Biến phân cấp học lực - 5 mức):**
   * *Công thức:* Phân loại dựa trên điểm số bài thi: $\ge 90$ (`Excellent`), $\ge 80$ (`Good`), $\ge 65$ (`Average`), $\ge 50$ (`Below Average`), và $< 50$ (`Weak`).
   * *Ý nghĩa phân tích:* Cung cấp một bộ lọc trực quan về cấu trúc năng lực học viên tại trung tâm. Hỗ trợ đắc lực cho việc đưa ra các chính sách khen thưởng nhóm xuất sắc hoặc thành lập các lớp phụ đạo nhóm học lực yếu.
3. **`Homework_Effort_Group` (Biến nhóm hành vi tự học - 3 mức):**
   * *Công thức:* Sử dụng hàm chia dải (`pd.cut`) trên cột tỷ lệ bài tập về nhà thành 3 nhóm: `Low Effort` ($\le 85\%$), `Medium Effort` ($86\% - 95\%$), và `High Effort` ($> 95\%$).
   * *Ý nghĩa phân tích:* Chuyển chỉ số phần trăm thành các nhóm hành vi nỗ lực cụ thể để phục vụ cho các bài toán phân tích tương quan giữa tính tự giác và kết quả thi cử cuối kỳ.
4. **`Academic_Risk_Flag` (Cờ cảnh báo rủi ro học tập sớm - Nhãn 0/1):**
   * *Công thức:* Gắn giá trị `1` (Nguy cơ cao) nếu học viên đồng thời thỏa mãn hai điều kiện: Điểm thi dưới trung bình (`Exam_Score` < 50) **VÀ** mức độ hoàn thành bài tập ở diện lười nhác (`Homework_Completion_%` $\le 85\%$). Ngược lại gắn giá trị `0`.
   * *Ý nghĩa phân tích:* Đây là một chỉ báo cực kỳ giá trị cho hệ thống quản lý (Early Warning System). Nó tự động định vị ra những học sinh đang rơi vào "vòng xoáy nguy hiểm" (vừa hổng kiến thức vừa thiếu tự giác) để giáo vụ trung tâm lập tức liên hệ hỗ trợ kịp thời trước khi quá muộn.

---

### 5.4. Xác thực chất lượng bộ dữ liệu sau biến đổi (Validation Results)
Sau khi thực thi toàn bộ tiến trình làm sạch trên nền tảng Python cá nhân, bộ dữ liệu đã được cô đọng lại từ $36,468$ dòng thô xuống còn **$28,736$ dòng dữ liệu sạch hoàn chỉnh**. 

Kết quả chạy hàm kiểm định chất lượng cuối cùng trả về các thông số lý tưởng:
* **Số lượng ô trống dính nhãn NaN:** 0 ô trống.
* **Số dòng dính lỗi điểm thi vượt ngưỡng (>100):** 0 dòng (Đã đưa về mức trần 100).
* **Số dòng dính lỗi tỷ lệ bài tập âm (<0):** 0 dòng (Đã thay thế bằng giá trị Median $95.0\%$).
* **Số dòng trùng lặp cặp logic (Student_ID, Subject):** 0 dòng (Cấu trúc bảng đạt chuẩn gọn gàng - Tidy Data).
* **Số dòng dính lỗi văn bản trống ẩn (Teacher_Comments):** 0 dòng (Đã chuẩn hóa thành nhãn `'No comment'`).

Tập dữ liệu hiện tại đã đạt độ tin cậy tuyệt đối và hoàn toàn sẵn sàng chuyển giao cho các giai đoạn phân tích sâu hoặc chạy thuật toán Machine Learning.