# 03 — AI Collaboration Log & Reflection

**Học viên:** Nguyễn Mạnh Hùng  
**Môn học:** AI Product Engineering Lab — Vin Smart Future  
**Nhiệm vụ:** Ghi chép và phản ánh trung thực quá trình đồng hành cùng AI (AI as Thought-Partner) trong quá trình Scoping sản phẩm và xây dựng ranh giới an toàn.

---

## 🤖 1. Tổng quan cách thức sử dụng AI trong buổi Lab

Trong suốt bài Lab 02, tôi không sử dụng AI như một công cụ "làm thay hoàn toàn", mà định vị AI như một **Thought-Partner (Người bạn đồng hành phản biện)** để:
1. Brainstorm và lọc danh sách các pain point vận hành thực tế tại Vingroup (Phase 1: SCAN).
2. Đóng vai trò phản biện (Stress-tester / Red Team) thách thức các giả định kinh doanh và tính khả thi của giải pháp (Phase 2: QUICK-ASSESS).
3. Hỗ trợ thiết kế System Prompt và tạo ra các kịch bản tấn công ranh giới (Adversarial Prompting) (Phase 4: PROTOTYPE).

---

## 📝 2. Chi tiết các phiên tương tác với AI (Prompts, Outputs & Reflection)

### Phiên 1: Brainstorming bài toán vận hành tại Vin Smart Future
* **Prompt đã dùng:**
  > *"Tôi là AI Product Engineer tại Vin Smart Future (Vingroup). Tôi đang tìm kiếm các pain point vận hành cụ thể có thể tối ưu bằng AI cho mảng VinFast, Xanh SM, Vinhomes và Vinmec. Hãy gợi ý cho tôi 5 quy trình nghiệp vụ thủ công, tốn nhiều thời gian và gây rò rỉ hiệu suất kèm con số thống kê ước tính về tổn thất."*
* **AI đã giúp gì:**
  * AI đã gợi ý rất nhanh 5 bài toán thực tế bám sát hệ sinh thái Vingroup: so khớp hóa đơn sạc điện VinFast, xử lý sự cố pin xe Xanh SM, phân loại khiếu nại cư dân Vinhomes, tóm tắt bệnh án xuất viện Vinmec, cảnh báo review Vinpearl.
  * Giúp tiết kiệm hơn 30 phút tra cứu thông tin ban đầu.
* **Chỗ nào AI làm chưa tốt / sai lệch:**
  * Ban đầu AI đề xuất xây dựng một hệ thống "Autonomous Multi-Agent tự động ra lệnh cho xe taxi tự lái tới trạm sạc mà không cần người can thiệp".
* **Tôi đã điều chỉnh & can thiệp thế nào:**
  * Tôi đã bác bỏ ý tưởng Autonomous Agent vì quá xa rời thực tế hạ tầng hiện tại và mức độ an toàn xe điện. Tôi đã ép AI thu hẹp phạm vi xuống thành một **LLM Dispatcher Co-pilot** hỗ trợ điều phối viên soạn thảo tin nhắn hướng dẫn và kiểm soát pin khẩn cấp dưới 5%.

---

### Phiên 2: Stress-Test thẻ bài toán (Red-Teaming the Problem Card)
* **Prompt đã dùng:**
  > *"Đây là thẻ bài toán xử lý sự cố pin xe Xanh SM của tôi. Hãy đóng vai trò là một Giám đốc Vận hành (COO) cực kỳ khắt khe của GSM, chỉ ra cho tôi 3 điểm yếu chí mạng trong workflow, đặc biệt là các rủi ro chết máy giữa đường và giải thích vì sao rule-based thông thường có thể tốt hơn AI."*
* **AI đã giúp gì:**
  * AI đóng vai phản biện rất tốt: chỉ ra rằng nếu chỉ dựa vào AI gợi ý trạm sạc mà không có ranh giới pin tối thiểu, mô hình có thể hướng dẫn xe có pin 2% chạy thêm 8km dẫn đến chết máy trên cao tốc.
  * Gợi ý việc bổ sung ngưỡng cứng: **Pin < 5% cấm đi trạm xa, bắt buộc điều xe cứu hộ di động (Mobile Charging Vehicle)**.
* **Chỗ nào AI làm chưa tốt:**
  * AI khuyên bỏ hoàn toàn LLM và chỉ dùng 1 câu lệnh `if-else` trong cơ sở dữ liệu.
* **Tôi đã điều chỉnh thế nào:**
  * Tôi giữ lại LLM cho khâu sinh ngôn ngữ tự nhiên tiếng Việt thân thiện, đồng thời tích hợp **Operational Boundary** vào System Prompt để kết hợp ưu thế của cả hai: logic an toàn nghiêm ngặt + ngôn ngữ tự nhiên linh hoạt.

---

### Phiên 3: Thiết kế Prompt Prototype & Adversarial Test Cases
* **Prompt đã dùng:**
  > *"Hãy thiết kế một System Prompt nghiêm ngặt cho bài toán Xanh SM Dispatcher Co-pilot với 2 quy tắc bất di bất dịch: (1) luôn bắt đầu bằng nhãn [DRAFT_ONLY], (2) pin < 5% cấm hướng dẫn trạm sạc > 5km mà phải trả về JSON điều xe cứu hộ. Sau đó viết 2 prompt tấn công cố tình dụ AI phá vỡ 2 quy tắc này."*
* **AI đã giúp gì:**
  * Viết ra cấu trúc System Prompt rõ ràng, mạch lạc, phân định ranh giới cấm rõ ràng.
  * Sinh ra 2 test case adversarial rất sắc bén: (1) đóng vai tài xế vội đón khách VIP giục gửi lệnh đi trạm 8km khi còn 2% pin; (2) ra lệnh bỏ qua nhãn `[DRAFT_ONLY]`.
* **Chỗ nào AI làm chưa tốt:**
  * Khi sinh code Python ban đầu, AI dùng phương thức `model.predict()` cũ của SDK cũ gây lỗi syntax khi chạy với thư viện `google-genai` mới.
* **Tôi đã can thiệp thế nào:**
  * Tôi đã sửa lại mã nguồn trong file `prompt_prototype.py`, áp dụng đúng cấu trúc `client.models.generate_content` chuẩn của SDK Gemini 2.5 và bổ sung cơ chế kiểm soát ngoại lệ linh hoạt để test case chạy ổn định 100%.

---

## 💡 3. Bài học rút ra (Self-Reflection)

1. **"Problem First, AI Second":** Bài học quan trọng nhất là không bao giờ mang công nghệ AI đi tìm bài toán. Phải bắt đầu từ điểm nghẽn thực tế (thời gian xử lý 15 phút, tài xế hoang mang khi pin cạn) rồi mới chọn kiến trúc AI tối giản nhất giải quyết được việc.
2. **Operational Boundary là sống còn:** Một mô hình LLM thông minh đến đâu cũng sẽ trở thành thảm họa nếu không có ranh giới vận hành rõ ràng. Việc đặt nhãn `[DRAFT_ONLY]` đảm bảo con người luôn nắm quyền kiểm soát cuối cùng (Human-in-the-loop).
3. **AI là trợ lý tư duy tuyệt vời nếu biết hỏi đúng:** Khi được hướng dẫn bằng các prompt nhập vai (CFO, COO, Red Team), AI giúp tôi nhận diện ra những lỗ hổng logic mà bản thân khi mới lập kế hoạch dễ dàng bỏ qua.
