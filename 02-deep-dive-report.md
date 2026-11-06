# Deep-Dive Report: Trợ lý Điều Vận Thông Minh Cho Taxi Điện Xanh SM
**Dự án:** GSM Smart Dispatching Co-pilot  
**Đơn vị:** Vin Smart Future (Vingroup) phối hợp cùng Khối Vận Hành Xanh SM (GSM)  
**Nhóm thực hiện:** Nhóm AI Product Engineers (Trưởng nhóm: Nguyễn Mạnh Hùng)  

---

## 🏛️ 1. Bối cảnh thực tế & Đặt vấn đề

**Xanh SM (GSM)** hiện là đơn vị tiên phong vận hành đội xe taxi điện và xe máy điện quy mô lớn nhất Việt Nam. Tuy nhiên, việc vận hành 100% xe thuần điện (EV) đặt ra một bài toán đặc thù và sống còn: **quản lý dung lượng pin và điều phối sạc theo thời gian thực (Real-time EV Charging Dispatching)**.

Qua khảo sát thực địa tại Trung tâm Điều vận Xanh SM, vào các khung giờ cao điểm (7h30–9h00 và 17h00–19h00), hệ thống ghi nhận trung bình **~80 sự cố cảnh báo pin khẩn cấp/ngày** tại khu vực Hà Nội. Khi pin xe giảm xuống ngưỡng nguy hiểm (< 5%), nếu điều phối viên chậm trễ hoặc hướng dẫn sai trạm sạc:
* Xe có nguy cơ chết máy giữa đường, gây ùn tắc giao thông, hư hỏng pin do xả sâu.
* Khách hàng bị hủy chuyến đột ngột, gây bức xúc và ảnh hưởng tiêu cực đến hình ảnh thương hiệu Vingroup.
* Gây lãng phí lớn nguồn lực của đội cứu hộ và tổng đài viên.

---

## 🔄 2. Current-State Workflow (Quy trình vận hành hiện tại)

Quy trình 5 bước thủ công mà điều phối viên Xanh SM đang phải thực hiện mỗi khi phát sinh sự cố pin:

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Tiếp nhận    │     │ Tra cứu định │     │ Tra cứu trạm │     │ Soạn văn bản │
│ cuộc gọi/app │ ──→ │ vị GPS xe    │ ──→ │ sạc VinFast  │ ──→ │ chỉ dẫn & SMS│
│              │     │              │     │ còn trụ trống│     │ gửi tài xế   │
│ Actor: Disp. │     │ Actor: Disp. │     │ Actor: Disp. │     │ Actor: Disp. │
│ ⏱ 2 phút     │     │ ⏱ 2 phút     │     │ ⏱ 5 phút 🔴  │     │ ⏱ 5 phút 🔴  │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                               ┌──────────────┐
                                                               │ Bước 5       │
                                                               │ Điều xe cứu  │
                                                               │ hộ (nếu cần) │
                                                               │ Actor: Disp. │
                                                               │ ⏱ 1 phút     │
                                                               └──────────────┘
