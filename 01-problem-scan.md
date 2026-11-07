# Lab 02 — Problem Scan & Quick-Assess

## Phase 1 — SCAN: Danh sách 5 bài toán

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|---|---|---|
| 1 | **VinFast (V-GREEN)** | Tốn thời gian | Phân bổ vị trí lắp đặt trạm sạc chưa đồng đều (nhiều trạm vắng khách <15% công suất, trong khi các điểm trung tâm quá tải >85%). Nhân viên quy hoạch mất nhiều thời gian đọc phản ánh của tài xế và hồ sơ khảo sát mặt bằng để tìm điểm lắp mới hoặc điều chuyển trụ sạc. |
| 2 | **Xanh SM** | Tốn thời gian | Điều phối viên phải tự tra bản đồ, tìm trạm sạc còn trụ trống và nhắn tin chỉ đường hoặc gọi xe cứu hộ khi tài xế báo sắp hết pin giữa đường (mất ~15 phút/lượt). |
| 3 | **Vinhomes** | Lặp lại | Nhân viên CSKH phải đọc và phân loại thủ công hàng nghìn phản ánh của cư dân trên App Vinhomes Resident (điện, nước, an ninh, tiếng ồn) để chuyển cho ban kỹ thuật từng tòa nhà. |
| 4 | **Vinmec** | Pain từ người khác | Bác sĩ mất 20–30 phút/ca để tổng hợp kết quả xét nghiệm, bệnh án điện tử và gõ giấy ra viện (Discharge Summary) cho từng bệnh nhân. |
| 5 | **Vinpearl** | AI có thể tốt hơn | Quản lý khách sạn không đọc xuể hàng nghìn review trên Booking, Agoda, Google Maps để lọc ra các phàn nàn khẩn cấp cần xử lý ngay. |

---

## Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Chọn 3 bài toán từ bảng trên để đánh giá nhanh: **#1 (VinFast/V-GREEN Phân bổ vị trí trạm sạc)**, **#3 (Vinhomes CSKH)** và **#4 (Vinmec Hồ sơ xuất viện)**.

### Card #1: VinFast (V-GREEN) — Phân bổ vị trí lắp đặt & điều chuyển trụ sạc

```text
┌────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                      │
│                                                                            │
│ Bài toán (1 câu): Tổng hợp phản ánh của tài xế và thẩm định hồ sơ mặt bằng │
│ để đề xuất vị trí lắp mới hoặc điều chuyển trụ sạc từ nơi vắng khách (<15%)│
│ sang khu vực đang quá tải (>85%).                                          │
│                                                                            │
│ Công ty thành viên: [x] VinFast (V-GREEN)  [x] Xanh SM  [ ] Vinhomes       │
│                     [ ] Vinmec             [ ] Khác: __________________    │
│                                                                            │
│ Đối tượng gặp vấn đề (Actor):                                              │
│ - Chuyên viên Quy hoạch trạm sạc (đọc hồ sơ & phản ánh thủ công quá tải)   │
│ - Tài xế Xanh SM & Chủ xe VinFast (phải xếp hàng chờ sạc ở điểm nóng)      │
│                                                                            │
│ Workflow thủ công hiện tại (5 bước):                                       │
│   1. Xuất log hiệu suất các trạm sạc từ CMS (lọc trạm <15% và trạm >85%)   │
│   ──> 2. Đọc thủ công các ticket phản ánh của tài xế để biết lý do thực tế │
│          (VD: trạm vắng do hầm thu phí gửi xe đắt/cấm giờ, nơi nào thiếu)  │
│   ──> 3. Đọc từng bộ Hồ sơ khảo sát mặt bằng mới quanh khu vực đó (kiểm    │
│          tra công suất điện 3 pha, diện tích đỗ xe, lối vào, giá thuê)     │
│   ──> 4. Tính phương án cấu hình trạm (rút bớt trụ ở trạm vắng chuyển sang │
│          lắp thêm trụ AC 11kW / DC 60kW-150kW khớp với tải điện mặt bằng)  │
│   ──> 5. Viết Tờ trình đề xuất phân bổ/điều chuyển gửi quản lý duyệt.      │
│                                                                            │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2, 3 & 5 (⏱ 45 phút / điểm khảo sát) │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3 & 5                        │
│ (Tóm tắt nguyên nhân từ ticket tài xế -> trích xuất thông số hồ sơ mặt     │
│  bằng -> soạn bản nháp tờ trình đề xuất vị trí & số lượng trụ sạc)         │
│                                                                            │
│ Đo thành công bằng gì (Metric có số)?                                      │
│ - Giảm thời gian rà soát & viết đề xuất từ 45 phút ──> dưới 8 phút/điểm.   │
│ - Giảm tỷ lệ trạm lắp mới bị vắng khách (<15% sau 3 tháng) từ 20% ──> <5%. │
│                                                                            │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent        │
└────────────────────────────────────────────────────────────────────────────┘
```

### Card #2: Vinhomes — Phân loại phản ánh cư dân trên App Vinhomes Resident

