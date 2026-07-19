# Báo cáo EDA, Trực quan hóa & Storytelling
## Kết quả học tập của học viên (Tùy chọn B)

**Bộ dữ liệu:** `performance_cleaned.csv` — 28.736 dòng × 9 cột, 11.580 học viên (mỗi học viên có 1–6 dòng, ứng với các môn học khác nhau).
**Tính kế thừa dữ liệu:** Bộ dữ liệu này là bản đã làm sạch (không có giá trị thiếu ở bất kỳ cột nào), được sử dụng liên tục làm nền cho phân tích EDA. *(Ghi chú: nếu đây là dữ liệu kế thừa trực tiếp từ Assignment 1, hãy bổ sung 1–2 câu mô tả các bước làm sạch đã áp dụng ở đó để phần này hoàn chỉnh theo đúng yêu cầu đề bài.)*

---

## Phần 1. Khung phân tích (Analytical Framing)

**Mục tiêu tổng quát:** Mô tả hình dạng phân bổ của điểm thi và mức độ hoàn thành bài tập, so sánh hiệu suất học tập giữa các môn học, và kiểm tra mối liên hệ giữa nỗ lực làm bài tập với kết quả thi — nhằm hỗ trợ trung tâm đào tạo xây dựng hệ thống cảnh báo sớm cho học viên có nguy cơ học tập kém.

**Câu hỏi phân tích trọng tâm:**
1. `Exam_Score` và `Homework_Completion_%` có hình dạng phân bổ như thế nào? Có tồn tại outlier không?
2. Kết quả thi có khác biệt đáng kể giữa 6 môn học hay không?
3. Mức độ nỗ lực làm bài tập (`Homework_Effort_Group`) có tương quan với điểm thi cuối kỳ không?
4. Tỷ lệ học viên rơi vào nhóm rủi ro học tập (`Academic_Risk_Flag`) có khác nhau giữa các môn không?
5. Cơ cấu học lực (`Academic_Grade`) phân bổ ra sao trên toàn bộ dữ liệu?

---

## Phần 2. Thống kê mô tả và Phân bổ

### Bảng thống kê mô tả

| Biến số | count | missing % | mean | median | std | min | Q1 | Q3 | max | skewness |
|---|---|---|---|---|---|---|---|---|---|---|
| Exam_Score | 28.736 | 0.00 | 76.91 | 79.00 | 19.15 | 40 | 61 | 96 | 100 | -0.33 |
| Homework_Completion_% | 28.736 | 0.00 | 91.99 | 95.00 | 6.80 | 80 | 90 | 95 | 100 | -0.75 |

### Biểu đồ 1–2: Phân bổ Exam_Score

![Histogram Exam_Score](figures/01_hist_exam_score.png)
![Boxplot Exam_Score](figures/02_box_exam_score.png)

**Nhận định:** Điểm thi có median (79,0) lớn hơn mean (76,91), skew = -0,33 → phân bổ **lệch trái nhẹ**: đa số học viên đạt điểm khá-giỏi, một bộ phận nhỏ điểm thấp kéo dài đuôi trái, thể hiện rõ ở cụm cột cao vọt gần mốc 95–100 trên histogram. Áp dụng phương pháp IQR: ngưỡng hợp lệ là [Q1 − 1,5×IQR, Q3 + 1,5×IQR] = [8,5 ; 148,5]. Do khoảng biến thiên thực tế của dữ liệu chỉ là 40–100, **không có điểm nào bị xác định là outlier** theo tiêu chí IQR — mặc dù về mặt trực quan phân bổ có vẻ "chất đống" ở vùng điểm cao, đây không phải là bất thường thống kê vì bản thân IQR (Q3 − Q1 = 35 điểm) khá rộng.

### Biểu đồ bổ sung: Phân bổ Homework_Completion_%

![Histogram HW Completion](figures/07_hist_hw_completion.png)
![Boxplot HW Completion](figures/07b_box_hw_completion.png)

