# Lab 02 — AI Log & Reflection (`03-ai-log.md`)

## 1. Tôi đã dùng AI để hỗ trợ những việc gì trong bài Lab?

* **Mở rộng danh sách 5 bài toán ở Phase 1 (SCAN):** Ban đầu tôi đã chọn sẵn đề tài chính cho phần báo cáo là bài toán **phân bổ vị trí lắp đặt trạm sạc của VinFast/V-GREEN** (vì thực tế có nhiều chỗ lắp trạm xong rất vắng người dùng, trong khi các điểm trung tâm lại quá tải). Tôi dùng AI để gợi ý thêm 4 bài toán phụ ở các mảng khác (Xanh SM, Vinhomes, Vinmec, Vinpearl) để hoàn thiện bảng SCAN 5 bài toán và làm 2 thẻ Quick Cards phụ.
* **Phản biện lại cách đặt vấn đề (Stress-Test ở Phase 2):** Khi tôi đưa ý tưởng "dùng AI chọn vị trí lắp trạm sạc" vào hỏi thử, tôi nhờ AI đóng vai người chấm bài khó tính để bắt lỗi. Nhờ bước này tôi mới nhận ra: nếu chỉ nói chung chung là *"dùng AI tính toán vị trí đặt trạm sạc dựa trên số lượng xe"* thì người chấm sẽ hỏi ngược lại ngay: *"Tại sao không dùng hàm thống kê hay câu lệnh SQL thông thường mà phải dùng tới LLM?"*.
* **Hỗ trợ hoàn thiện bài tập code cá nhân ở Phase 4 (`prompt_prototype.py`):** Đối với file bài tập code mẫu của môn học (kịch bản trợ lý điều phối Xanh SM kiểm soát thẻ `[DRAFT_ONLY]` và xử lý pin yếu `< 5%`), tôi dùng AI để tra nhanh cú pháp gọi model `gemini-2.5-flash` bằng thư viện `google-genai` và thiết kế thêm câu prompt tấn công (Adversarial Test).

---

## 2. Những chỗ AI trả lời sai / chưa hợp lý và cách tôi đã tự sửa lại

Trong quá trình làm, có 3 điểm AI gợi ý chưa sát thực tế hoặc làm quá tay mà tôi phải tự chỉnh lại:

### 1) AI đề xuất giải pháp quá phức tạp (Over-engineering với Multi-Agent)
* **Vấn đề:** Ở những câu trả lời đầu tiên cho bài toán trạm sạc, AI gợi ý xây dựng hẳn một hệ thống *Autonomous Multi-Agent* tự động phân tích dữ liệu trạm sạc, tự chốt vị trí mặt bằng mới và tự động phát lệnh điều chuyển trụ sạc mà không cần con người can thiệp.
* **Cách tôi sửa:** Cách làm tự động 100% này không thực tế vì việc lắp mới hay di dời một trạm sạc tốn rất nhiều chi phí đầu tư (CAPEX), liên quan đến hợp đồng thuê mặt bằng và hạ tầng điện 3 pha. Do đó tôi đã hạ kiến trúc xuống thành **LLM Feature** kết hợp **Human-in-the-loop**: AI chỉ làm nhiệm vụ đọc tổng hợp dữ liệu và soạn bản nháp (`[DRAFT_ONLY]`), còn người ra quyết định cuối cùng bắt buộc phải là Chuyên viên Quy hoạch.

### 2) Nhầm lẫn giữa việc của SQL/Rule-based và việc của LLM
* **Vấn đề:** Bản nháp quy trình đầu tiên do AI viết giao luôn việc *"tính toán hiệu suất trạm sạc `<15%` hay `>85%` và tính khoảng cách GPS"* cho LLM xử lý.
* **Cách tôi sửa:** LLM không phù hợp để làm các phép tính thống kê bảng biểu — việc lọc ra trạm nào vắng khách (`<15%`) hay quá tải (`>85%`) dùng SQL/Rule-based vừa chính xác 100% vừa nhanh. Tôi đã sửa lại quy trình trong `02-deep-dive-report.md`: để Rule-based lọc số liệu trước, còn **LLM chỉ nhảy vào xử lý phần văn bản tự do** mà SQL không làm được (đó là đọc hàng trăm tin nhắn phàn nàn của tài xế để biết *tại sao* trạm đó bị né tránh, và đọc các file Word/PDF biên bản khảo sát mặt bằng mới).

### 3) Trộn lẫn kịch bản của bài tập code mẫu vào báo cáo đề tài riêng
* **Vấn đề:** Vì thấy file code mẫu `starter-code/prompt_prototype.py` có quy tắc *"xe còn dưới 5% pin thì gọi xe cứu hộ sạc lưu động (`dispatch_mobile_charger`)"*, lúc đầu AI đã ghép luôn chi tiết gọi xe cứu hộ pin `< 5%` này vào trong quy trình của Chuyên viên Quy hoạch vị trí lắp đặt trạm sạc ở file `02-deep-dive-report.md`.
* **Cách tôi sửa:** Khi đọc lại tôi phát hiện chỗ này không hợp lý (vì người làm quy hoạch đi khảo sát mặt bằng lắp trạm sạc thì không làm công việc trực tổng đài cứu hộ xe hết pin giữa đường). Tôi đã tách bạch rõ: báo cáo `02-deep-dive-report.md` và sơ đồ `04-workflow-diagram.png` chỉ tập trung 100% vào quy trình thẩm định mặt bằng & điều chuyển trụ sạc cố định (kiểm tra công suất trạm biến áp 3 pha, điều kiện PCCC); còn quy tắc cứu hộ pin `< 5%` giữ đúng ở file bài tập code `starter-code/prompt_prototype.py`.

---

## 3. Bài học rút ra sau buổi Lab

* Không phải bài toán nào cũng nên đẩy hết cho AI tự quyết định; những chỗ tính toán số liệu rõ ràng thì nên dùng Rule-based, chỉ dùng LLM ở bước cần đọc hiểu và tóm tắt văn bản phi cấu trúc.
* Khi dùng AI hỗ trợ viết tài liệu thiết kế sản phẩm, mình phải nắm rõ nghiệp vụ thực tế của người vận hành (Actor) để phát hiện ngay những chi tiết AI tự ghép nối một cách máy móc hoặc không đúng vai trò.
