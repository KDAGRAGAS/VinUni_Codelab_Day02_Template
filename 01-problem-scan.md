# 01 — Problem Scan & Quick Cards

**Họ tên:** ____________ · **Branch:** ____________ · **Ngày:** 06/11/2026

> **Điểm xuất phát.** Tôi đóng vai AI Product Engineer tại Vin Smart Future và bắt đầu từ một thay đổi có hạn chót cứng: Thông tư 31/2026/TT-BXD có hiệu lực từ 15/12/2026, buộc mọi chung cư hiện hữu rà soát khu để xe, sạc và đổi pin xe điện trong 6 tháng. Cùng lúc, VinFast đã giao hơn 710.000 xe máy điện trong 9 tháng đầu 2026, gấp 3 lần cùng kỳ. Vì vậy tôi quét các bài toán quanh "xe điện trong khu đô thị", trải trên nhiều công ty thành viên.

---

## 🔍 Phase 1 — SCAN

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|---|---|---|
| 1 | **Vinhomes** | Tốn thời gian | Kỹ sư kỹ thuật tòa nhà phải rà soát khu sạc và đổi pin của từng tòa theo Thông tư 31 trước 15/6/2027: gom hồ sơ thiết kế và PCCC, đối chiếu từng hạng mục trên Excel, viết báo cáo. Ước tính ~16 giờ công/tòa. |
| 2 | **Vinhomes** | Lặp lại | Ban quản lý trả lời đi trả lời lại cùng một nhóm câu hỏi của cư dân về xe điện (sạc ở đâu, phí bao nhiêu, đăng ký thế nào, vì sao không được dắt xe lên căn hộ) qua app Vinhomes Resident và hotline. |
| 3 | **VinFast** | AI có thể tốt hơn | Khách mô tả lỗi xe máy điện bằng tiếng Việt tự do ("sạc không vào", "đang chạy tự ngắt"); nhân viên phân loại thủ công trước khi hẹn xưởng, trong khi số xe giao năm 2026 tăng gấp 3. |
| 4 | **Vinhomes** | Tốn thời gian | Ban quản trị phải tổng hợp hàng trăm ý kiến cư dân (phiếu giấy, nhóm Zalo, email) về phương án khu sạc để chuẩn bị hội nghị nhà chung cư. |
| 5 | **V-Green** | Pain từ người khác | Ô tô đã sạc đầy vẫn chiếm trụ trong hầm chung cư; khu sạc xe máy bị tài xế công nghệ từ ngoài vào dùng, cư dân phải chờ. |
| 6 | **Vinpearl** | Pain từ người khác | Review khách sạn trên Booking, Agoda, Google Maps nằm rải rác; phàn nàn khẩn cấp ("phòng bẩn", "nhân viên thái độ") đến tay quản lý chậm. |

**Lọc nhanh trước khi chọn top 3:**
- **#5 không cần AI.** Đây là bài toán rule-based: đếm phút sau khi pin đầy rồi tính phí. V-Green đã làm đúng như vậy tại trạm sạc DC: chỉ miễn phí 10 phút đỗ sau khi sạc đầy.
- **#6** có giá trị nhưng nằm ngoài trọng tâm "xe điện trong khu đô thị" mà tôi đang quét.
- **#4** là một phần của cùng chuỗi việc với #1 và #2; để sau.
- Top 3: **#1, #2, #3**.

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

> Thời gian trong các thẻ là **ước tính của tôi**, chưa đo ngoài thực tế; baseline thật sẽ lấy ở bước pilot.

### Card #1 — Vinhomes: rà soát khu sạc theo Thông tư 31

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                       │
│                                                             │
│ Bài toán: Rà soát khu sạc/đổi pin xe điện của từng tòa      │
│ chung cư theo Thông tư 31/2026 mất ~2 ngày công/tòa.        │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau? Kỹ sư kỹ thuật tòa nhà (làm rà soát) và        │
│ Trưởng ban kỹ thuật (duyệt hàng loạt báo cáo).              │
│                                                             │
│ Workflow thủ công hiện tại:                                 │
│   1. Gom và đọc hồ sơ thiết kế, PCCC, danh sách trụ         │
│   ──> 2. Khảo sát hiện trường                               │
│   ──> 3. Đối chiếu 14 hạng mục TT31 trên Excel              │
│   ──> 4. Viết báo cáo, phân loại A/B/C                      │
│   ──> 5. Trưởng ban duyệt (trả về nếu thiếu)                │
│                                                             │
│ Bước tốn nhất? 1, 3, 4 (ước tính 660 phút/tòa)              │
│ AI nhảy vào đâu? Trích số liệu từ hồ sơ PDF và ghi chú      │
│ hiện trường; soạn báo cáo nháp. Phần so ngưỡng: rule.       │
│                                                             │
│ Metric: thời gian thao tác ~16 giờ ──> ≤ 6 giờ/tòa;         │
│ 0 hạng mục "không đạt" bị bỏ sót trên 10 tòa pilot.         │
│                                                             │
│ Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

