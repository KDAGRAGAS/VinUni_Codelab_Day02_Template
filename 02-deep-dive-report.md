# Lab 02 — Problem Deep-Dive & Evaluation Report

**Bài toán lựa chọn:** Phân bổ vị trí lắp đặt mới & điều chuyển trụ sạc (VinFast / V-GREEN & Xanh SM)

---

## Phase 3 — DEEP-DIVE

### 3.1. Current-State Workflow Mapping (Quy trình hiện tại)

Hiện tại, để xử lý tình trạng nơi thì thừa trụ sạc bỏ trống (`<15%` công suất), nơi thì quá tải (`>85%`), Chuyên viên Quy hoạch Mạng lưới trạm sạc (V-GREEN / VinFast) phải thực hiện thủ công 5 bước sau (xem sơ đồ trực quan tại file `04-workflow-diagram.png`):

```text
┌───────────────────┐      ┌───────────────────┐      ┌───────────────────┐
│ Bước 1            │      │ Bước 2 🔴         │      │ Bước 3 🔴         │
│ Xuất báo cáo hiệu │ 🔄   │ Đọc & gom nhóm    │ 🔄   │ Đọc rà soát hồ sơ │
│ suất trạm sạc     │ ───► │ phản ánh thực địa │ ───► │ khảo sát mặt bằng │
│ trên hệ thống CMS │      │ của tài xế/chủ xe │      │ mới quanh khu vực │
│                   │      │                   │      │                   │
│ Ai: Site Planner  │      │ Ai: Site Planner  │      │ Ai: Site Planner  │
│ ⏱ 5 phút          │      │ ⏱ 15 phút         │      │ ⏱ 15 phút         │
│ In: Log IoT trụ   │      │ In: Ticket CRM/App│      │ In: File PDF/Word │
│ Out: DS trạm <15% │      │ Out: Lý do vắng / │      │ Out: Thông số điện│
│      & trạm >85%  │      │      điểm kẹt sạc │      │      & bãi đỗ xe  │
└───────────────────┘      └───────────────────┘      └─────────┬─────────┘
                                                                │
                                                                ▼
                           ┌───────────────────┐      ┌───────────────────┐
                           │ Bước 5 🔴         │      │ Bước 4            │
                           │ Viết Tờ trình đề  │ 🔄   │ Tính toán cấu hình│
                           │ xuất phân bổ/điều │ ◄─── │ phân bổ & điều    │
                           │ chuyển trụ sạc    │      │ chuyển trụ AC/DC  │
                           │                   │      │                   │
                           │ Ai: Site Planner  │      │ Ai: Site Planner  │
                           │ ⏱ 5 phút          │      │ ⏱ 5 phút          │
                           │ In: Phương án chọn│      │ In: Dữ liệu B2+B3 │
                           │ Out: Báo cáo trình│      │ Out: Số trụ AC/DC │
                           │      Quản lý duyệt│      │      khớp tải điện│
                           └───────────────────┘      └───────────────────┘

🔴 = Bottleneck (Điểm nghẽn: Bước 2, Bước 3, Bước 5 — tốn 35/45 phút)
🔄 = Handoff (4 điểm chuyển dữ liệu thủ công giữa CMS -> CRM -> File khảo sát -> Báo cáo)
⏱ Tổng thời gian xử lý hiện tại: 45 phút / 1 hồ sơ vị trí.
```

**Giải thích các điểm nghẽn (🔴 Bottleneck):**
* **Bước 2 (15 phút):** Số liệu trên CMS chỉ báo trạm A vắng khách (`<15%`) hoặc trạm B quá tải (`>85%`), nhưng không cho biết lý do. Nhân viên phải tự đọc hàng trăm phản ánh của tài xế Xanh SM và chủ xe VinFast mới biết nguyên nhân thực tế (ví dụ: trạm A đặt dưới hầm chung cư thu phí gửi xe quá đắt, bảo vệ đóng cổng sau 22h, hoặc đường vào bị rào chắn nên tài xế bỏ sang trạm B).
* **Bước 3 (15 phút):** Phải mở đọc từng file Word/PDF biên bản khảo sát mặt bằng mới do đội phát triển thị trường gửi về để kiểm tra công suất trạm biến áp 3 pha còn dư bao nhiêu kVA, lối vào có đủ rộng cho xe ra vào sạc không, chi phí thuê mặt bằng thế nào.
* **Bước 5 (5 phút):** Ngồi gõ lại toàn bộ lý do và thông số kỹ thuật thành văn bản Tờ trình đề xuất gửi cấp trên phê duyệt.

