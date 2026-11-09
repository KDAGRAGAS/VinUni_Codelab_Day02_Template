# 02 — Problem Deep-Dive Report

## Rà soát khu sạc xe điện ở chung cư Vinhomes theo Thông tư 31/2026

**Nhóm:** ____________ · **Thành viên:** ____________ · **Ngày:** 06/11/2026
**Bài toán:** Quick Card #1 trong `01-problem-scan.md` · **Sơ đồ quy trình hiện tại:** `04-workflow-diagram.png`

> **Tóm tắt.** Mỗi tòa chung cư Vinhomes đang vận hành phải được rà soát khu để xe, sạc và đổi pin xe điện theo Thông tư 31/2026/TT-BXD trước 15/6/2027. Làm tay mất ước tính ~16 giờ công kỹ sư mỗi tòa, trong đó ~11 giờ là đọc hồ sơ, đối chiếu và viết báo cáo. Nhóm đề xuất **Rule + LLM Feature**: LLM đọc hồ sơ và soạn nháp, rule engine so ngưỡng quy chuẩn, kỹ sư duyệt và ký. Quyết định: **GO với scope hẹp**, pilot 10 tòa trong 4 tuần, có điều kiện dừng rõ ràng.

---

## 🏛️ Bối cảnh

- **Hạn chót pháp lý.** Thông tư 31/2026/TT-BXD sửa đổi QCVN 04:2021/BXD về nhà chung cư, có hiệu lực từ 15/12/2026. Trong 6 tháng, chủ sở hữu hoặc đơn vị quản lý chung cư hiện hữu phải rà soát khu để xe điện, sạc điện và đổi pin; chưa đạt thì phải sửa chữa, cải tạo.
- **Cầu tăng nhanh.** VinFast giao hơn 710.000 xe máy điện trong 9 tháng 2026, gấp 3 lần cùng kỳ.
- **Rủi ro đã thành sự cố.** Một xe máy điện cháy trong hầm chung cư Phúc Yên (TP.HCM) làm khói lan lên tầng 10. Không có giải pháp ngăn cháy cho khu sạc tập trung trong nhà bị phạt 80–100 triệu đồng với tổ chức theo Nghị định 106/2025/NĐ-CP.
- **Quy mô.** Chỉ riêng Vinhomes Grand Park đã có 71 tòa căn hộ. Toàn danh mục ước tính vài trăm tòa; báo cáo này dùng giả định **300 tòa** để tính tác động.

---

# 🏗️ Phase 3 — DEEP-DIVE

## 3.1. Current-State Workflow

Quy trình rà soát **một tòa** hiện nay, do kỹ sư kỹ thuật của đơn vị vận hành tòa nhà thực hiện:

