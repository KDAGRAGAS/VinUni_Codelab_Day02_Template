# 03 — AI Log & Reflection

**Họ tên:** ____________ · **Branch:** ____________ · **Ngày:** 06/11/2026
**Công cụ AI:** Claude (Anthropic), dùng trong ứng dụng Claude

---

## 1. Tôi đã dùng AI vào việc gì

| # | Tôi yêu cầu | AI làm | Tôi giữ hay bỏ |
|---|---|---|---|
| 1 | "Research Vingroup, chọn 1 vấn đề ảnh hưởng lớn, xác định actor, workflow, bottleneck" | Đề xuất bài toán VinFast thua lỗ kéo dài và gánh nặng vốn lên tập đoàn | **Bỏ** — quá rộng, không phải một quy trình vận hành |
| 2 | "Vấn đề khác đi" | Tìm tin Vingroup 2026, đưa 4 lựa chọn: giải phóng mặt bằng siêu dự án, nợ vay, Xanh SM, vận hành khu đô thị Vinhomes | **Bỏ cả 4** — vẫn còn rộng |
| 3 | "Cần vấn đề nhỏ hơn nhưng chất" | Chọn bài sạc xe điện trong chung cư Vinhomes theo Thông tư 31; viết tài liệu có bản đồ actor, sơ đồ 8 bước, 6 điểm nghẽn, lộ trình | **Giữ** phần bối cảnh và actor |
| 4 | "Đọc xem cần phải làm gì, tôi là trưởng nhóm" | Đọc README, worksheet, autograder; tóm tắt việc của trưởng nhóm và các bẫy chấm điểm | **Giữ** |
| 5 | "Tạo nội dung cho các file md" | Soạn bản nháp đầu cho 01, 02, 03 | Bản nháp — tôi đọc lại, sửa giọng văn và kiểm số liệu trước khi nộp |

---

## 2. AI đã giúp được gì

- **Tìm và đọc nguồn nhanh.** Trong vài phút có đủ dữ kiện về Thông tư 31/2026 (hiệu lực 15/12/2026, hạn rà soát 6 tháng, trụ trong hầm ≤ 22 kW, mỗi phân vùng ≤ 50 chỗ sạc xe máy, đầu báo khí CO/HF), Công điện 27, mức phạt theo Nghị định 106/2025 và số xe VinFast đã giao.
- **Cấu trúc hóa.** Biến một chủ đề rối (xe điện + chung cư + PCCC + cư dân) thành actor, workflow và bottleneck rõ ràng.
- **Đọc đề kỹ hơn tôi.** AI chỉ ra autograder chấm file `.py` bằng từ khóa của bài mẫu Xanh SM (`DRAFT_ONLY`, `5%`, `dispatch_mobile_charger`) và đếm chữ "Failed" trong output. Không biết điều này thì dễ mất điểm code dù prompt viết tốt.
- **Phản biện thiết kế.** AI gợi ý tách phần so ngưỡng quy chuẩn cho rule engine thay vì để LLM làm hết — đây thành điểm chính của phần AI Fit.

---

## 3. AI sai hoặc suýt sai ở đâu

1. **Chọn vấn đề quá to.** Lần đầu AI đưa bài toán cấp tập đoàn (tài chính VinFast); lần hai vẫn còn những bài như nợ vay, giải phóng mặt bằng. Đúng là "ảnh hưởng lớn", nhưng không thể scoping trong một buổi lab và AI cũng không giải được. Tôi phải kéo lại hai lần.
2. **Điểm nghẽn đúng nhưng AI không giải được.** Phân tích đầu tiên kết luận điểm nghẽn là khâu phê duyệt: cư dân biểu quyết và cơ quan PCCC thẩm duyệt. Điều này hợp lý ngoài thực tế, nhưng đó là việc của con người và cơ quan nhà nước, không khớp yêu cầu "AI Fit" của lab. Nhóm phải thu hẹp vào bước rà soát hồ sơ — nơi có nhiều văn bản để đọc và viết.
3. **Nguồn sai thời điểm.** Kết quả tìm kiếm có bài "gần 9.000 căn Vinhomes Grand Park chưa nộp hồ sơ cấp sổ hồng" trông như tin mới. Mở trang ra thì bài đăng ngày 21/4/2024. AI đã tự kiểm ngày và không dùng, nhưng nếu chỉ đọc đoạn trích thì đã dùng nhầm số liệu cũ hơn hai năm.
4. **Số liệu "trông như thật" nhưng là ước tính.** Thời gian từng bước (16 giờ/tòa, 4 giờ gom hồ sơ...) và giả định 300 tòa là con số AI đề xuất, không phải đo thực tế. Đây là kiểu hallucination nguy hiểm nhất vì nó rất hợp lý. Tôi yêu cầu ghi rõ "ước tính" ở mọi chỗ và đưa vào bảng giả định cần kiểm.
5. **Nguồn có thể đã cũ.** Vài nguồn về trạm sạc trong hầm các tòa Vinhomes là bài blog 2022–2023, chưa chắc còn đúng hiện trạng.

---

## 4. Tôi đã sửa prompt và đặt ranh giới thế nào

- **Thu hẹp phạm vi bằng ràng buộc cụ thể:** "nhỏ hơn nhưng chất" — một quy trình, một nhóm người làm, một hạn chót.
- **Bắt buộc có nguồn:** mỗi con số phải đi kèm link trang đã mở, không dùng đoạn trích của công cụ tìm kiếm.
- **Tách hai loại thông tin:** dữ kiện có nguồn và ước tính của nhóm phải được ghi rõ ràng là khác nhau.
- **Giữ vai trò:** AI tìm tài liệu và soạn nháp; quyết định chọn bài toán và GO / NOT YET / NO-GO là của nhóm.
- **Áp chính tư duy "ranh giới" của lab vào cách dùng AI:** AI chỉ tạo bản nháp, người kiểm và chịu trách nhiệm.

---

## 5. Bài học

- AI mạnh nhất ở tìm nguồn công khai và dựng khung phân tích; yếu nhất ở số liệu vận hành nội bộ — thứ chỉ có được bằng cách hỏi người làm thật.
- Lần sau tôi sẽ phỏng vấn một kỹ sư tòa nhà 15 phút trước khi để AI ước tính thời gian từng bước.
- "Problem first, AI second": hai lần đầu AI đi từ "vấn đề to" thay vì từ "một quy trình cụ thể". Người dùng phải là người kéo lại.