---

### 3.2. Problem Statement (6-Field)

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Chuyên viên Quy hoạch & Phát triển Mạng lưới Trạm sạc (Site Planning Specialist) tại V-GREEN / VinFast. |
| **2. Current Workflow** | Nhân viên tải file Excel hiệu suất trạm sạc từ CMS để lọc các trạm `<15%` và `>85%`, sau đó tra cứu ticket phản ánh của tài xế trên CRM, mở đọc từng file PDF/Word biên bản khảo sát mặt bằng mới để kiểm tra hạ tầng điện - bãi đỗ, tính số lượng trụ rồi viết tờ trình đề xuất lắp mới hoặc điều chuyển trụ sạc. Quy trình 5 bước làm tay tốn **45 phút/hồ sơ**. |
| **3. Bottleneck** | **Bước 2, Bước 3 và Bước 5 (mất 35/45 phút):** Phải đọc thủ công rất nhiều phản ánh văn bản tự do của tài xế và các biên bản khảo sát mặt bằng dài để tổng hợp nguyên nhân rồi viết báo cáo đề xuất. |
| **4. Business Impact** | • Mỗi tuần có khoảng 120 điểm mặt bằng & cụm trạm sạc cần rà soát $\rightarrow$ mất **90 giờ làm việc/tuần** của đội quy hoạch.<br>• Việc chậm điều chuyển làm ~18% trụ sạc đặt sai chỗ bị bỏ trống (lãng phí chi phí đầu tư thiết bị CAPEX), trong khi ở các điểm nóng quá tải, tài xế Xanh SM và chủ xe VinFast mất trung bình **35 phút chờ sạc/ca**, làm giảm ~12% doanh thu cuốc xe giờ cao điểm. |
| **5. Success Metric** | 1. **Tốc độ (Efficiency):** Giảm thời gian phân tích và soạn đề xuất cho 1 vị trí từ **45 phút xuống dưới 8 phút**.<br>2. **Chất lượng (Quality):** Giảm tỷ lệ trạm lắp mới bị vắng khách (`<15%` công suất sau 3 tháng) từ **20% xuống dưới 5%**; 100% đề xuất phân bổ trụ sạc tuân thủ đúng giới hạn công suất trạm biến áp 3 pha tại mặt bằng. |
| **6. Operational Boundary** | • **AI được phép làm:** Tổng hợp nguyên nhân từ ticket phản ánh của tài xế, trích xuất thông số từ biên bản khảo sát mặt bằng và tạo bản nháp đề xuất vị trí/số lượng trụ sạc cần lắp mới hoặc điều chuyển (gắn nhãn `[DRAFT_ONLY]`).<br>• **AI TUYỆT ĐỐI KHÔNG được làm:**<br>  1. Không được tự động phê duyệt ngân sách đầu tư hay tự phát lệnh thi công lắp đặt/di dời trụ sạc khi chưa có người duyệt (**Bắt buộc Human-in-the-loop**, mọi đầu ra chỉ ở dạng bản nháp `[DRAFT_ONLY]`).<br>  2. Không được đề xuất lắp đặt trụ sạc nhanh công suất lớn (DC 60kW/150kW) tại các mặt bằng mà công suất dư của trạm biến áp hạ thế không đủ tải hoặc không đạt điều kiện an toàn PCCC. |

---

### 3.3. Future-State Flow & AI Fit

#### Chọn mức AI Fit: `[x] LLM Feature` (kết hợp Rule-based)
* **Vì sao không chỉ dùng Rule-based / SQL?** SQL dễ dàng lọc ra danh sách trạm `<15%` hoặc `>85%` công suất, nhưng không thể đọc hiểu nội dung chữ viết tự do trong hàng nghìn phản ánh của tài xế (để biết *tại sao* họ né trạm sạc đó) và không tóm tắt được các file biên bản khảo sát mặt bằng.
* **Vì sao không dùng Agent tự trị hoàn toàn?** Quyết định di dời hay lắp đặt trụ sạc tốn chi phí đầu tư lớn (hàng trăm triệu đồng/trạm) và liên quan trực tiếp đến an toàn lưới điện 3 pha, bắt buộc phải có kỹ sư quy hoạch kiểm tra bước cuối.
* **Cách kết hợp hợp lý nhất:** Dùng **Rule-based** để lọc số liệu tải trạm sạc (`<15%`, `>85%`) và kiểm tra ngưỡng công suất điện, kết hợp **LLM Feature (Gemini 2.5 Flash)** để đọc văn bản phản ánh + hồ sơ mặt bằng và sinh bản nháp Tờ trình đề xuất.