**Nhận định:** Đây là biến rời rạc chỉ nhận 4 giá trị (80/90/95/100), mean = 91,99, median = 95,00, skew = -0,75 (lệch trái rõ hơn Exam_Score). Vì Q1 = 90 và Q3 = 95, IQR rất hẹp (chỉ 5 điểm phần trăm) khiến ngưỡng dưới chỉ ở mức 82,5. Kết quả là **toàn bộ 5.796 lượt (20,17% dữ liệu) mang giá trị 80% bị gắn nhãn "outlier"** theo tiêu chí IQR thuần túy. Đây là một ví dụ kinh điển cho thấy giới hạn của phương pháp IQR khi áp dụng cho biến có ít giá trị rời rạc và phân bổ hẹp: về bản chất, mức hoàn thành 80% không phải lỗi dữ liệu mà là một giá trị hợp lệ (tương ứng nhóm *Low Effort*), chỉ đơn thuần hiếm gặp hơn so với phần đông học viên đạt 90–100%. Kết luận này cần được nêu rõ để tránh loại bỏ nhầm dữ liệu hợp lệ khi xử lý outlier.

---

## Phần 3. So sánh nhóm (Group Comparisons)

**Câu hỏi:** Exam_Score có khác biệt giữa các môn học (Subject) hay không?

| Subject | count (n) | mean | median | std |
|---|---|---|---|---|
| Arabic | 4.868 | 77,04 | 80 | 19,19 |
| English | 4.784 | 77,11 | 80 | 19,11 |
| Geography | 4.792 | 76,58 | 79 | 19,14 |
| History | 4.708 | 77,13 | 80 | 19,23 |
| Math | 4.782 | 76,47 | 78 | 19,18 |
| Science | 4.802 | 77,14 | 80 | 19,05 |

![Boxplot theo môn học](figures/03_box_examscore_by_subject.png)

**Nhận định:** Cỡ mẫu giữa các môn khá cân bằng (n dao động 4.708–4.868 lượt/môn, đủ lớn để phát hiện khác biệt nhỏ nếu tồn tại). Tuy vậy, điểm trung bình chỉ dao động trong khoảng rất hẹp — từ 76,47 (Math) đến 77,14 (Science), **chênh lệch tối đa chỉ 0,67 điểm**, nhỏ hơn nhiều so với độ lệch chuẩn nội nhóm (~19 điểm). Boxplot minh họa trực quan: phân bổ của cả 6 môn gần như trùng khớp nhau (median 78–80, khoảng biến thiên 40–100 giống hệt). **Kết luận:** không có bằng chứng cho thấy sự khác biệt có ý nghĩa thực tế (practical significance) giữa các môn học — dù n đủ lớn để bắt được khác biệt nhỏ, mức chênh lệch quan sát được quá nhỏ so với độ phân tán tự nhiên của điểm số để mang giá trị phân tích.

**Biểu đồ bổ sung — So sánh theo nhóm nỗ lực làm bài tập:**

![Bar theo effort](figures/06_bar_examscore_by_effort.png)

| Homework_Effort_Group | count (n) | mean | median | std |
|---|---|---|---|---|
| Low Effort | 5.796 | 76,69 | 79 | 19,20 |
| Medium Effort | 17.232 | 76,99 | 79 | 19,14 |
| High Effort | 5.708 | 76,91 | 79 | 19,13 |

Tương tự như so sánh theo môn học, điểm trung bình giữa 3 nhóm nỗ lực gần như không khác biệt (chênh lệch tối đa 0,3 điểm) — bar chart gần như phẳng tuyệt đối, dù nhóm "High Effort" không cho điểm cao vượt trội như kỳ vọng trực giác.

---

## Phần 4. Phân tích mối quan hệ (Relationship Analysis)

**Cặp biến:** `Homework_Completion_%` và `Exam_Score`.

![Scatter HW vs Exam](figures/04_scatter_hw_vs_exam.png)

**Hệ số tương quan Pearson:** r = 0,0025 (p = 0,666, n = 28.736).

**Nhận định:** Hệ số tương quan gần như bằng 0 và không có ý nghĩa thống kê (p >> 0,05). Biểu đồ phân tán xác nhận điều này: tại mỗi mức hoàn thành bài tập (80/90/95/100%), điểm thi trải dài đều từ 40 đến 100, đường trung bình (đường đỏ nằm ngang) gần như bất biến qua các mức. Kết quả này nhất quán với bảng so sánh nhóm ở Phần 3: dù chia theo `Homework_Effort_Group` (dựa trực tiếp trên `Homework_Completion_%`), điểm trung bình các nhóm gần như bằng nhau.