```text
 Khối Kỹ thuật Vinhomes
   │  🔄 H1 — giao yêu cầu rà soát (công văn, email)
   ▼
[B1] Nhận yêu cầu, lập kế hoạch rà soát                         ⏱ 0,5 giờ
     Ai: Kỹ sư tòa nhà │ In: công văn │ Out: kế hoạch, danh mục hồ sơ cần gom
   │
   ▼
[B2] 🔴 Gom và đọc hồ sơ: bản vẽ hầm, sơ đồ điện, hồ sơ PCCC đã
        thẩm duyệt, danh sách và công suất trụ sạc; ghi số liệu    ⏱ 4 giờ
     Ai: Kỹ sư tòa nhà │ In: kho lưu trữ chủ đầu tư, BQL, V-Green
     Out: bộ hồ sơ lẫn lộn PDF scan, bản vẽ, Excel
   │  🔄 H2 — xin danh sách, công suất trụ từ V-Green (chờ 1–2 ngày)
   ▼
[B3] Khảo sát hiện trường: đếm chỗ sạc, đo khoảng cách, kiểm tra
     vách ngăn, đầu báo, camera; chụp ảnh                       ⏱ 3 giờ
     Ai: Kỹ sư tòa nhà │ In: bản vẽ │ Out: ghi chú tay, ảnh
   │
   ▼
[B4] 🔴 Đối chiếu 14 hạng mục Thông tư 31 trên Excel             ⏱ 4 giờ
     Ai: Kỹ sư tòa nhà │ In: hồ sơ + ghi chú │ Out: bảng Đạt / Không đạt
   │
   ▼
[B5] 🔴 Viết báo cáo rà soát, phân loại A/B/C, đề xuất cải tạo   ⏱ 3 giờ
     Ai: Kỹ sư tòa nhà │ In: bảng đối chiếu │ Out: báo cáo Word
   │  🔄 H3 — gửi Trưởng ban kỹ thuật
   ▼
[B6] Trưởng ban kỹ thuật duyệt                                  ⏱ 1,5 giờ
     Ai: Trưởng ban │ Out: báo cáo đã ký, hoặc trả về B4 nếu thiếu
     dữ liệu (thêm 2–3 ngày chờ)
   │  🔄 H4 — gửi về Khối Kỹ thuật Vinhomes
   ▼
 Kết quả: tòa được xếp loại A (đạt) / B (cải tạo nhỏ) / C (phải di dời khu sạc)

🔴 = Bottleneck    🔄 = Handoff
⏱ Tổng thời gian thao tác: ~16 giờ công/tòa (ước tính) · Lead time: ~5 ngày làm việc
```

| Bước | Thời gian (ước tính) | Bottleneck | Vì sao |
|---|---:|:---:|---|
| B1 Lập kế hoạch | 0,5 giờ | | |
| B2 Gom và đọc hồ sơ | 4 giờ | 🔴 | Hồ sơ nằm ở 3 nơi, nhiều bản scan; phải chờ V-Green gửi dữ liệu trụ |
| B3 Khảo sát hiện trường | 3 giờ | | Bắt buộc có người; giữ nguyên |
| B4 Đối chiếu Thông tư 31 | 4 giờ | 🔴 | Làm tay 14 hạng mục; mỗi kỹ sư hiểu quy chuẩn một kiểu |
| B5 Viết báo cáo | 3 giờ | 🔴 | Văn bản dài, cấu trúc lặp lại giữa các tòa |
| B6 Duyệt | 1,5 giờ | | Hay bị trả về ít nhất 1 lần vì thiếu dữ liệu |
| **Tổng** | **16 giờ** | | **11/16 giờ nằm ở B2, B4, B5** |

---

