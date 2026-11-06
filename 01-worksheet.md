# Lab 02 — Worksheet: AI Product Scoping (Vin Smart Future)

---

## 🏛️ 1. Bối cảnh thực tế: Vin Smart Future (Vingroup)

**Vingroup** — Tập đoàn tư nhân lớn nhất Việt Nam — vừa sáp nhập toàn bộ các phòng ban công nghệ thuộc các công ty thành viên thành một đơn vị công nghệ thống nhất mang tên **Vin Smart Future**. 

Nhiệm vụ của **Vin Smart Future** là xây dựng các giải pháp AI, số hóa, và tự động hóa cốt lõi để nâng cao hiệu suất vận hành và trải nghiệm khách hàng xuyên suốt các công ty thành viên:
* 🚗 **VinFast:** Hệ thống xe điện thông minh (EV), trợ lý AI ảo trong xe, dự đoán bảo trì pin, và quản lý chuỗi cung ứng sản xuất.
* 🚕 **Xanh SM (GSM):** Vận hành đội xe taxi/xe máy điện thông minh, điều vận thông minh (Smart Dispatching), tối ưu hóa lộ trình di chuyển.
* 🏢 **Vinhomes:** Quản lý đô thị thông minh (Smart Cities), trợ lý cư dân thông minh, tối ưu hóa mức tiêu thụ năng lượng.
* 🏥 **Vinmec:** Y tế thông minh, chẩn đoán hình ảnh bằng AI, tối ưu hóa quản lý hồ sơ bệnh án.
* 🎢 **Vinpearl / VinWonders:** Trải nghiệm du lịch số hóa, quản lý phòng và luồng khách thông minh tại các khu vui chơi.

Trong buổi Lab hôm nay, nhóm của bạn sẽ đóng vai trò là **AI Product Engineer** tại **Vin Smart Future**, tiến hành tìm kiếm, scoping, phân tích độ khả thi, thiết lập ranh giới vận hành, và xây dựng một **bản mẫu kỹ thuật (prompt prototype)** cho một bài toán cụ thể thuộc một trong những mảng kinh doanh trên.

---

## 📊 2. Cơ cấu tính điểm bài lab

### 👥 Điểm nhóm (60 điểm)

| Gate | Điểm | Deliverable | Tiêu chí chấm |
|---|---:|---|---|
| **G1. Workflow Mapping** | 20 | Problem Deep-Dive | Vẽ chi tiết quy trình hiện tại: các bước, handoff, thời gian, bottleneck |
| **G2. Problem Statement** | 20 | Problem Deep-Dive | Problem Statement 6-field bám sát thực tế, metric có số và ranh giới rõ ràng |
| **G3. AI Fit & Future Flow** | 10 | Problem Deep-Dive | So sánh Rule vs LLM vs Agent, future flow có bước AI, ranh giới và Fallback |
| **G4. Decision Quality** | 10 | Problem Deep-Dive | Quyết định Go/Not Yet/No-Go trung thực và có chứng cứ rõ ràng |

### 👤 Điểm cá nhân (40 điểm)

| Gate | Điểm | Deliverable | Tiêu chí chấm |
|---|---:|---|---|
| **I1. Scan & Cards** | 15 | Quick Cards | Liệt kê 5 problems sử dụng 3 lenses, hoàn thiện 3 quick cards chất lượng |
| **I2. Prototyping** | 10 | 02-lab/ | Chạy thử nghiệm programmatic prompt prototype thành công |
| **I3. AI Log & Reflection** | 15 | 03-ai-log.md | Phản ánh trung thực về việc dùng AI làm thought-partner (giúp gì, sai gì, sửa gì) |

---

# 🚀 Phase 0 — worked Example: Xanh SM Intelligent Dispatcher (15 min)

*Giảng viên walk-through ví dụ thực tế từ Vin Smart Future để bạn hiểu rõ cách scoping một bài toán AI.*
Đọc chi tiết worked example tại file [02-deliverable-example.md](02-deliverable-example.md).

---

# 🔍 Phase 1 — SCAN (Cá nhân, 20 min)

Hãy sử dụng **4 Lenses** dưới đây để quét qua hoạt động vận hành của các công ty thành viên Vingroup. Ghi lại **ít nhất 5 bài toán/bottleneck** thực tế.

### 4 Lenses tìm bài toán AI cho Vingroup:
1. **Lặp lại (Repetitive):** Tác vụ lặp đi lặp lại nhiều lần hằng ngày. (Ví dụ: So khớp hóa đơn sạc điện tại VinFast, route lại chuyến taxi tại Xanh SM).
2. **Tốn thời gian (Time-consuming):** Tác vụ ngốn thời gian xử lý thủ công của nhân viên. (Ví dụ: Soạn thảo phản hồi đánh giá 1-star của cư dân Vinhomes).
3. **AI có thể tốt hơn (AI-upgrade):** Dịch vụ khách hàng hiện tại còn chậm hoặc phản hồi rập khuôn. (Ví dụ: Chatbot CSKH Vinpearl hỗ trợ đặt vé vui chơi).
4. **Pain từ người khác (Stakeholder Pain):** Bottleneck khiến khách hàng hoặc nhân viên thực địa phàn nàn. (Ví dụ: Tài xế Xanh SM phàn nàn về việc hệ thống gợi ý điểm đón khách không chính xác).