```text
┌────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                      │
│                                                                            │
│ Bài toán (1 câu): Tự động phân loại mức độ ưu tiên và chuyển phản ánh của  │
│ cư dân trên App Vinhomes Resident về đúng bộ phận kỹ thuật/an ninh tòa nhà.│
│                                                                            │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes                 │
│                     [ ] Vinmec   [ ] Khác: __________________              │
│                                                                            │
│ Đối tượng gặp vấn đề (Actor):                                              │
│ - Nhân viên CSKH Vinhomes (tốn công đọc phân loại), Cư dân (chờ xử lý lâu) │
│                                                                            │
│ Workflow thủ công hiện tại (4 bước):                                       │
│   1. Cư dân gửi phản ánh kèm mô tả chữ và ảnh lên App                      │
│   ──> 2. Nhân viên CSKH mở đọc từng ticket để xem thuộc mảng nào, gấp không│
│   ──> 3. Chuyển tiếp thủ công trên phần mềm cho Ban quản lý/Kỹ thuật tòa   │
│   ──> 4. Nhắn tin xác nhận đã tiếp nhận cho cư dân.                        │
│                                                                            │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 8 phút / ticket)           │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3 & 4                        │
│ (Đọc nội dung -> gắn nhãn bộ phận & mức độ khẩn cấp -> soạn nháp phản hồi) │
│                                                                            │
│ Đo thành công bằng gì (Metric có số)?                                      │
│ - Giảm thời gian phân luồng lần đầu từ 4 giờ ──> dưới 15 phút.             │
│ - Tỷ lệ chuyển đúng bộ phận kỹ thuật đạt >= 92%.                           │
│                                                                            │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent        │
└────────────────────────────────────────────────────────────────────────────┘
```

### Card #3: Vinmec — Hỗ trợ bác sĩ soạn Tóm tắt hồ sơ xuất viện

```text
┌────────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                      │
│                                                                            │
│ Bài toán (1 câu): Trích xuất thông tin khám và xét nghiệm từ bệnh án điện  │
│ tử (EMR) để soạn nháp giấy Tóm tắt hồ sơ xuất viện cho bác sĩ duyệt.       │
│                                                                            │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes                 │
│                     [x] Vinmec   [ ] Khác: __________________              │
│                                                                            │
│ Đối tượng gặp vấn đề (Actor):                                              │
│ - Bác sĩ nội trú/điều trị (mất nhiều thời gian gõ giấy tờ hành chính)      │
│ - Bệnh nhân (phải đợi lâu khi làm thủ tục ra viện)                         │
│                                                                            │
│ Workflow thủ công hiện tại (4 bước):                                       │
│   1. Bác sĩ mở phần mềm HIS/EMR xem lại lịch sử khám, xét nghiệm, đơn thuốc│
│   ──> 2. Tổng hợp lại quá trình điều trị của bệnh nhân                     │
│   ──> 3. Gõ bản Tóm tắt hồ sơ xuất viện và lời dặn uống thuốc/tái khám     │
│   ──> 4. Bác sĩ đọc kiểm tra lại, ký xác nhận và in cho bệnh nhân.         │
│                                                                            │
│ Bước nào tốn thời gian/lỗi nhất? Bước 1, 2 & 3 (⏱ 25 phút / bệnh nhân)    │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 1, 2 & 3                        │
│ (Gom dữ liệu từ EMR -> tạo bản nháp tóm tắt xuất viện để bác sĩ sửa/duyệt) │
│                                                                            │
│ Đo thành công bằng gì (Metric có số)?                                      │
│ - Giảm thời gian bác sĩ làm giấy ra viện từ 25 phút ──> dưới 5 phút/ca.    │
│ - 100% bản tóm tắt có bác sĩ kiểm tra trước khi in.                        │
│                                                                            │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent        │
└────────────────────────────────────────────────────────────────────────────┘
```

---

## Quyết định chọn bài toán để làm Deep-Dive

Nhóm chọn **Card #1 (VinFast / V-GREEN — Phân bổ vị trí lắp đặt & điều chuyển trụ sạc)** để phân tích sâu ở Phase 3 vì:
* **Lý do chưa chọn Card #2 (Vinhomes):** Phần lớn ticket có thể xử lý đơn giản hơn bằng cách sửa lại giao diện App (thêm menu chọn sẵn loại sự cố bằng Rule-based) mà chưa cần dùng tới LLM.
* **Lý do chưa chọn Card #3 (Vinmec):** Hồ sơ bệnh án liên quan trực tiếp đến sức khỏe và bảo mật y tế, nếu AI tóm tắt nhầm liều thuốc hoặc chẩn đoán sẽ rất rủi ro nên khó áp dụng thử nghiệm nhanh.
* **Lý do chọn Card #1 (VinFast / V-GREEN):** Giải quyết vấn đề rất thực tế là chỗ thì thừa trụ sạc không ai dùng, chỗ thì tài xế Xanh SM/VinFast xếp hàng chờ quá tải. Bài toán này kết hợp rõ cả dữ liệu số (tải trạm sạc) lẫn văn bản tự do (tin nhắn phàn nàn của tài xế và biên bản khảo sát mặt bằng), rất phù hợp để làm một **LLM Feature** có người duyệt (Human-in-the-loop).