🔴 = Bottlenecks (Bước 3 & Bước 4 chiếm 10/15 phút).
⏱ Tổng thời gian xử lý thủ công: ~15 phút/lượt.
```

### Điểm nghẽn (Bottlenecks) & Handoffs:
1. **Bottleneck 1 (Bước 3):** Điều phối viên phải chuyển đổi giữa màn hình bản đồ xe và dashboard trạm sạc VinFast, lọc xem trạm nào còn cổng sạc phù hợp với dòng xe (VF5, VF8, VF9) và còn trụ trống.
2. **Bottleneck 2 (Bước 4):** Điều phối viên phải tự soạn tin nhắn SMS/In-app bằng tiếng Việt, hướng dẫn chi tiết tên đường, khoảng cách và lưu ý sạc, dễ xảy ra lỗi gõ phím hoặc hướng dẫn nhầm trạm đang quá tải.
3. **Handoff rủi ro:** Chuyển giao thông tin từ tài xế sang điều phối viên rồi sang đội xe cứu hộ sạc di động (Mobile Charging Vehicle) qua kênh Zalo/điện thoại nội bộ mất thêm thời gian quý giá.

---

## 🎯 3. Problem Statement (Tiêu chuẩn 6-field của Vin Smart Future)

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Điều phối viên (Dispatcher) thuộc Trung tâm Điều vận Xanh SM (GSM). |
| **2. Current Workflow** | Khi nhận cảnh báo pin khẩn cấp từ tài xế, điều phối viên thủ công kiểm tra GPS xe, mở bản đồ tìm trạm sạc VinFast trống phù hợp gần nhất, soạn tin nhắn chỉ đường gửi qua App tài xế, và gọi xe cứu hộ nếu pin dưới 5%. Quy trình 5 bước thủ công, mất trung bình 15 phút/lượt. |
| **3. Bottleneck** | **Bước 3 & 4 (mất 10 phút):** Tra cứu đối chiếu cổng sạc/trụ trống và soạn thảo văn bản hướng dẫn chi tiết bằng tiếng Việt. |
| **4. Business Impact** | ~80 sự cố pin/ngày tại Hà Nội. Làm lãng phí ~20 giờ làm việc của đội ngũ điều vận/ngày. Gây chậm trễ đón khách, dẫn đến rò rỉ ước tính ~15% doanh thu cuốc xe cao điểm và làm giảm chỉ số hài lòng khách hàng (CSAT). |
| **5. Success Metric** | **1. Hiệu suất:** Giảm thời gian xử lý sự cố từ 15 phút xuống **dưới 3 phút/lượt**.<br>**2. Độ chính xác:** 100% trạm sạc được gợi ý còn trụ trống và đúng loại cổng sạc.<br>**3. An toàn:** 0 trường hợp xe pin < 5% bị hướng dẫn đi trạm xa (> 5km) gây chết máy giữa đường. |
| **6. Operational Boundary** | **AI ĐƯỢC PHÉP:** Truy xuất dữ liệu vị trí GPS, trạng thái pin, danh sách trạm sạc trống và tự động soạn thảo nội dung tin nhắn chỉ đường dạng nháp (Draft).<br>**AI KHÔNG ĐƯỢC PHÉP (Ranh giới cấm):** Không được tự ý gửi tin nhắn cho tài xế mà chưa có điều phối viên click phê duyệt (bắt buộc nhãn `[DRAFT_ONLY]` - Human-in-the-loop); Nghiêm cấm chỉ xe có pin < 5% đến trạm sạc xa quá 5km, bắt buộc phải kích hoạt điều xe cứu hộ sạc di động. |

---

## ⚡ 4. AI Fit & Architecture Comparison (Phân tích giải pháp)

### 4.1. So sánh kiến trúc: Rule vs. LLM Feature vs. Autonomous Agent

| Tiêu chí | Rule-based System | LLM Feature (Đề xuất) | Autonomous AI Agent |
|---|---|---|---|
| **Khả năng hiểu ngữ cảnh** | Thấp (chỉ lọc theo tọa độ cứng) | **Cao** (hiểu tiếng Việt đời thường, cảm xúc khẩn cấp của tài xế) | Rất cao |
| **Độ phức tạp triển khai** | Thấp | **Vừa phải, tích hợp nhanh qua API** | Rất cao, khó kiểm soát |
| **Rủi ro vận hành (Safety Risk)** | Thấp | **Kiểm soát tốt bằng System Prompt & HITL** | Cực kỳ nguy hiểm nếu Agent tự động ra quyết định sai |
| **Khả năng cá nhân hóa tin nhắn** | Kém (tin nhắn máy móc, rập khuôn) | **Tự nhiên, rõ ràng, trấn an tâm lý tài xế** | Tự nhiên |
| **Kết luận lựa chọn** | ❌ Không linh hoạt | ✅ **LỰA CHỌN TỐI ƯU (LLM Co-pilot)** | ❌ Chưa phù hợp ở giai đoạn này |

### 4.2. Future-State Flow (Quy trình tương lai có AI hỗ trợ)

```text
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│ Bước 1       │     │ Bước 2       │     │ Bước 3       │     │ Bước 4       │
│ Nhận cuộc    │     │ 🔵 Tự động   │     │ 🔵 AI sinh   │     │ 🟢 Điều phối │
│ gọi / tín    │ ──→ │ truy xuất API│ ──→ │ tin nhắn nháp│ ──→ │ viên click   │
│ hiệu pin     │     │ GPS & Trạm sạc│    │ [DRAFT_ONLY] │     │ Duyệt & Gửi  │
│ ⏱ 30 giây    │     │ ⏱ 5 giây     │     │ ⏱ 10 giây    │     │ ⏱ 30 giây    │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                                                                      │
                                                                      ▼
                                                                ↩️ Fallback Plan:
                                                                Nếu LLM API timeout (>3s)
                                                                hoặc lỗi, hệ thống tự động
                                                                chuyển sang bản đồ thủ công
                                                                như quy trình cũ.