> [!TIP]
> **🤖 AI Prompts — Partner brainstorm:**
> Hãy sử dụng prompt sau để brainstorm các bài toán thực tế nếu bạn chưa có ý tưởng:
> *"Tôi là AI Engineer tại Vin Smart Future (Vingroup). Tôi đang tìm kiếm các pain point vận hành cụ thể có thể tối ưu bằng AI cho mảng [Chọn một: VinFast / Xanh SM / Vinhomes / Vinmec]. Hãy gợi ý cho tôi 5 quy trình nghiệp vụ thủ công, tốn nhiều thời gian và gây rò rỉ hiệu suất kèm con số thống kê ước tính về tổn thất."*

### 📝 List bài toán của tôi:
| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | **Xanh SM (GSM)** | Tốn thời gian / Pain từ người khác | Hỗ trợ điều phối viên xử lý khẩn cấp sự cố sạc pin / xe sắp hết pin giữa đường (12-15 min/lượt). |
| 2 | **VinFast** | AI có thể tốt hơn | Chẩn đoán ban đầu lỗi kỹ thuật xe qua mô tả tiếng Việt tự nhiên của khách hàng. |
| 3 | **Vinhomes** | Lặp lại / Tốn thời gian | Phân loại và điều hướng phản ánh sự cố cư dân gửi qua App Vinhomes Resident (4-12 giờ). |
| 4 | **Vinmec** | Tốn thời gian | Tự động hóa soạn thảo tóm tắt hồ sơ bệnh án xuất viện (Discharge Summary) (20-30 min/bệnh nhân). |
| 5 | **Vinpearl** | Pain từ người khác | Quét review trên Booking/Agoda/Google Maps để cảnh báo các phàn nàn dịch vụ nghiêm trọng. |

---

# 🃏 Phase 2 — QUICK-ASSESS (Cá nhân, 30 min)

Chọn **top 3 bài toán** từ danh sách trên và hoàn thiện **3 Quick Problem Cards** (xem chi tiết tại [`01-problem-scan.md`](01-problem-scan.md)).

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1 (Lựa chọn Deep-Dive)                  │
│                                                             │
│ Bài toán (1 câu): Hỗ trợ điều phối viên xử lý khẩn cấp sự   │
│ cố hết pin và điều phối xe sạc di động cho tài xế Xanh SM. │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Tài xế Xanh SM & Điều phối viên (Disp.) │
│                                                             │
│ Workflow thủ công hiện tại (5 bước):                        │
│   1. Nhận báo ──> 2. Check GPS ──> 3. Tìm trạm ──> 4. SMS   │
│   ──> 5. Điều xe cứu hộ nếu pin < 5%                       │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (⏱ 10 phút/lượt)│
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3, 4, 5          │
│                                                             │
│ Đo thành công bằng gì (Metric có số)?                        │
│   - Giảm thời gian xử lý sự cố từ 15 min ──> under 3 min.   │
│   - 100% ca pin < 5% không bị điều đi trạm xa (> 5km).      │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

---

# 🏗️ Phase 3 — DEEP-DIVE (Nhóm, 85 min)

> 📑 **Chi tiết toàn bộ báo cáo chuyên sâu được lưu tại:** [`02-deep-dive-report.md`](02-deep-dive-report.md)  
> 📊 **Sơ đồ quy trình As-Is vs To-Be:** [`04-workflow-diagram.pdf`](04-workflow-diagram.pdf)

## 3.1. Current-State Workflow Mapping (25 min)
* 🔴 **Bottleneck:** Bước 3 (Tìm trạm sạc trống phù hợp) & Bước 4 (Soạn tin nhắn SMS/In-app hướng dẫn tài xế).
* 🔄 **Handoff:** Chuyển giao thông tin từ tài xế ──> điều phối viên ──> đội xe cứu hộ sạc pin di động.
* **Tổng thời gian vận hành trung bình:** **15 phút/lượt**.