#### Sơ đồ Quy trình Tương lai (Future-State Flow):

```text
┌────────────────────┐     ┌────────────────────┐     ┌────────────────────┐     ┌────────────────────┐
│ Bước 1 (Rule-based)│     │ Bước 2 🔵 AI Step  │     │ Bước 3 🔵 AI Step  │     │ Bước 4 🟢 HITL     │
│ Tự động lọc trạm   │     │ LLM đọc ticket tài │     │ LLM đối chiếu công │     │ Chuyên viên Quy    │
│ <15% & >85% từ CMS │ ──► │ xế & trích xuất hồ │ ──► │ suất điện 3 pha &  │ ──► │ hoạch xem lại bản  │
│ + gom ticket & hồ  │     │ sơ mặt bằng để tóm │     │ soạn bản nháp đề   │     │ nháp, chỉnh sửa và │
│ sơ mặt bằng quanh  │     │ tắt nguyên nhân &  │     │ xuất cấu hình trụ  │     │ bấm Duyệt trình    │
│ bán kính 5km       │     │ thông số hạ tầng   │     │ sạc [DRAFT_ONLY]   │     │ Hội đồng Đầu tư    │
└────────────────────┘     └────────────────────┘     └─────────┬──────────┘     └────────────────────┘
                                                                │
                                                                ▼
                                                      ┌────────────────────┐
                                                      │ ↩️ Fallback        │
                                                      │ Nếu hồ sơ mặt bằng │
                                                      │ thiếu thông số trạm│
                                                      │ biến áp hoặc AI lỗi│
                                                      │ -> Chuyển về luồng │
                                                      │ thẩm định tay 100% │
                                                      └────────────────────┘
```

---

## Phase 5 — EVALUATE (Đánh giá & Quyết định)

### AI Readiness Checklist:
1. [x] **Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test?** (Đã có log hiệu suất trụ sạc trên CMS, lịch sử ticket phản ánh của tài xế trên CRM và các file biên bản khảo sát mặt bằng mẫu).
2. [x] **Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)?** (Mọi đề xuất của AI đều chỉ là bản nháp `[DRAFT_ONLY]`, bắt buộc Chuyên viên Quy hoạch kiểm tra thông số điện và phê duyệt trước khi trình đầu tư; nếu hồ sơ thiếu thông số kỹ thuật sẽ tự động chuyển về xử lý thủ công qua Fallback).
3. [x] **Stakeholders sẵn sàng thay đổi quy trình làm việc cũ?** (Nhân viên quy hoạch giảm được 35 phút đọc ghép tài liệu thủ công trên mỗi hồ sơ vị trí nên rất dễ áp dụng vào công việc hằng tuần).

### Quyết định cuối cùng:
- [x] **GO (Bắt đầu xây dựng Prototype với scope hẹp)**
- [ ] **NOT YET**
- [ ] **NO-GO**

**Lý giải quyết định (Justification):**
* **Kỹ thuật khả thi và đúng trọng tâm:** Giải pháp tách bạch rõ việc lọc số liệu công suất (do SQL/Rule đảm nhiệm) và việc đọc tổng hợp văn bản phản ánh của tài xế + biên bản khảo sát mặt bằng (do LLM đảm nhiệm). Cơ chế Human-in-the-loop đảm bảo không xảy ra rủi ro duyệt nhầm mặt bằng không đủ tải điện.
* **Chi phí thấp, hiệu quả kinh tế lớn:** Chi phí gọi API Gemini 2.5 Flash cho ~120 hồ sơ/tuần chỉ tốn vài chục nghìn đồng/tháng, nhưng giúp tiết kiệm khoảng 90 giờ làm việc/tuần cho đội ngũ kỹ sư quy hoạch và tránh lãng phí hàng trăm triệu đồng vốn đầu tư (CAPEX) do đặt nhầm trạm sạc ở những vị trí bị hạn chế ra vào. Có thể triển khai thử nghiệm (Pilot) ngay cho các cụm trạm sạc tại Hà Nội và TP.HCM.
