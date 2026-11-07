# Lab 02 — Problem Scan & Quick Problem Cards

**Người thực hiện:** Quang Dũng · **Nhánh cá nhân:** `QuangDung` · **Ngày:** 07/11/2026.

## 1. Phạm vi và độ tin cậy

Bài làm đóng vai AI Product Engineer tại Vin Smart Future theo bối cảnh của đề. Các quy trình và con số dưới đây là **giả định phục vụ bài lab**, chưa phải số liệu nội bộ đã được xác minh. Chưa có phỏng vấn nhân viên, truy cập hệ thống hoặc đo baseline thực tế. Trước pilot cần đối chiếu giả định bằng quan sát và logs được cho phép sử dụng.

## 2. SCAN — Năm cơ hội

Sử dụng cả bốn lenses trong Phase 1: lặp lại, tốn thời gian, AI có thể tốt hơn và pain từ người khác. Rubric ghi ba lenses nhưng phần hướng dẫn liệt kê bốn; bài làm bao phủ cả bốn.

| # | Đơn vị | Lens chính | Bài toán vận hành cụ thể | Giả thuyết cần kiểm chứng |
|---|---|---|---|---|
| 1 | Xanh SM | Tốn thời gian | Điều phối viên phải nghe tài xế báo pin thấp, tra cứu vị trí, tìm phương án và tự soạn hướng dẫn. | 15 phút/lượt; phần tìm phương án và soạn tin chiếm 10 phút. |
| 2 | Vinhomes | Lặp lại | Nhân viên đọc phản ánh cư dân, gán nhóm sự cố và chuyển cho bộ phận xử lý. | 6 phút/phiếu; phản ánh nhiều chủ đề dễ chuyển nhầm. |
| 3 | Vinpearl | Pain từ người khác | Quản lý phải đọc nhiều đánh giá để phát hiện phàn nàn cần phản hồi sớm. | 30 phút/50 đánh giá; dễ bỏ sót phản ánh nghiêm trọng. |
| 4 | VinFast | Lặp lại | Nhân viên đối soát phiên sạc và hóa đơn, tìm các dòng lệch số tiền hoặc thiếu mã giao dịch. | 45 phút/100 dòng; có thể xử lý chủ yếu bằng quy tắc và khóa đối chiếu. |
| 5 | Vinpearl | AI có thể tốt hơn | Nhân viên đặt phòng đọc email đoàn dài, trích ngày ở, số phòng và câu hỏi còn thiếu. | 12 phút/email; dễ bỏ sót thay đổi lịch nằm trong chuỗi email. |

## 3. QUICK-ASSESS — Ba thẻ bài toán

### Card #1 — Xanh SM: Soạn nháp hỗ trợ sự cố pin

| Trường | Nội dung |
|---|---|
| Bài toán | Giảm thời gian điều phối viên chuẩn bị phương án và tin nhắn khi tài xế báo sự cố pin. |
| Công ty thành viên | Xanh SM. |
| Actor | Điều phối viên; tài xế là người chịu thời gian chờ. |
| Workflow hiện tại | 1. Nhận cuộc gọi (2 phút) → 2. Xác minh xe, vị trí, pin (2 phút) → 3. Tra cứu và chọn phương án (5 phút) → 4. Soạn hướng dẫn (5 phút) → 5. Xác nhận, chuyển yêu cầu cứu hộ nếu cần (1 phút). |
| Bottleneck | Bước 3–4: 10/15 phút, phải tổng hợp thông tin và diễn đạt hướng dẫn. |
| AI hỗ trợ | LLM soạn nháp tiếng Việt từ dữ liệu được xác nhận; quy tắc xác định trường hợp cần cứu hộ. Prototype chỉ dùng tình huống giả lập, chưa kết nối dữ liệu vận hành. |
| Metric có số | Mục tiêu pilot: trung vị thời gian xử lý ≤5 phút/lượt, ít nhất 95% nháp đúng phương án trên tập nhãn do điều phối viên duyệt; 100% nháp được người kiểm tra trước hành động. |
| Quick Architecture | Rule + LLM Feature, không cần Agent. |
| Ranh giới | Không gửi tin/điều xe thực; pin <5% chỉ đề xuất cứu hộ để người duyệt; không bịa trạm sạc, vị trí hoặc tình trạng trụ. |
| Dữ liệu cần có | Logs sự cố được ẩn danh, pin/vị trí có thời điểm cập nhật, danh mục phương án được vận hành chấp thuận. |

### Card #2 — Vinhomes: Gợi ý phân loại phản ánh cư dân