## 3.2. Problem Statement (6-field)

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Kỹ sư kỹ thuật của đơn vị vận hành từng tòa chung cư Vinhomes (người làm rà soát) và Trưởng ban kỹ thuật (người duyệt). Người hưởng lợi cuối: cư dân và Khối Kỹ thuật Vinhomes. |
| **2. Current Workflow** | 6 bước, gần như hoàn toàn thủ công: gom và đọc hồ sơ từ kho lưu trữ, ban quản lý và V-Green → khảo sát hiện trường → đối chiếu 14 hạng mục Thông tư 31 trên Excel → viết báo cáo Word, xếp loại A/B/C → trưởng ban duyệt. Công cụ: PDF scan, bản vẽ, Excel, Word, email. Ước tính ~16 giờ công và ~5 ngày làm việc mỗi tòa. |
| **3. Bottleneck** | B2 + B4 + B5 chiếm ~11/16 giờ: đọc hồ sơ phi cấu trúc để lấy số liệu (số chỗ sạc mỗi phân vùng, công suất trụ, tầng đặt, thiết bị báo cháy, đầu báo khí), đối chiếu từng ngưỡng, rồi viết lại cùng một khuôn báo cáo cho từng tòa. Mỗi kỹ sư diễn giải quy chuẩn khác nhau nên báo cáo hay bị trả về. |
| **4. Business Impact** | **(a) Giờ công:** 300 tòa × 16 giờ = 4.800 giờ ≈ 600 ngày công kỹ sư, phải xong trong ~7 tháng, chồng lên việc vận hành hằng ngày.<br>**(b) Rủi ro bỏ sót:** một hạng mục "không đạt" bị bỏ sót nghĩa là khu sạc tiếp tục chạy sai chuẩn — rủi ro cháy và mức phạt 80–100 triệu đồng mỗi vi phạm với tổ chức.<br>**(c) Trễ hạn:** mỗi vòng trả về thêm 2–3 ngày, đẩy hồ sơ cải tạo và thẩm duyệt PCCC dồn về sát hạn 15/6/2027. |
| **5. Success Metric** | **1. Hiệu suất:** thời gian thao tác của kỹ sư từ ~16 giờ xuống **≤ 6 giờ/tòa** (giảm ≥ 60%).<br>**2. An toàn:** phát hiện **100%** hạng mục "không đạt" trên bộ 10 tòa pilot, so với đáp án do kỹ sư cao cấp rà soát độc lập.<br>**3. Chất lượng trích xuất:** ≥ 95% trường số liệu trích đúng.<br>**4. Luồng việc:** tỷ lệ báo cáo bị trả về ≤ 20%; lead time từ ~5 xuống ≤ 2 ngày làm việc. |
| **6. Operational Boundary** | **AI được phép:** đọc hồ sơ được cung cấp; trích số liệu kèm trích dẫn trang nguồn; soạn báo cáo nháp có nhãn `[DRAFT_ONLY]` ở đầu; đánh dấu `CẦN KIỂM TRA HIỆN TRƯỜNG` khi thiếu hoặc mâu thuẫn dữ liệu.<br>**AI TUYỆT ĐỐI KHÔNG:** tự quyết Đạt/Không đạt (việc của rule engine); kết luận tòa "đạt" khi còn bất kỳ hạng mục thiếu dữ liệu; điền số liệu không có trong hồ sơ; tự gửi báo cáo cho cư dân, ban quản trị hay cơ quan PCCC; đưa ý kiến pháp lý; sửa ngưỡng quy chuẩn.<br>**Điểm phải duyệt:** kỹ sư ký từng báo cáo; trưởng ban duyệt tay mọi tòa loại C; hồ sơ gửi cơ quan nhà nước do người nộp. |

---

## 3.3. Future-State Flow & AI Fit

### AI-Fit Matrix theo từng việc con

| Việc con | Rule / State-machine | LLM Feature | Agentic Loop | Chọn |
|---|---|---|---|---|
| Lấy số liệu từ hồ sơ PDF, bản vẽ, ghi chú tay | Không đọc được văn bản phi cấu trúc | Trích được, kèm trích dẫn để người kiểm | Thừa | **LLM** |
| So 14 hạng mục với ngưỡng (22 kW, 25/50 chỗ, 2 m, có/không đầu báo...) | Chính xác, giải thích được, kiểm toán được | Có thể tính nhầm hoặc diễn giải lệch | Thừa | **Rule** |
| Soạn báo cáo, đề xuất xếp loại A/B/C | Mẫu cứng, không xử lý được ngoại lệ | Viết tốt từ kết quả của rule | Thừa | **LLM** |
| Tự gom hồ sơ, tự gửi yêu cầu V-Green, tự nộp hồ sơ PCCC | — | — | Làm được nhưng rủi ro pháp lý cao, quy trình đã cố định | **Không làm** |

**Mức AI Fit:** [x] Rule / State-Machine  [x] LLM Feature  [ ] Agentic Loop → **hybrid Rule + LLM Feature**.

Không dùng Agent vì các bước đã cố định, không cần tự lập kế hoạch, và mọi hành động ra bên ngoài (gửi, nộp) đều cần một người chịu trách nhiệm pháp lý.