**Ranh giới diễn giải (Association vs. Causation):** Kết quả này cho thấy **không tồn tại mối liên hệ thống kê (association)** giữa mức hoàn thành bài tập và điểm thi trong bộ dữ liệu này — đây là một phát hiện đáng chú ý vì trái với giả định thông thường rằng làm bài tập đầy đủ hơn sẽ dẫn tới điểm thi cao hơn. Cần nhấn mạnh: (1) đây chỉ là kết luận về mối liên hệ thống kê, **không thể quy kết bất kỳ quan hệ nhân quả nào** dù là thuận hay nghịch; (2) sự vắng mặt của tương quan tuyến tính không loại trừ khả năng tồn tại mối quan hệ phi tuyến tính hoặc bị chi phối bởi biến ẩn (ví dụ: động lực học tập tổng thể, năng lực môn học, chất lượng làm bài tập chứ không chỉ tỷ lệ hoàn thành số lượng); (3) `Homework_Completion_%` trong bộ dữ liệu này chỉ có 4 giá trị rời rạc — biến này có thể đo lường "số lượng bài đã nộp" hơn là "chất lượng/nỗ lực học tập thực chất", nên không loại trừ khả năng đây là một proxy variable yếu cho khái niệm mà nó đại diện.

---

## Phần 5. Đánh giá và Cải tiến trực quan hóa (Chart Critique)

**Câu hỏi nền:** Số lượt thi phân bổ ra sao giữa 6 môn học?

| Trước (Poor Chart) | Sau (Good Chart) |
|---|---|
| ![Before pie](figures/05a_before_pie.png) | ![After bar](figures/05b_after_bar.png) |

| Khía cạnh | Biểu đồ Trước (Pie) | Biểu đồ Sau (Bar) |
|---|---|---|
| Tiêu đề | Chung chung: "Subject" — không truyền tải phát hiện nào | Cụ thể, mang thông điệp: "Số lượt thi phân bổ khá đồng đều giữa 6 môn học (4700–4900 lượt/môn)" |
| Gắn nhãn | Chỉ có tên môn học quanh vòng tròn, không có số liệu hay đơn vị đo | Trục x có đơn vị rõ ràng ("Số lượt thi — số dòng dữ liệu"), mỗi cột có nhãn số liệu chính xác |
| Thiết kế | 6 màu sắc rực rỡ, khó phân biệt lát nào lớn hơn lát nào khi các phần gần bằng nhau | Một màu đơn sắc nhất quán, sắp xếp giảm dần theo giá trị — tập trung vào so sánh độ lớn |
| Thông điệp | Không đưa ra so sánh định lượng nào — người đọc phải tự ước lượng góc để so sánh | Cho phép so sánh chính xác ngay lập tức nhờ trục số và nhãn giá trị |
| Tính trung thực | Dễ gây cảm giác các môn "chênh lệch" nếu người đọc ước lượng góc sai (sai số thị giác phổ biến với pie chart nhiều lát gần bằng nhau) | Thể hiện đúng bản chất: chênh lệch giữa môn nhiều nhất (Arabic, 4.868) và ít nhất (History, 4.708) chỉ là 160 lượt (~3,4%) |

**Kết luận cải tiến:** Với 6 nhóm có giá trị gần bằng nhau, pie chart là lựa chọn kém vì mắt người rất khó so sánh chính xác diện tích/góc của các lát gần bằng nhau. Horizontal bar chart sắp xếp theo giá trị giảm dần giải quyết triệt để vấn đề này, đồng thời tiêu đề đã được viết lại để phản ánh đúng phát hiện chính (phân bổ đồng đều), thay vì chỉ mô tả biến số một cách trung tính.

---

## Phần 6. Câu chuyện phân tích (Analytical Narrative)

### 5 phát hiện cốt lõi

