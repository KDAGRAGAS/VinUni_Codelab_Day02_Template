# Phase 1 & 2 — Problem Scan & Quick Problem Cards
**Học viên:** Nguyễn Mạnh Hùng  
**Vai trò:** AI Product Engineer — Vin Smart Future (Vingroup)  
**Mục tiêu:** Quét tìm các cơ hội tối ưu hóa vận hành bằng AI tại các công ty thành viên Vingroup.

---

## 🔍 Phase 1 — SCAN: Danh sách 5 bài toán thực tế

Sử dụng **4 Lenses** (Lặp lại, Tốn thời gian, AI có thể tốt hơn, Pain từ người khác) để rà soát các điểm nghẽn (bottlenecks) trong vận hành:

| # | Subsidiary | Lens | Tên bài toán & Mô tả ngắn |
|---|---|---|---|
| **1** | **Xanh SM (GSM)** | Tốn thời gian / Pain từ người khác | **Xử lý sự cố sạc pin & điều phối cứu hộ khẩn cấp:** Tài xế báo hết pin hoặc xe dưới 5% giữa hành trình. Điều phối viên phải tra cứu thủ công GPS xe, mở bản đồ trạm sạc VinFast, kiểm tra trụ trống và soạn tin hướng dẫn thủ công (mất 12-15 phút/lượt). |
| **2** | **VinFast** | AI có thể tốt hơn | **Chẩn đoán ban đầu lỗi kỹ thuật xe qua mô tả của khách:** Khách hàng mô tả hiện tượng xe bằng tiếng Việt tự nhiên (*"xe đi qua gờ giảm tốc kêu lục cục ở bánh trước"*). Cố vấn dịch vụ mất thời gian tra cứu mã lỗi OBD-II và hướng dẫn kiểm tra ban đầu. |
| **3** | **Vinhomes** | Lặp lại / Tốn thời gian | **Phân loại & điều hướng phản ánh cư dân trên App:** Cư dân gửi khiếu nại (mất nước, rò rỉ, thang máy kêu, sửa chữa...). CSKH phải đọc, phân loại thủ công và chuyển tiếp đến đúng ban quản lý/kỹ thuật của từng tòa nhà, mất từ 4–12 tiếng. |
| **4** | **Vinmec** | Tốn thời gian / Pain từ người khác | **Soạn thảo tóm tắt hồ sơ xuất viện (Discharge Summary):** Bác sĩ mất 20–30 phút/bệnh nhân để tổng hợp kết quả xét nghiệm, chẩn đoán và phác đồ điều trị thành bản tóm tắt dễ hiểu cho bệnh nhân, gây quá tải giờ trực. |
| **5** | **Vinpearl** | Pain từ người khác | **Tổng hợp & cảnh báo đánh giá tiêu cực (Review Alert):** Quét các đánh giá trên Booking, Agoda, Google Maps để phát hiện và cảnh báo tức thời các phàn nàn nghiêm trọng về vệ sinh, thái độ phục vụ cho Quản lý khách sạn. |

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Chọn lọc Top 3 bài toán từ danh sách trên để phân tích nhanh:

### 📌 QUICK PROBLEM CARD #1 (Bài toán lựa chọn Deep-Dive)
```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                       │
│                                                                             │
│ Bài toán: Hỗ trợ điều phối viên xử lý khẩn cấp sự cố sạc pin / xe sắp cạn    │
│           pin trên đường của tài xế taxi điện Xanh SM.                      │
│ Công ty thành viên: [x] Xanh SM (GSM)                                       │
│                                                                             │
│ Ai đang đau (Actor)?                                                        │
│ - Tài xế Xanh SM: Stress vì nguy cơ chết máy giữa đường, trễ đón khách.     │
│ - Điều phối viên (Dispatcher): Quá tải vì phải tra cứu nhiều hệ thống.      │
│                                                                             │
│ Workflow thủ công hiện tại (5 bước):                                        │
│   1. Nhận báo cáo sự cố qua hotline/app tài xế                             │
│   ──> 2. Tra cứu toạ độ GPS định vị xe trên bản đồ nội bộ                   │
│   ──> 3. Tra cứu trạm sạc VinFast gần nhất còn trụ trống phù hợp           │
│   ──> 4. Soạn tin nhắn hướng dẫn/chỉ đường gửi cho tài xế                   │
│   ──> 5. Điều động xe cứu hộ sạc di động nếu pin < 5%                      │
│                                                                             │
│ Bước tốn thời gian nhất: Bước 3 & Bước 4 (mất 8-10 phút/lượt)                │
│ AI có thể nhảy vào hỗ trợ: Bước 3, 4, 5                                      │
│ - Tự động so khớp trạm sạc trống và soạn sẵn bản nháp hướng dẫn.           │
│ - Tự động phát hiện ranh giới nguy hiểm (pin < 5%) để kích hoạt cứu hộ.     │
│                                                                             │
│ Đo thành công bằng gì (Metric có số)?                                       │
│ - Giảm thời gian xử lý sự cố từ 15 phút ──> dưới 3 phút/lượt.               │
│ - Đảm bảo 100% trường hợp pin < 5% không bị điều đi trạm sạc xa (> 5km).   │
│                                                                             │
│ Quick Architecture: [x] LLM Feature (có Human-in-the-loop review)           │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

### 📌 QUICK PROBLEM CARD #2
```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                       │
│                                                                             │
│ Bài toán: Tự động phân loại và điều hướng phản ánh sự cố của cư dân Vinhomes│
│ Công ty thành viên: [x] Vinhomes                                            │
│                                                                             │
│ Ai đang đau (Actor)?                                                        │
│ - Cư dân Vinhomes: Bực bội vì phản ánh mất nhiều tiếng mới được xử lý.       │
│ - Ban Quản lý Tòa nhà: Mất thời gian sàng lọc và chuyển tiếp thủ công.      │
│                                                                             │
│ Workflow thủ công hiện tại (4 bước):                                        │
│   1. Cư dân gửi form/tin nhắn phản ánh qua App Vinhomes Resident            │
│   ──> 2. Nhân viên CSKH đọc và phân loại mức độ khẩn cấp (P1 -> P4)         │
│   ──> 3. Tìm kỹ thuật viên phụ trách tòa nhà tương ứng                      │
│   ──> 4. Tạo ticket giao việc trên Jira/ERP nội bộ                          │
│                                                                             │
│ Bước tốn thời gian nhất: Bước 2 & Bước 3 (chờ duyệt mất 2-6 tiếng)          │
│ AI có thể nhảy vào hỗ trợ: Bước 2 (Phân loại tự động & gán tag ưu tiên)    │
│                                                                             │
│ Đo thành công bằng gì (Metric có số)?                                       │
│ - Rút ngắn thời gian tiếp nhận & phân bổ ticket từ 4 tiếng ──> 2 phút.      │
│ - Độ chính xác phân loại sự cố đạt >= 92%.                                  │
│                                                                             │
│ Quick Architecture: [x] Rule + LLM Classifier                               │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

### 📌 QUICK PROBLEM CARD #3
```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                       │
│                                                                             │
│ Bài toán: Hỗ trợ bác sĩ soạn thảo tóm tắt hồ sơ bệnh án xuất viện tại Vinmec│
│ Công ty thành viên: [x] Vinmec                                              │
│                                                                             │
│ Ai đang đau (Actor)?                                                        │
│ - Bác sĩ điều trị: Mất quá nhiều thời gian hành chính thay vì khám bệnh.     │
│ - Bệnh nhân: Chờ đợi lâu để nhận giấy ra viện và hướng dẫn dùng thuốc.      │
│                                                                             │
│ Workflow thủ công hiện tại (4 bước):                                        │
│   1. Bác sĩ mở hồ sơ bệnh án điện tử (EMR) của bệnh nhân                    │
│   ──> 2. Đọc lại lịch sử xét nghiệm, hình ảnh chẩn đoán, thuốc đã dùng      │
│   ──> 3. Tự gõ tay bản tóm tắt quá trình điều trị và lời dặn xuất viện      │
│   ──> 4. In và ký xác nhận bàn giao cho bệnh nhân                           │
│                                                                             │
│ Bước tốn thời gian nhất: Bước 2 & 3 (mất 20-25 phút/hồ sơ)                  │
│ AI có thể nhảy vào hỗ trợ: Bước 3 (Trích xuất & soạn thảo bản nháp tóm tắt) │
│                                                                             │
│ Đo thành công bằng gì (Metric có số)?                                       │
│ - Giảm thời gian soạn tóm tắt xuất viện từ 25 phút ──> dưới 5 phút.         │
│ - 100% hồ sơ bắt buộc bác sĩ chuyên môn ký duyệt (bảo mật y tế).           │
│                                                                             │
│ Quick Architecture: [x] LLM Feature (Strict Human-in-the-loop)              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 🗳️ Quyết định lựa chọn bài toán Deep-Dive
Nhóm thống nhất chọn **Quick Problem Card #1: Xanh SM — Trợ lý điều vận xử lý sự cố pin & cứu hộ di động**.

* **Lý do chọn Card #1:** 
  - Tác động vận hành trực tiếp, diễn ra liên tục theo thời gian thực (real-time).
  - Có ranh giới an toàn rất rõ ràng (Operational Boundary: ngưỡng pin < 5% và nhãn duyệt `[DRAFT_ONLY]`).
  - Phù hợp hoàn hảo để xây dựng và kiểm thử prompt prototype an toàn trên nền tảng Gemini.
* **Lý do hoãn Card #2 & #3:**
  - *Card #2 (Vinhomes):* Rủi ro thấp nhưng hệ thống ERP nội bộ còn phức tạp, ưu tiên giải pháp Rule-based trước.
  - *Card #3 (Vinmec):* Dữ liệu y tế cực kỳ nhạy cảm (HIPAA/Bảo mật y khoa), rào cản pháp lý cao hơn nhiều.