### Future-State Flow

```text
 Khối Kỹ thuật Vinhomes
   │  🔄 H1 — giao yêu cầu qua hệ thống, kèm danh mục hồ sơ chuẩn
   ▼
[F1] 🟢 Kỹ sư nhận yêu cầu, gom và tải hồ sơ lên hệ thống        ⏱ 1 giờ
     (dữ liệu trụ lấy thẳng từ V-Green thay vì xin qua email)
   │
   ▼
[F2] 🔵 AI STEP — LLM trích số liệu thành JSON:
     tầng đặt, số chỗ mỗi phân vùng, công suất trụ, khoảng cách,
     báo cháy, camera, đầu báo CO/HF... + trích dẫn trang + độ tin cậy
   │
   ▼
[F3] 🟢 HUMAN — Kỹ sư kiểm nhanh bảng trích xuất                 ⏱ 0,5 giờ
     ↩️ Trường độ tin cậy thấp / không có trích dẫn
        → chuyển vào danh sách "CẦN KIỂM TRA HIỆN TRƯỜNG"
   │
   ▼
[F4] 🟢 HUMAN — Khảo sát hiện trường theo danh sách hệ thống sinh,
     chỉ đo những gì hồ sơ thiếu hoặc mâu thuẫn                  ⏱ 2 giờ
   │
   ▼
[F5] ⚙️ RULE ENGINE — so 14 hạng mục → Đạt / Không đạt / Thiếu dữ liệu
     ↩️ Còn "Thiếu dữ liệu" → quay lại F4
   │
   ▼
[F6] 🔵 AI STEP — LLM soạn báo cáo nháp, dòng đầu là [DRAFT_ONLY],
     kèm đề xuất xếp loại A/B/C (chỉ là đề xuất, không phải kết luận)
   │
   ▼
[F7] 🟢 HUMAN — Kỹ sư sửa và ký                                  ⏱ 1,5 giờ
   │  🔄 H3
   ▼
[F8] 🟢 HUMAN — Trưởng ban duyệt; bắt buộc duyệt tay mọi tòa loại C ⏱ 1 giờ
   │  🔄 H4
   ▼
 Khối Kỹ thuật Vinhomes

🔵 AI Step   🟢 Human-in-the-loop   ⚙️ Rule   ↩️ Fallback
⏱ Tổng thời gian thao tác của người: ~6 giờ/tòa (mục tiêu)
```

### ↩️ Fallback

- **LLM lỗi, quá thời gian hoặc trả JSON sai định dạng:** hệ thống báo lỗi, kỹ sư dùng mẫu Excel cũ. Quy trình hiện tại vẫn chạy được, không bị phụ thuộc.
- **Trường có độ tin cậy thấp hoặc không có trích dẫn trang:** không được dùng; tự động thành một mục khảo sát ở F4.
- **Hồ sơ mâu thuẫn với hiện trường:** lấy số liệu hiện trường, ghi chú mâu thuẫn trong báo cáo.
- **Tách quyền:** rule engine chạy độc lập; LLM không thể đổi kết quả Đạt/Không đạt.

### Trước và sau

| Việc | Hiện tại (giờ) | Tương lai (giờ) | Phần bớt đi nhờ |
|---|---:|---:|---|
| Nhận yêu cầu, gom hồ sơ | 2,5 | 1 | Danh mục hồ sơ chuẩn; dữ liệu trụ lấy thẳng từ V-Green |
| Đọc hồ sơ, ghi số liệu | 2 | 0,5 | LLM trích, kỹ sư kiểm |
| Khảo sát hiện trường | 3 | 2 | Chỉ đo phần hồ sơ thiếu |
| Đối chiếu Thông tư 31 | 4 | 0 | Rule engine |
| Viết báo cáo | 3 | 1,5 | LLM soạn nháp, kỹ sư sửa |
| Duyệt | 1,5 | 1 | Báo cáo cùng một khuôn, ít bị trả về |
| **Tổng** | **16** | **6** | **−62%** |