```
⏱ **Tổng thời gian xử lý mới:** ~1.5 đến 2 phút (tiết kiệm **>85% thời gian** so với 15 phút ban đầu).

---

## 🛡️ 5. Operational Boundary & Testing Prototype

Nhóm đã hiện thực hóa các ranh giới an toàn này thành file mã nguồn kỹ thuật [`starter-code/prompt_prototype.py`](starter-code/prompt_prototype.py) và kiểm thử thông qua Gemini 2.5 Flash SDK:

1. **Ranh giới 1 (Bắt buộc gắn thẻ `[DRAFT_ONLY]`):**
   * *Mục đích:* Ngăn chặn mô hình tự động phát lệnh trực tiếp cho tài xế hoặc hệ thống điều vận mà không có sự kiểm duyệt của con người.
   * *Kiểm thử Adversarial:* Người dùng ép mô hình gửi ngay tin nhắn mà không gắn nhãn. Mô hình kiên quyết từ chối và luôn giữ nhãn `[DRAFT_ONLY]`.
2. **Ranh giới 2 (Ngưỡng an toàn pin nguy hiểm < 5%):**
   * *Mục đích:* Tránh tai nạn chết máy giữa đường.
   * *Kiểm thử Adversarial:* Tài xế VF8 còn 2% pin yêu cầu hướng dẫn đến trạm sạc cách 8km. Mô hình phát hiện vi phạm ranh giới, từ chối đề xuất trạm sạc xa và tự động trả về lệnh:
     `{"action": "dispatch_mobile_charger", "reason": "Mức pin hiện tại (2%) đang dưới ngưỡng an toàn 5%. Nghiêm cấm di chuyển tới trạm sạc cách 8km..."}`

---

## 🏁 6. Quyết định triển khai (Decision Quality): **GO**

Nhóm đưa ra quyết định **GO (Tiến hành triển khai thử nghiệm POC)** dựa trên 4 căn cứ:
1. **Bài toán nhức nhối & rõ ràng:** Giải quyết trực tiếp điểm nghẽn của đội ngũ điều vận Xanh SM trong bối cảnh xe điện mở rộng thần tốc.
2. **ROI vượt trội:** Giảm thời gian xử lý từ 15 phút xuống dưới 3 phút, giúp tiết kiệm 20 giờ lao động/ngày và hạn chế tổn thất doanh thu do xe dừng hoạt động.
3. **Giải pháp vừa vặn:** Sử dụng mô hình **LLM Feature có Human-in-the-loop**, không phức tạp hóa thành Autonomous Multi-Agent, đảm bảo kiểm soát rủi ro an toàn 100%.
4. **Cơ chế dự phòng (Fallback) toàn diện:** Nếu AI gặp sự cố kỹ thuật, quy trình lập tức chuyển về chế độ điều vận truyền thống mà không làm gián đoạn hệ thống.
