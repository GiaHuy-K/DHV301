## 2. YÊU CẦU CLAUDE PHÂN TÍCH DỮ LIỆU
---
**Dữ liệu này thể hiện điều gì**

Đây là dữ liệu bán hàng năm 2025 của chuỗi điện máy ABC — cấu trúc tidy chuẩn với 192 dòng, mỗi dòng là một tổ hợp duy nhất Tháng × Chi nhánh × Nhóm hàng. Hệ thống có 4 chi nhánh (TP.HCM, Hà Nội, Đà Nẵng, Cần Thơ) và 4 nhóm sản phẩm (Điện thoại, Laptop, Đồng hồ, Phụ kiện). Tổng doanh thu cả năm đạt **76,783 trđ** với 11,480 đơn hàng và 3,654 khách hàng mới.

---

**Xu hướng chính**

Doanh thu theo tháng vẽ ra một **đường chữ U nghiêng**: cao tháng 1 (7,391 trđ) do sức mua trước Tết, sau đó lao dốc mạnh đến đáy tháng 4 (5,129 trđ), rồi phục hồi đều đến tháng 10, và bùng nổ ở tháng 11–12 (lần lượt 7,780 và 8,402 trđ). Q4 là quý mạnh nhất, vượt Q2 tới **41.7%** — khoảng cách giữa quý cao điểm và thấp điểm rất lớn, đặt ra yêu cầu lập kế hoạch tồn kho nghiêm túc.

Về cơ cấu sản phẩm, Điện thoại dẫn đầu (39% doanh thu) nhưng Laptop mới là sản phẩm sinh lợi nhất tính theo đơn — **21.97 trđ/đơn** so với chỉ 1.51 trđ/đơn của Phụ kiện. Laptop cũng có tỷ lệ chi phí QC thấp nhất (~6%), nghĩa là đây là nhóm hàng hiệu quả nhất về mặt marketing.

---

**Điểm bất thường**

Ba điểm cần điều tra kỹ hơn: tháng 11 TP.HCM Điện thoại đạt 104 đơn (cao nhất cả năm, vượt bình quân ~40%) — khả năng cao là chiến dịch 11.11; tháng 9 TP.HCM Phụ kiện ghi nhận 72 khách hàng mới trên chỉ 195 đơn, tỷ lệ 37% bất thường gấp đôi mức trung bình, đây có thể là hiệu ứng của một chương trình referral hoặc sự kiện offline; và tháng 5 Cần Thơ Phụ kiện chỉ đạt 78 trđ — thấp nhất tuyệt đối toàn bộ dataset, cần kiểm tra xem có vấn đề vận hành hay không.

---

**Giá trị bị thiếu**

Không có missing value trong dữ liệu — đây là điểm tốt. Tuy nhiên file có **floating-point noise trong raw Excel** (ví dụ 280.60000000000002 thay vì 280.6) — dấu hiệu dữ liệu được tạo ra từ phép tính công thức thay vì nhập tay. Cần chuẩn hóa khi export sang BI tools.

---

**Lỗi có thể xảy ra**

Header của file gồm 2 dòng mô tả trước khi đến tiêu đề cột, điều này sẽ làm hỏng hầu hết các pipeline import tự động nếu không xử lý riêng. Không có cột ID đơn hàng duy nhất — không thể join với bảng chi tiết nếu cần phân tích sâu hơn sau này. Quan trọng hơn: file hoàn toàn không có dữ liệu về **giá vốn, lợi nhuận gộp, hay chi phí ngoài QC** — mọi so sánh hiệu quả hiện tại chỉ là doanh thu, không phải profitability thực sự.

---

**Insight tổng quan**

TP.HCM đang gánh 36.2% toàn hệ thống — đây vừa là sức mạnh vừa là rủi ro tập trung. Cần Thơ tuy nhỏ nhất nhưng tăng trưởng đều và bền, từ 713 trđ tháng 4 lên 1,226 trđ tháng 12, xứng đáng được đầu tư thêm. Phụ kiện có chi phí QC cao nhất (10% doanh thu) nhưng cũng là kênh tạo ra nhiều đơn nhất — cần đánh giá lại xem chiến dịch đang kéo khách mua Phụ kiện hay dùng Phụ kiện như điểm đến upsell Điện thoại và Laptop. Hai nút ở cuối dashboard có thể dùng để phân tích tiếp.



# 3: TỰ ĐỘNG TẠO CÔNG THỨC EXCEL

📋 Cấu trúc file
File gồm 3 sheet:

Dữ liệu — dữ liệu gốc 192 dòng
Công thức Excel — 28 công thức có giải thích
Bảng tổng hợp — kết quả tính tự động


