# Student Performance Dashboard — README

## 1. Mục tiêu Dashboard

Dashboard theo dõi hiệu suất học tập của học viên theo môn học, phục vụ ban quản lý đào tạo / giáo vụ giám sát nhanh sức khỏe học tập tổng thể và nhận diện môn học có tỷ lệ rủi ro cao để ưu tiên can thiệp sớm.

**Đối tượng người dùng đích:** Ban giám hiệu / quản lý đào tạo.

**Câu hỏi cốt lõi dashboard trả lời:**
1. Điểm trung bình và tỷ lệ đỗ hiện tại toàn hệ thống là bao nhiêu?
2. Môn học nào có tỷ lệ rủi ro học tập cao nhất, cần ưu tiên can thiệp?
3. Cơ cấu học lực (Excellent/Good/Average/Below Average/Weak) phân bổ ra sao?
4. Kết quả có thay đổi hợp lý khi lọc theo từng môn học cụ thể không?

## 2. Nguồn dữ liệu & Tính liên tục

- **File nguồn:** `performance_cleaned.csv` — 28.736 dòng, 11.580 học viên (mỗi học viên có 1–6 lượt thi ứng với các môn khác nhau).
- **Kế thừa từ Assignment 1:** dữ liệu đã qua bước làm sạch — loại bỏ 7.732 dòng trùng lặp (Student_ID + Subject), chuẩn hóa `Exam_Score` về thang 40–100 (raw ban đầu có giá trị lên tới 110), chuẩn hóa `Homework_Completion_%` về 4 mức hợp lệ (80/90/95/100), sửa lỗi định dạng hỗn hợp ("100%" vs "100") và giá trị lỗi nhập liệu ("-5" được xác định là lỗi của "95").
- **Kế thừa từ Assignment 2 (EDA):** đã xác nhận không có tương quan giữa `Homework_Completion_%` và `Exam_Score` (r = 0,0025, p = 0,666), và không có khác biệt điểm thi có ý nghĩa thực tế giữa 6 môn học (chênh lệch tối đa 0,67 điểm). Các phát hiện này định hướng dashboard **không** xây KPI ngụ ý quan hệ nhân quả giữa nỗ lực làm bài tập và điểm thi.

## 3. Cơ chế làm mới dữ liệu

Import **tĩnh** (static import) từ file CSV vào Power BI Desktop qua Power Query. Không cấu hình auto-refresh / gateway / nguồn dữ liệu trực tuyến. Khi cần cập nhật dữ liệu mới, người dùng thay file CSV nguồn tại cùng đường dẫn và bấm **Refresh** thủ công trong Power BI Desktop.

## 4. Mô hình dữ liệu

Star schema đơn giản gồm 2 bảng:
- **Fact_Performance** (fact table) — grain: 1 dòng = 1 lượt thi của 1 học viên trong 1 môn học cụ thể.
- **Dim_Subject** (dimension table) — 6 dòng, mỗi dòng là 1 môn học duy nhất (Arabic, English, Geography, History, Math, Science).
- **Quan hệ:** Dim_Subject[Subject] (1) → Fact_Performance[Subject] (nhiều), cross-filter direction: Single, active: Yes.

## 5. Kết quả KPI tổng quan (đã kiểm chứng thực tế trên dashboard)

| KPI | Giá trị |
|---|---|
| Total Exams | 28.736 |
| Average Exam Score | 76,9 |
| Pass Rate | 88,61% |
| Risk Rate | 2,34% |

**Cơ cấu học lực (Academic_Grade):** Excellent 34,83% · Average 20,46% · Below Average 18,41% · Good 14,9% · Weak 11,39%.

**Risk Rate theo môn (giảm dần):** Math (~2,8%) > History (~2,5%) > Geography (~2,3%) > Arabic (~2,2%) ≈ Science (~2,2%) > English (~2,0%).

## 6. Giả định & Giới hạn kỹ thuật

- `Academic_Grade` và `Exam_Status` là các cột **dẫn xuất trực tiếp** từ `Exam_Score` theo ngưỡng cố định (không phải đo lường độc lập), đã có sẵn trong file cleaned từ Assignment 1.
- `Homework_Completion_%` chỉ có 4 giá trị rời rạc (80/90/95/100) — phản ánh tỷ lệ số bài đã nộp, không phản ánh chất lượng làm bài hay nỗ lực học tập thực chất.
- Dữ liệu có dấu hiệu là **dữ liệu tổng hợp/giả lập** (Teacher_Comments là văn bản sinh ngẫu nhiên vô nghĩa; Academic_Grade/Exam_Status là hàm bậc thang tuyệt đối của Exam_Score; các thống kê mô tả gần như giống hệt nhau giữa các môn học và nhóm nỗ lực). Các KPI trên dashboard nên được diễn giải trong phạm vi bài tập học thuật, không đại diện cho một hiện tượng giáo dục thực tế.
- Dashboard không đưa ra bất kỳ khẳng định nhân quả nào giữa `Homework_Completion_%` và `Exam_Score`, vì Assignment 2 đã xác nhận không có tương quan thống kê giữa hai biến này.

## 7. Cấu trúc thư mục nộp bài

```
project_root/
├── pbix/
│   └── student_performance_dashboard.pbix
├── screenshots/
│   ├── model_view.png
│   ├── dashboard_overview.png
│   ├── slicer_test_math.png
│   ├── slicer_test_science.png
│   ├── group_total_test.png
│   └── filter_context_test.png
├── docs/
│   ├── README.md
│   ├── dax_kpi_notes.md
│   ├── checklist.md
│   └── privacy_note.md
└── data/
    └── performance_cleaned.csv
```