### Card #2 — Vinhomes: trợ lý trả lời cư dân về xe điện

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                       │
│                                                             │
│ Bài toán: Ban quản lý trả lời thủ công các câu hỏi lặp lại  │
│ của cư dân về gửi và sạc xe điện.                           │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau? Nhân viên CSKH/lễ tân ban quản lý; cư dân      │
│ phải chờ câu trả lời.                                       │
│                                                             │
│ Workflow thủ công hiện tại:                                 │
│   1. Cư dân hỏi qua app/hotline/quầy lễ tân                 │
│   ──> 2. Nhân viên tra quy chế gửi xe, biểu phí             │
│   ──> 3. Hỏi lại bộ phận kỹ thuật nếu hỏi vị trí sạc        │
│   ──> 4. Soạn và gửi câu trả lời                            │
│                                                             │
│ Bước tốn nhất? 2-3 (ước tính 5 phút/lượt, chưa tính chờ)    │
│ AI nhảy vào đâu? Tra quy chế từng tòa (RAG) và soạn         │
│ câu trả lời nháp cho nhân viên duyệt.                       │
│                                                             │
│ Metric: 70% câu hỏi thường gặp có nháp đúng trong < 1 phút; │
│ thời gian phản hồi trung bình giảm 50%.                     │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

### Card #3 — VinFast: phân loại mô tả lỗi xe máy điện

```text
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                       │
│                                                             │
│ Bài toán: Phân loại mô tả lỗi xe máy điện bằng tiếng Việt   │
│ của khách để hẹn đúng xưởng, đúng kỹ thuật viên.            │
│ Công ty thành viên: [x] VinFast  [ ] Xanh SM  [ ] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác                   │
│                                                             │
│ Ai đang đau? Tổng đài viên dịch vụ, kỹ thuật viên xưởng;    │
│ khách bị hẹn sai phải quay lại lần hai.                     │
│                                                             │
│ Workflow thủ công hiện tại:                                 │
│   1. Khách gọi/nhắn mô tả lỗi                               │
│   ──> 2. Tổng đài ghi lại bằng lời của mình                 │
│   ──> 3. Kỹ thuật viên đọc, gán nhóm lỗi                    │
│   ──> 4. Hẹn lịch xưởng theo nhóm lỗi                       │
│                                                             │
│ Bước tốn nhất? 2-3 (ước tính 8 phút/phiếu)                  │
│ AI nhảy vào đâu? Gợi ý nhóm lỗi và câu cần hỏi thêm;        │
│ cờ "nguy cơ pin" luôn chuyển người xử lý ngay.              │
│                                                             │
│ Metric: 85% phiếu gợi ý đúng nhóm lỗi; phân loại < 1 phút.  │
│                                                             │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent │
└─────────────────────────────────────────────────────────────┘
```

---

## 🗳️ Đề xuất chọn cho Deep-Dive: Card #1

| Tiêu chí | Card #1 Rà soát TT31 | Card #2 Trợ lý cư dân | Card #3 Phân loại lỗi xe |
|---|---|---|---|
| Áp lực thật | Hạn pháp lý 15/6/2027 | Có, nhưng không có hạn chót | Có, tăng theo số xe bán |
| Đầu ra đo được | Checklist có/không, giờ công | Tỷ lệ trả lời đúng | Tỷ lệ phân đúng nhóm |
| Dữ liệu nhóm tiếp cận được | Thông tư công khai; hồ sơ tòa phải xin | Quy chế từng tòa chưa thống nhất | Phiếu sửa chữa nội bộ VinFast |
| Rủi ro khi AI sai | Cao, nhưng chặn được bằng rule + kỹ sư ký | Trung bình (trả lời sai quy định) | Cao (an toàn pin) |

**Lý do loại hai thẻ còn lại:**
- **Card #2:** câu trả lời phụ thuộc quy chế khu sạc của từng tòa, mà quy chế này sẽ thay đổi sau đợt rà soát ở Card #1. Làm chatbot trước khi quy chế ổn định thì dễ trả lời sai. Nên làm sau Card #1.
- **Card #3:** dính tới an toàn pin và cần dữ liệu phiếu sửa chữa có nhãn của VinFast, thứ nhóm không tiếp cận được trong phạm vi lab.

---

## Nguồn

- [VOV — Quy định mới: chung cư bắt buộc phải có khu vực riêng cho xe điện (07/07/2026)](https://vov.vn/o-to-xe-may/quy-dinh-moi-chung-cu-bat-buoc-phai-co-khu-vuc-rieng-cho-xe-dien-post1312811.vov)
- [LuatVietnam — Chung cư hiện hữu phải rà soát khu vực sạc xe điện, đổi pin trong 6 tháng](https://luatvietnam.vn/tin-van-ban-moi/tu-15-12-2026-chung-cu-hien-huu-phai-ra-soat-khu-vuc-sac-xe-dien-doi-pin-trong-vong-6-thang-186-109699-article.html)
- [Vietbao — Vingroup báo lãi 43.700 tỷ đồng sau 9 tháng 2026 (số xe máy điện VinFast giao)](https://vietbao.vn/vingroup-bao-lai-43700-ty-dong-sau-9-thang-gap-gan-6-lan-cung-ky-613800.html)
- [VnReview — V-Green chỉ miễn phí 10 phút đỗ sau khi sạc đầy](https://vnreview.vn/threads/vinfast-ra-quy-dinh-moi-khi-do-xe-tai-tram-sac-chi-con-10-phut-mien-phi-sau-khi-sac-day-qua-phut-nao-tinh-tien-phut-ay.67894/latest)
- [Vietbao — Người dân Hà Nội sạc xe điện tại chung cư ra sao (Vinhomes Metropolis)](https://vietbao.vn/nguoi-dan-ha-noi-dang-sac-xe-dien-tai-chung-cu-nha-rieng-ra-sao-551636.html)
- [VnExpress — Dân ở chung cư sạc ô tô điện thế nào?](https://vnexpress.net/dan-o-chung-cu-sac-oto-dien-the-nao-4745947.html)