🔢 28 Công thức theo 6 nhóm
1️⃣ Tốc độ tăng trưởng (4 CT)
#Công thứcMục đích1SUMIF / SUMIF - 1Tăng trưởng DT tháng 2 vs tháng 12SUMIF đơn hàngTăng trưởng số đơn3SUMIFS Q4 vs Q1Tăng trưởng theo quý & chi nhánh4(T12 - T1) / T1Tăng trưởng cả năm
2️⃣ Biên lợi nhuận (4 CT)
(Doanh thu - Chi phí QC) / Doanh thu — tính cho toàn hệ thống, từng nhóm hàng, từng chi nhánh
3️⃣ Dự báo (4 CT)
Công thứcGiải thíchFORECAST(13, DT, Tháng)Dự báo tháng 13 bằng hồi quy tuyến tínhAVERAGEIF × FORECAST / AVERAGEPhân bổ dự báo theo tỷ trọng chi nhánhAVERAGEIF tháng >= 10Trung bình động 3 tháng cuốiPOWER(T12/T1, 1/11) - 1CAGR bình quân hàng tháng
4️⃣ Giá trị trung bình (5 CT)
AVERAGE, AVERAGEIF (1 điều kiện), AVERAGEIFS (2 điều kiện), theo quý, theo tháng
5️⃣ Tra cứu dữ liệu (5 CT)
SUMPRODUCT, SUMIFS (3 điều kiện), COUNTIFS, MATCH+MAX, INDEX+MATCH
6️⃣ Logic điều kiện (6 CT)
IFS, IF lồng nhau, AND, OR, RANK, TEXT & IF kết hợp tạo dashboard text

# 4


---

**Kết luận tổng thể: Dữ liệu đạt 82/100 — tốt, có thể dùng ngay cho phân tích.**

Cấu trúc rất sạch: đúng 192 dòng, không trùng lặp, không thiếu giá trị, ánh xạ Quý/Tháng chính xác tuyệt đối. Nhưng có một số điểm cần chú ý trước khi đưa vào báo cáo chính thức.

**3 vấn đề cần xử lý ưu tiên:**

Thứ nhất, 4 dòng có z-score > 3σ tập trung ở TP.HCM tháng 9–12. Đặc biệt tháng 11 Điện thoại TP.HCM đạt 1.197,6 trđ doanh thu — cao nhất toàn dataset. Đây nhiều khả năng là cao điểm mùa vụ (11/11, Black Friday) nhưng cần xác nhận với bộ phận kinh doanh và ghi chú lại để tránh làm lệch mô hình dự báo.

Thứ hai, nhóm Phụ kiện hoạt động theo logic khác hoàn toàn (giá thấp, số lượng cao) khiến 14/48 dòng bị IQR đánh dấu là "outlier" — nhưng thực ra là đặc tính ngành. Khi phân tích tổng thể nên lọc riêng nhóm này để tránh kéo lệch phân phối.

Thứ ba, dataset thiếu `row_id` và `timestamp` — không phải lỗi ảnh hưởng phân tích hiện tại, nhưng sẽ gây khó khăn khi kết nối với hệ thống ERP hoặc cập nhật dữ liệu theo chu kỳ.


# 5 INSIGHT: 
Luận điểm cốt lõi: Doanh nghiệp này không có vấn đề về nhu cầu — thị trường đang tăng trưởng thực sự. Vấn đề là phân bổ nguồn lực sai: đang đổ tiền vào kênh chi phí cao nhất (Phụ kiện), bỏ qua kênh hiệu quả nhất (Laptop), và không có chiến lược để giữ doanh thu qua Q2.
Ba ưu tiên nếu chỉ được chọn 3:
Ngay lập tức, chuyển ngân sách QC sang Laptop — đây là nơi mỗi đồng marketing sinh ra nhiều doanh thu nhất với ít ma sát nhất. Trước Q2/2026, thiết kế chương trình kích cầu cho tháng 4-6 — khoảng gap 6,739 trđ mỗi năm đang là tiền bỏ phí không cần mở thêm bất cứ điều gì. Song song, bổ sung dữ liệu lợi nhuận gộp vào hệ thống — vì tất cả những gì ta đang phân tích chỉ là doanh thu, và có thể toàn bộ ranking đang bị lộn ngược nếu tính đúng margin.
Rủi ro không thể bỏ qua: TP.HCM là single point of failure. Một sự cố nhỏ ở đó — dù chỉ là vấn đề nhân sự hay cạnh tranh giá cục bộ — sẽ kéo toàn hệ thống xuống ngay lập tức. Đây là thứ cần được giảm từ 36% xuống dưới 30% trong vòng 24 tháng.