## 3.2. Problem Statement (6-field) & Metrics (15 min)

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Điều phối viên (Dispatcher) thuộc Trung tâm Điều vận Xanh SM (GSM). |
| **2. Current Workflow** | Khi tài xế báo pin yếu, điều phối viên tra GPS xe, mở bản đồ tìm trụ sạc VinFast trống phù hợp, soạn tin SMS chỉ đường, gọi cứu hộ nếu pin < 5% (5 bước thủ công, mất 15 min/lượt). |
| **3. Bottleneck** | Bước 3 & Bước 4: Mất 10 phút tra cứu đối chiếu trụ sạc trống và soạn tin nhắn hướng dẫn bằng tiếng Việt. |
| **4. Business Impact** | ~80 sự cố pin/ngày tại Hà Nội; lãng phí ~20 giờ làm việc/ngày của điều phối viên; rò rỉ ~15% doanh thu đón khách giờ cao điểm. |
| **5. Success Metric** | 1. Giảm thời gian xử lý từ 15 phút xuống dưới 3 phút/lượt.<br>2. 0 trường hợp xe pin < 5% bị hướng dẫn đi trạm xa (> 5km) gây chết máy giữa đường. |
| **6. Operational Boundary** | AI được phép truy xuất GPS, trạm sạc trống và soạn draft tin nhắn. **CẤM:** Tuyệt đối không tự ý gửi tin nếu thiếu nhãn `[DRAFT_ONLY]` (bắt buộc Human-in-the-loop); nếu pin < 5% cấm chỉ trạm xa, bắt buộc kích hoạt `dispatch_mobile_charger`. |

## 3.3. Future-State Flow & AI Fit (25 min)
* **Xác định mức AI Fit (AI-Fit Matrix):** **[x] LLM Feature (Co-pilot)**.
* **Thời gian xử lý mới:** ~1.5 đến 2 phút/lượt (tiết kiệm >85% thời gian).
* **Cơ chế Fallback:** Nếu LLM API gặp sự cố timeout (> 3s), hệ thống tự động fallback về màn hình thao tác thủ công truyền thống.

---

# 💻 Phase 4 — Prompt Prototype & Testing (Cá nhân, 30 min)

* Mã nguồn nguyên mẫu kỹ thuật: [`starter-code/prompt_prototype.py`](starter-code/prompt_prototype.py)
* Đã cấu hình và kiểm thử thành công bằng Gemini 2.5 Flash SDK.
* Vượt qua toàn bộ các Adversarial Test Cases (Bảo vệ thẻ `[DRAFT_ONLY]` và ngưỡng pin `< 5%`).
* Điểm tự động Autograder: **10.00 / 10.00 điểm (100% PASS)**.

---

# 📝 Phase 5 — Reflection & AI Log (Cá nhân, 15 min)

* Chi tiết nhật ký tương tác và bài học rút ra được lưu tại: [`03-ai-log.md`](03-ai-log.md)
  * 🔵 **AI Step:** Tác vụ LLM xử lý.
  * 🟢 **Human Step (HITL):** Bước con người phê duyệt/review (Human-in-the-loop).
  * ↩️ **Fallback:** Kế hoạch dự phòng khi LLM trả về kết quả lỗi hoặc không tự tin.

---

# 💻 Phase 4 — TECHNICAL PROMPT PROTOTYPE (Nhóm, 30 min)

Để đảm bảo kỹ sư của Vin Smart Future luôn giữ vững năng lực lập trình, nhóm của bạn sẽ tiến hành **lập trình bản mẫu prompt** trực tiếp trên **Gemini 2.5 Flash** bằng Python để stress-test hệ thống.

### Hướng dẫn thực hiện:
1. Mở file [starter-code/prompt_prototype.py](starter-code/prompt_prototype.py) bằng VS Code/Cursor.
2. Hoàn thiện các nội dung sau:
   * **System Prompt:** Viết chỉ thị cực kỳ nghiêm ngặt quy định vai trò, nhiệm vụ, định dạng output và **Operational Boundary (Ranh giới cấm)** của mô hình.
   * **Structured Output:** Định nghĩa định dạng JSON output rõ ràng.
   * **Adversarial Test Cases:** Viết ít nhất 3 prompts "tấn công" (Adversarial inputs) cố tình dụ AI vượt ranh giới hoặc đưa ra câu trả lời không được phép để kiểm tra xem ranh giới của bạn có thực sự vững chắc.
3. Chạy file python:
   ```bash
   python3 prompt_prototype.py
   ```
4. Kiểm tra xem các ranh giới an toàn có bị LLM phá vỡ hay không và ghi lại kết quả vào worksheet.

---

# 🏁 Phase 5 — EVALUATE (Nhóm, 20 min)

### AI Readiness Checklist:
1. [ ] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test?
2. [ ] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)?
3. [ ] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ?

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
[ ] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.
[ ] **NOT YET (Cần tích lũy thêm dữ liệu/xác lập baseline):** Trì hoãn để chuẩn bị thêm.
[ ] **NO-GO (Không khả thi / Rule-based tốt hơn):** Hủy bỏ dự án AI này.

**Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):**
> *Viết lý giải chi tiết tại đây*

---

# 📝 Phase 6 — REFLECTION (Cá nhân)
*Ghi nhận phản ánh của cá nhân bạn về việc phối hợp với AI trong buổi học hôm nay vào file `03-ai-log.md`.*
