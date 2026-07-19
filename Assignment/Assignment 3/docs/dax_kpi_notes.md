# DAX & KPI Documentation

## 1. Bảng tổng hợp Measures

| Measure | Công thức | Ý nghĩa (ngôn ngữ phổ thông) | Loại (theo yêu cầu ma trận) |
|---|---|---|---|
| `Total Exams` | `COUNTROWS(Fact_Performance)` | Tổng số lượt thi ghi nhận trong hệ thống (1 lượt = 1 học viên × 1 môn) | 1. Gom tụ cơ bản |
| `Average Exam Score` | `AVERAGE(Fact_Performance[Exam_Score])` | Điểm thi trung bình, tự động tính lại theo bất kỳ bộ lọc nào (môn, học lực...) | 3. Trung bình / chuẩn hóa |
| `Pass Rate` | `DIVIDE(CALCULATE([Total Exams], Fact_Performance[Exam_Status]="Pass"), [Total Exams])` | Tỷ lệ % lượt thi đạt (Exam_Score ≥ 50) trên tổng số lượt thi | 2. Tỷ lệ / phần trăm |
| `Risk Rate` | `DIVIDE(CALCULATE([Total Exams], Fact_Performance[Academic_Risk_Flag]=1), [Total Exams])` | Tỷ lệ % lượt thi được gắn cờ rủi ro học tập | 2. Tỷ lệ / phần trăm |
| `Risk Level` | `SWITCH(TRUE(), [Risk Rate]>=0.03,"High", [Risk Rate]>=0.02,"Medium", "Low")` | Tự động phân loại mức cảnh báo (Cao/Trung bình/Thấp) dựa trên ngưỡng Risk Rate, thay đổi theo mọi ngữ cảnh lọc | 4. Logic nâng cao (SWITCH) |

## 2. Công thức đầy đủ

```dax
Total Exams = COUNTROWS(Fact_Performance)

Average Exam Score = AVERAGE(Fact_Performance[Exam_Score])

Pass Rate = 
DIVIDE(
    CALCULATE([Total Exams], Fact_Performance[Exam_Status] = "Pass"),
    [Total Exams]
)

Risk Rate = 
DIVIDE(
    CALCULATE([Total Exams], Fact_Performance[Academic_Risk_Flag] = 1),
    [Total Exams]
)

Risk Level = 
SWITCH(
    TRUE(),
    [Risk Rate] >= 0.03, "🔴 High",
    [Risk Rate] >= 0.02, "🟡 Medium",
    "🟢 Low"
)
```

## 3. Kết quả kiểm thử hành vi KPI (KPI Behavior Testing)

### 3.1 Slicer test
**Câu hỏi kiểm thử:** Khi chọn lọc theo môn học cụ thể trên slicer, `Average Exam Score` có tự động đổi chính xác không?

| Slicer chọn | Average Exam Score hiển thị |
|---|---|
| Math | 76,47 |
| Science | 77,14 |

**Kết luận:** Measure phản hồi đúng theo filter context của slicer. Số liệu khớp với bảng so sánh nhóm đã tính ở Assignment 2 (Math: mean 76,47 / n=4.782; Science: mean 77,14 / n=4.802).
**Minh chứng:** `slicer_test_math.png`, `slicer_test_science.png`.

### 3.2 Group total test
**Câu hỏi kiểm thử:** Tổng `Total Exams` cộng từ từng dòng Subject trong Table có khớp với giá trị trên Card tổng không?

| Subject | Total Exams |
|---|---|
| Arabic | 4.868 |
| English | 4.784 |
| Geography | 4.792 |
| History | 4.708 |
| Math | 4.782 |
| Science | 4.802 |
| **Total (dòng tổng trong Table)** | **28.736** |
| **Card tổng (không lọc)** | **28.736 (hiển thị 29K)** |

**Kết luận:** Khớp tuyệt đối, không có chênh lệch — xác nhận measure `Total Exams` cộng gộp đúng ở mọi cấp độ phân rã.
**Minh chứng:** `group_total_test.png`.

### 3.3 Filter context test
**Câu hỏi kiểm thử:** `Risk Rate` có phản ánh đúng khi phân rã theo từng môn học không?

Kết quả trên bar chart "Risk Rate by Subject" (sắp xếp giảm dần):

| Hạng | Subject | Risk Rate |
|---|---|---|
| 1 | Math | ~2,8% (cao nhất) |
| 2 | History | ~2,5% |
| 3 | Geography | ~2,3% |
| 4 | Arabic | ~2,2% |
| 5 | Science | ~2,2% |
| 6 | English | ~2,0% (thấp nhất) |

**Kết luận:** Thứ tự khớp hoàn toàn với kết quả tính từ Assignment 2. Measure tự động phân rã đúng theo ngữ cảnh lọc khi đặt vào trục phân loại (không cần slicer riêng).
**Minh chứng:** `filter_context_test.png`.

### 3.4 Manual spot check
**Phép tính đối chiếu thủ công (Python/pandas):**
```python
import pandas as pd
df = pd.read_csv('performance_cleaned.csv')
geo = df[df['Subject']=='Geography']
print(len(geo), round(geo['Exam_Score'].mean(), 2))
# Kết quả: 4792  76.58
```
So với Power BI (slicer chọn "Geography"): Total Exams = 4.792, Average Exam Score ≈ 76,58 — **khớp tuyệt đối**.

## 4. Ghi chú diễn giải KPI (tránh sai lầm nhân quả)

- `Risk Rate` và `Pass Rate` chỉ mô tả **tình trạng hiện tại**, không hàm ý nguyên nhân. Ví dụ: Math có Risk Rate cao nhất không đồng nghĩa "môn Math khó hơn" — cần thêm dữ liệu định tính để kết luận.
- `Homework_Completion_%` **không** được đưa vào bất kỳ measure nào ngụ ý liên hệ với điểm thi, vì Assignment 2 đã xác nhận không có tương quan thống kê (r=0,0025, p=0,666) giữa hai biến này trên toàn bộ 28.736 dòng dữ liệu.