1. **Điểm thi lệch trái nhẹ, không có outlier theo IQR:** Exam_Score có mean 76,91, median 79,0, skew -0,33; đa số học viên đạt khá-giỏi (34,83% xếp loại "Excellent"). Khoảng biến thiên 40–100 nằm gọn trong ngưỡng IQR hợp lệ.
2. **Không có khác biệt thực chất giữa 6 môn học:** dù cỡ mẫu đủ lớn (4.708–4.868/môn), chênh lệch điểm trung bình giữa môn cao nhất và thấp nhất chỉ 0,67 điểm — nhỏ hơn nhiều so với độ lệch chuẩn nội nhóm (~19 điểm).
3. **Không tồn tại tương quan giữa mức hoàn thành bài tập và điểm thi:** r = 0,0025 (p = 0,666) — trái với kỳ vọng thông thường. Cả ba nhóm Low/Medium/High Effort đều có điểm trung bình gần như giống hệt nhau (76,7–77,0).
4. **Tỷ lệ rủi ro học tập thấp nhưng có khác biệt theo môn:** tỷ lệ chung 2,34% (672/28.736 lượt); Math có tỷ lệ cao nhất (2,8%), English thấp nhất (2,0%) — chênh lệch gần 1,4 lần, dù vẫn ở mức thấp tuyệt đối.
5. **Homework_Completion_% là biến rời rạc, hẹp (80/90/95/100):** điều này khiến phương pháp IQR gắn nhãn "outlier" cho 20,17% dữ liệu (giá trị 80%) một cách máy móc, dù đây là giá trị hợp lệ — minh chứng cho việc không nên áp dụng IQR một cách mù quáng với biến rời rạc/hẹp.

### Giới hạn của dữ liệu — điều chưa thể chứng minh

- Dữ liệu **không hỗ trợ bất kỳ khẳng định nhân quả nào**: không thể kết luận việc làm bài tập nhiều hơn "gây ra" điểm thi cao hơn hay thấp hơn — chỉ có thể nói không tìm thấy liên hệ thống kê.
- `Homework_Completion_%` chỉ đo *tỷ lệ số bài đã nộp*, không phản ánh chất lượng làm bài, thời gian đầu tư hay năng lực thực chất của học viên — nên phát hiện "không tương quan" cần được diễn giải trong giới hạn của proxy variable này, không nên suy rộng thành "làm bài tập không quan trọng".
- Bộ dữ liệu không có biến kiểm soát (ví dụ: điểm đầu vào, số giờ tự học, đặc điểm giáo viên) nên không thể loại trừ các yếu tố nhiễu tiềm ẩn (confounders) có thể vừa ảnh hưởng đến nỗ lực làm bài tập vừa ảnh hưởng đến điểm thi theo cách triệt tiêu lẫn nhau.
- Chênh lệch tỷ lệ rủi ro giữa các môn (2,0%–2,8%) tuy nhất quán về hướng nhưng biên độ nhỏ; chưa có kiểm định thống kê chính thức (ví dụ chi-square) để khẳng định khác biệt này có ý nghĩa thống kê hay chỉ là biến động ngẫu nhiên.

### Đề xuất bước phân tích tiếp theo

1. Thực hiện kiểm định thống kê chính thức (ANOVA cho so sánh môn học/nhóm effort; chi-square cho tỷ lệ rủi ro theo môn) để xác nhận các khác biệt quan sát được có ý nghĩa thống kê hay chỉ là nhiễu ngẫu nhiên trên cỡ mẫu lớn.
2. Thu thập hoặc bổ sung biến đo lường "chất lượng" nỗ lực học tập (ví dụ: điểm quiz, thời gian tự học, số lần nộp trễ) để kiểm tra lại giả thuyết về mối liên hệ giữa nỗ lực và kết quả thi bằng một proxy tốt hơn `Homework_Completion_%`.

---

## Danh mục biểu đồ (Figure Index)

| # | File | Loại | Mục đích |
|---|---|---|---|
| 1 | `01_hist_exam_score.png` | Distribution | Hình dạng phân bổ Exam_Score |
| 2 | `02_box_exam_score.png` | Distribution | Kiểm tra outlier Exam_Score (IQR) |
| 3 | `03_box_examscore_by_subject.png` | Group Comparison | So sánh Exam_Score giữa 6 môn học |
| 4 | `04_scatter_hw_vs_exam.png` | Relationship | Tương quan Homework_Completion_% vs Exam_Score |
| 5a/5b | `05a_before_pie.png` / `05b_after_bar.png` | Chart Critique | So sánh Trước/Sau: phân bổ số lượt thi theo môn |
| 6 | `06_bar_examscore_by_effort.png` | Additional | Điểm thi trung bình theo nhóm nỗ lực bài tập |
| 7/7b | `07_hist_hw_completion.png` / `07b_box_hw_completion.png` | Distribution (Additional) | Phân bổ Homework_Completion_% và outlier |
| 8 | `08_bar_riskflag_by_subject.png` | Additional | Tỷ lệ rủi ro học tập theo môn |

*(Toàn bộ file ảnh gốc nằm trong thư mục `/figures` đi kèm báo cáo này.)*