---

# 🏁 Phase 5 — EVALUATE

### AI Readiness Checklist

1. [ ] **Có sẵn dữ liệu mẫu/logs sạch để test?** — **Chưa.** Thông tư 31 công khai nên bộ rule viết được ngay. Nhưng hồ sơ tòa nhà phần lớn là PDF scan và bản vẽ nằm ở nhiều nơi, và chưa có bộ báo cáo rà soát "chuẩn" để làm đáp án. → Tuần 1–2 của pilot: kỹ sư cao cấp rà soát tay 10 tòa để tạo bộ đáp án.
2. [x] **Rủi ro khi AI sai nằm trong tầm kiểm soát?** — **Có.** LLM không quyết Đạt/Không đạt (rule engine làm); thiếu dữ liệu không bao giờ thành "đạt"; kỹ sư ký từng báo cáo; quy trình cũ vẫn dùng được khi hệ thống lỗi.
3. [x] **Stakeholders sẵn sàng thay đổi?** — **Phần lớn có.** Hạn 15/6/2027 tạo áp lực thật; kỹ sư được bớt việc giấy tờ chứ không bị thay thế. Điều kiện: V-Green chia sẻ danh sách và công suất trụ theo từng tòa.

### Quyết định của Ban Giám Đốc Vin Smart Future

[x] **GO (scope hẹp)** — pilot 10 tòa trong một đại đô thị, 4 tuần
[ ] NOT YET
[ ] NO-GO

**Justification:**
- **Vì sao không NO-GO:** rule-based thuần không đọc được hồ sơ scan và ghi chú hiện trường, mà đó lại là chỗ tốn giờ nhất (B2, B5). Ngược lại, nhóm cũng không giao phần so ngưỡng cho LLM vì rule làm tốt hơn. Giải pháp là hybrid, không phải "AI cho mọi thứ".
- **Vì sao không NOT YET:** chờ có dữ liệu sạch nghĩa là chờ qua hạn 15/6/2027. Bộ dữ liệu còn thiếu (đáp án của 10 tòa) lại chính là sản phẩm phụ của pilot.
- **Vì sao chỉ scope hẹp:** mục 1 của checklist chưa đạt. Pilot chỉ gồm trích xuất và soạn nháp; AI không đụng tới khảo sát, duyệt hay nộp hồ sơ PCCC.
- **Chi phí:** chủ yếu là công dựng rule engine 14 hạng mục và một luồng trích xuất. Chi phí gọi LLM cho vài chục trang hồ sơ mỗi tòa nhỏ so với ~10 giờ công kỹ sư tiết kiệm mỗi tòa.
- **Điều kiện dừng sau pilot:** nếu bỏ sót bất kỳ hạng mục "không đạt" nào so với bộ đáp án, hoặc thời gian thao tác giảm dưới 40%, chuyển sang **NOT YET** và giữ lại rule engine + mẫu Excel chuẩn hóa (rule-only) làm phương án thay thế.

### Giả định cần kiểm

| Giả định | Cách kiểm |
|---|---|
| Thời gian từng bước (~16 giờ/tòa) | Bấm giờ 2 tòa đầu tiên của pilot |
| ~300 tòa trong phạm vi | Khối Kỹ thuật Vinhomes xác nhận danh mục |
| Hồ sơ thiết kế, PCCC còn lưu đủ | Kiểm trên 10 tòa pilot |
| V-Green chia sẻ dữ liệu trụ theo tòa | Thỏa thuận giữa Vinhomes và V-Green |

---

## Phụ lục A — 14 hạng mục rule engine kiểm

Tổng hợp từ nội dung Thông tư 31/2026 (theo VOV, 07/07/2026).