| Trường | Nội dung |
|---|---|
| Bài toán | Giảm việc đọc và phân tuyến thủ công phản ánh dạng văn bản. |
| Công ty thành viên | Vinhomes. |
| Actor | Nhân viên chăm sóc cư dân và đầu mối kỹ thuật tòa nhà. |
| Workflow hiện tại | 1. Nhận phiếu (1 phút) → 2. Đọc, xác định chủ đề (2 phút) → 3. Tìm đầu mối (2 phút) → 4. Chuyển phiếu, xác nhận (1 phút). |
| Bottleneck | Bước 2–3: 4/6 phút; câu chữ không thống nhất và phiếu nhiều vấn đề. |
| AI hỗ trợ | Gợi ý nhãn, trích thông tin còn thiếu, soạn mô tả ngắn; nhân viên quyết định nơi nhận. |
| Metric có số | Mục tiêu pilot: trung vị thời gian phân tuyến ≤2 phút/phiếu; macro-F1 ≥0,90 trên 100 phiếu được gán nhãn và giữ riêng để đánh giá. |
| Quick Architecture | Rule cho tình huống rõ ràng; LLM Feature cho văn bản khó phân loại; chuyển người khi không đủ dữ liệu. |
| Ranh giới | Không tự giải quyết tranh chấp, cam kết bồi thường hoặc bỏ qua cảnh báo khẩn cấp; không tự đóng phiếu. |
| Dữ liệu cần có | Danh mục nhãn, đầu mối và phiếu được ẩn danh; cần quyền truy cập và quy trình xử lý khẩn cấp. |

### Card #3 — Vinpearl: Tổng hợp phàn nàn từ đánh giá

| Trường | Nội dung |
|---|---|
| Bài toán | Hỗ trợ quản lý phát hiện và tóm tắt nhóm phàn nàn cần xem xét. |
| Công ty thành viên | Vinpearl. |
| Actor | Quản lý trải nghiệm khách hàng. |
| Workflow hiện tại | 1. Tập hợp 50 đánh giá (5 phút) → 2. Đọc, đánh dấu vấn đề (15 phút) → 3. Nhóm chủ đề (7 phút) → 4. Soạn báo cáo (3 phút). |
| Bottleneck | Bước 2–3: 22/30 phút; phàn nàn gián tiếp hoặc nhiều ngôn ngữ dễ bị bỏ sót. |
| AI hỗ trợ | Nhóm chủ đề, tạo tóm tắt kèm mã đánh giá nguồn để người quản lý kiểm tra. |
| Metric có số | Mục tiêu pilot: ≤10 phút/50 đánh giá; recall ≥0,95 với phản ánh nghiêm trọng theo nhãn quản lý; 100% nhận định có mã nguồn kiểm tra. |
| Quick Architecture | LLM Feature theo lô; Rule hỗ trợ từ khóa khẩn cấp; chưa cần Agent. |
| Ranh giới | Không đăng phản hồi công khai hoặc kết luận trách nhiệm; không suy đoán danh tính khách; không coi tóm tắt là chứng cứ độc lập. |
| Dữ liệu cần có | Tập đánh giá được phép sử dụng và ẩn danh, nhãn mức độ nghiêm trọng và bộ tiêu chí của quản lý. |

## 4. Lựa chọn và phản biện

Chọn **Card #1 — Xanh SM** để deep-dive vì bài toán có đầu ra hẹp, ranh giới kiểm tra rõ và phù hợp prototype/autograder đã cung cấp. Đây là lý do lựa chọn trong bài lab, chưa phải kết luận nhu cầu thị trường hoặc hiệu quả kinh doanh thực tế.

Card #2 cần danh mục tuyến xử lý và dữ liệu nội bộ trước khi đánh giá đúng. Card #3 có thể làm theo lô nhưng chưa có tập đánh giá gán nhãn. Card #4 nên thử đối soát bằng Rule trước vì dữ liệu có cấu trúc; việc gắn LLM có thể không tạo thêm giá trị. Card #5 có tiềm năng nhưng phải kiểm chứng dữ liệu email và cách kết nối hệ thống đặt phòng.

Phản biện chính với Card #1: nếu quy tắc và mẫu tin cố định đã đủ tốt, LLM có thể làm tăng chi phí và độ trễ. Pilot phải so sánh **thủ công / Rule + mẫu tin / Rule + LLM** trên cùng tập tình huống, cùng cách đo; chỉ giữ LLM khi cải thiện rõ thời gian hoặc chất lượng.

## 5. Tài liệu tham chiếu

- [Worksheet](../01-worksheet.md): Phase 1–2 và rubric.
- [Bài mẫu](../02-deliverable-example.md): tham khảo cấu trúc, không coi các số liệu ví dụ là dữ liệu khảo sát.
- [Báo cáo phân tích sâu](02-deep-dive-report.md): scope được chọn và kế hoạch kiểm chứng.