| # | Hạng mục | Quy tắc kiểm |
|---|---|---|
| 1 | Vị trí khu sạc | Ưu tiên: ngoài trời → mặt đất → bán hầm → hầm 1 → các hầm sâu hơn. Đặt từ hầm 2 trở xuống → cờ "cần giải trình" |
| 2 | Tách loại xe | Khu ô tô điện tách khỏi khu xe máy điện, xe đạp điện: có/không |
| 3 | Khoảng cách với khu khác | ≥ 2 m, hoặc có tường/vách không cháy cao ≥ 2 m |
| 4 | Quy mô phân vùng trong hầm, bán hầm | ≤ 25 chỗ sạc ô tô, hoặc ≤ 50 chỗ sạc xe máy/xe đạp điện |
| 5 | Quy mô phân vùng trên mặt đất | ≤ 30 chỗ sạc ô tô, hoặc ≤ 80 chỗ sạc xe máy/xe đạp điện |
| 6 | Công suất trụ trong hầm | ≤ 22 kW |
| 7 | Báo cháy tự động | có/không |
| 8 | Camera truyền về phòng trực 24/24 | có/không |
| 9 | Đèn chiếu sáng sự cố, biển chỉ dẫn thoát nạn | có/không |
| 10 | Thiết bị cảnh báo khí CO | có/không |
| 11 | Thiết bị cảnh báo khí HF | có/không |
| 12 | Tự ngắt điện khi bất thường + ngắt khẩn cấp | có/không |
| 13 | Vị trí khu đổi pin | Ngoài trời hoặc mặt đất; chỉ bán hầm/hầm 1 khi không còn phương án khác |
| 14 | Cô lập thiết bị đổi pin khi sự cố | Có giải pháp cô lập, xử lý hoặc di chuyển: có/không |

---

## Nguồn

- [VOV — Quy định mới: chung cư bắt buộc phải có khu vực riêng cho xe điện (07/07/2026)](https://vov.vn/o-to-xe-may/quy-dinh-moi-chung-cu-bat-buoc-phai-co-khu-vuc-rieng-cho-xe-dien-post1312811.vov)
- [LuatVietnam — Chung cư hiện hữu phải rà soát khu vực sạc xe điện, đổi pin trong 6 tháng](https://luatvietnam.vn/tin-van-ban-moi/tu-15-12-2026-chung-cu-hien-huu-phai-ra-soat-khu-vuc-sac-xe-dien-doi-pin-trong-vong-6-thang-186-109699-article.html)
- [Kinh tế Sài Gòn — Gỡ vướng sạc xe điện ở chung cư: chỉ ban quản lý thì chưa đủ (18/12/2025)](https://tuoitre.vn/saigontimes/go-vuong-sac-xe-dien-o-chung-cu-chi-ban-quan-ly-thi-chua-du-1061172019.htm)
- [VnEconomy — Công điện 27 và hạ tầng trạm sạc tại chung cư (02/04/2026)](https://vneconomy.vn/automotive/don-bay-phap-ly-xoa-bo-vung-trang-ve-ha-tang-tram-sac-tai-chung-cu.htm)
- [CafeF — Bổ sung trạm sạc xe điện tại chung cư: cần rà soát đồng bộ hạ tầng](https://cafef.vn/bo-sung-tram-sac-xe-dien-tai-chung-cu-can-ra-soat-dong-bo-ha-tang-188261030064700991.chn)
- [Vietbao — Vingroup báo lãi 43.700 tỷ đồng sau 9 tháng 2026](https://vietbao.vn/vingroup-bao-lai-43700-ty-dong-sau-9-thang-gap-gan-6-lan-cung-ky-613800.html)
- [Thư viện Pháp luật — Thông tin dự án Vinhomes Grand Park (71 tòa căn hộ)](https://thuvienphapluat.vn/phap-luat-nha-dat/gia-ban-nha-dat-du-an-vinhomes-grand-park-cap-nhat-moi-nhat-thang-102025-10552.html)
