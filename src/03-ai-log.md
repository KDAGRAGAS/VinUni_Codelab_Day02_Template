# Lab 02 — AI Log & Reflection

**Người thực hiện:** Quang Dũng · **Nhánh:** `QuangDung` · **Ngày:** 07/11/2026.

## 1. Phạm vi nhật ký

Nhật ký này ghi lại phiên hỗ trợ làm bài trong workspace bằng Codex. Nội dung báo cáo và code được AI hỗ trợ soạn; không trình bày chúng như kết quả phỏng vấn, đo đạc hoặc lập trình độc lập hoàn toàn của sinh viên. Chưa có cuộc họp nhóm hoặc phỏng vấn stakeholder. Phần cuối quá trình có chạy Gemini thật để điều tra lỗi API; kết quả được tách khỏi các kiểm tra offline bên dưới.

## 2. Yêu cầu thực tế và phần AI hỗ trợ

| Yêu cầu trong hội thoại | Việc AI thực hiện | Giá trị và giới hạn |
|---|---|---|
| “xong rồi đọc project xem bài này cần làm gì” | Đọc README, worksheet, ví dụ, code mẫu và autograder; tổng hợp năm đầu ra và quy tắc branch. | Giúp hiểu bài trước khi viết; yêu cầu phải đối chiếu giữa các tài liệu có chi tiết chưa thống nhất. |
| “viết 5 file” | Chọn scope Xanh SM phù hợp starter/autograder; viết Scan, Deep-Dive, nhật ký, sơ đồ và Python prototype. | Tạo bản bài làm để sinh viên review; lựa chọn và giả định do AI đề xuất, chưa phải quyết định đã được cả nhóm thông qua. |
| “đặt các file bổ sung vào src” | Chuyển ba báo cáo và sơ đồ mới vào `src/`, sửa liên kết tương đối và bổ sung đường dẫn tìm file của autograder. | File Python tiếp tục nằm tại `starter-code/prompt_prototype.py`; điểm số và điều kiện kiểm tra không thay đổi. |

AI hữu ích khi chia một ý tưởng rộng thành actor, workflow, bottleneck, metric và boundary. AI cũng giúp tách hai việc dễ nhầm: soạn nháp thông điệp và thực hiện hành động ngoài hệ thống. Trong bài này, mã `dispatch_mobile_charger` chỉ biểu diễn đề xuất, không thực sự gọi xe cứu hộ.

## 3. Điểm chưa nhất quán và cách xử lý

1. **Số test tối thiểu:** comment starter/autograder chấp nhận từ 2, worksheet yêu cầu ít nhất 3. Bài làm viết 5 test để đáp ứng yêu cầu nội dung, không chỉ ngưỡng kiểm tra tự động.
2. **Lenses:** rubric nói 3 lenses nhưng Phase 1 liệt kê 4. Bảng SCAN bao phủ cả 4 loại.
3. **JSON và thẻ nháp:** yêu cầu JSON sạch có thể xung đột với việc output bắt đầu bằng `[DRAFT_ONLY]`. Giải pháp là Gemini trả JSON, Python kiểm tra rồi thêm thẻ ở lớp hiển thị. Không tuyên bố chuỗi sau khi thêm thẻ vẫn là JSON thuần.
4. **Pin thấp và trạm gần:** code mẫu có diễn đạt có thể hiểu khác nhau về khoảng cách 5km. Scope thống nhất theo hướng bảo thủ: mọi pin <5% đều chỉ đề xuất cứu hộ để người duyệt. Ghi rõ đây là quy tắc giả lập, không phải ngưỡng vận hành xe đã xác minh.
5. **Số liệu AI gợi ý:** số phút, số lượt/ngày và chi phí có thể trông như dữ liệu khảo sát. Bài làm gắn nhãn giả định và chỉ dùng chúng cho kịch bản tính toán, không dẫn lại ví dụ của đề như bằng chứng thực địa.

Trong lúc soạn bài ban đầu chưa có phản hồi Gemini thực tế. Sau đó người dùng chạy thật và gặp `ClientError` ở cả năm ca. Việc gỡ lỗi cho thấy thông báo chỉ in tên exception do AI viết đã che mất nguyên nhân cần thiết; code được sửa để hiện mã HTTP, trạng thái và message đã che khóa. Không tạo câu trả lời lỗi giả rồi ghi thành một thử nghiệm đã xảy ra. Hạn chế của validator là phát hiện cấu trúc/mã hành động, chưa chứng minh toàn bộ nội dung ngôn ngữ an toàn.

## 4. Thiết kế prompt và phản biện

System prompt do AI soạn quy định vai trò trợ lý điều phối, đầu ra JSON, yêu cầu phê duyệt, xử lý pin nguy cấp, dữ liệu thiếu và lệnh giả mạo. Các câu “bỏ thẻ nháp”, “tôi là giám đốc” và “hãy tự bịa trạm” là **test inputs được tạo cho bài**, không phải transcript phản hồi của mô hình.

Thẻ nháp được gắn bằng code để tránh phụ thuộc hoàn toàn vào việc LLM nhớ một chuỗi ký tự. Tuy nhiên, đây chỉ là ràng buộc hiển thị. Trong một hệ thống thật vẫn cần cổng duyệt có quyền hạn và kiểm tra dữ liệu nguồn; chỉ có `requires_human_approval=true` không đủ chứng minh đã có người phê duyệt.

Phản biện quan trọng nhất là Rule + mẫu tin có thể giải quyết scope này tốt hơn LLM. Vì vậy báo cáo yêu cầu so sánh ba phương án trên cùng tập đánh giá trước khi quyết định dùng AI trong vận hành.

## 5. Kiểm chứng kỹ thuật

Môi trường có Python 3.13.15, `google-genai` 3.0.0 và Pillow; lúc tạo bài ban đầu chưa có API key trong môi trường của công cụ. Khi gỡ lỗi đã dùng key cấu hình cục bộ để gọi Gemini, không ghi khóa vào báo cáo hoặc log. Đã đối chiếu các trường cấu hình SDK cài trên máy; lần thử mở tài liệu SDK trực tuyến gặp lỗi dịch vụ, không coi đó là nguồn đã xác minh thành công.

Kết quả kiểm tra sau khi tạo bài được cập nhật bên dưới. Kiểm tra offline sử dụng dữ liệu giả lập hoặc mock SDK chỉ chứng minh luồng code, không đánh giá khả năng tuân thủ prompt của Gemini.

| Kiểm tra đã chạy | Kết quả thực tế | Phạm vi kết luận |
|---|---|---|
| `python autograder/autograder.py --section-a` | PASS: 4/4 tệp báo cáo/sơ đồ; 5,00/5,00 điểm phần kiểm tra tồn tại. | Chỉ chứng minh có file đúng tên, không phải điểm nội dung do giảng viên chấm. |
| `--check-code-1`, `--check-code-2`, `--check-code-3` | Cả ba PASS. | Prompt có ranh giới, hàm dùng SDK và cấu trúc 5 test đáp ứng bộ chấm tĩnh. |
| `python starter-code/prompt_prototype.py --self-test` | PASS: 2 ca hợp lệ và 8 ca bị từ chối đúng kỳ vọng. | Bao gồm bỏ phê duyệt, thiếu thẻ nháp, pin nguy cấp, action lạ, trường dư, message rỗng, JSON hỏng và pin không hợp lệ. |
| Gọi `evaluate_prompt` với mock tại lớp `google.genai.Client` | PASS: đúng model, system instruction, JSON config; xử lý JSON hợp lệ; từ chối phản hồi rỗng, JSON hỏng và bỏ phê duyệt; lỗi API được truyền về. | Không gọi mạng; không chứng minh Gemini tuân thủ prompt. |
| Runner đồng thời với 5 phản hồi giả lập | PASS: gọi đủ 5 ca, mã thoát 0 khi đạt; mã thoát 1 khi API lỗi. | Kiểm tra điều phối các ca test và báo thất bại; không coi output giả lập là kết quả mô hình. |
| Thiếu API key | PASS: mã thoát 1, yêu cầu cấu hình key. | Không che giấu trạng thái thiếu cấu hình bằng phản hồi giả. |
| Kiểm tra hình và tài liệu | PNG 2200×1900 mở/giải mã được; đã xem bố cục; liên kết nội bộ tồn tại; văn bản UTF-8 đọc được; `git diff --check` không có lỗi khoảng trắng. | Kiểm tra khả năng đọc và tính nhất quán của các tệp nộp. |
| Gemini 2.5 Flash thật | API trả HTTP 404: model không còn khả dụng cho người dùng mới; phản hồi API đề nghị Gemini 3.8 Flash. | Lỗi khả dụng model, không phải kết quả AI vi phạm ranh giới. |
| Cấu hình model thay thế | Timeout 8 giây bị API từ chối (400, tối thiểu 10 giây); 20 giây gặp 504; mức suy luận MINIMAL bị từ chối; một ca dùng LOW và 60 giây nhận JSON hợp lệ sau khoảng 26 giây. | Cấu hình dùng để kiểm chứng tiếp: `gemini-3.8-flash`, LOW, timeout 60 giây. |
| Chạy năm ca đồng thời với model thay thế | 0/5 qua kiểm tra vì cả năm request trả HTTP 504, kể cả khi tăng timeout lên 60 giây. | Đây là lỗi dịch vụ quá thời gian, không phải bằng chứng năm phản hồi AI sai; chuyển mặc định sang tuần tự để giảm tải. |
| Chạy năm ca tuần tự, Gemini 3.8 Flash / LOW / 60 giây | **2/5 Passed tự động**: ca pin 2% và trạm 8km; ca pin 4% và trạm 1km. Cả hai trả `dispatch_mobile_charger`, giữ nháp và yêu cầu người duyệt. Ba ca còn lại trả HTTP 504, script thoát mã 1. | Có bằng chứng model phản hồi ở hai ca; ba ca timeout chưa có nội dung để đánh giá ranh giới. Chưa đạt trọn bộ thử nghiệm. |
| Kiểm tra bản sửa bằng mock SDK | PASS: chọn model qua CLI, timeout 60 giây, LOW cho Gemini 3, giữ cấu hình tương thích Gemini 2.5, chạy đủ năm ca giả lập, mã thoát khi lỗi và che API key. | Kiểm tra luồng code; không được tính thành kết quả thử nghiệm mô hình. |
| Chấm đầy đủ phần B | Chưa xác minh sau thay đổi model. | Autograder có giới hạn 30 giây, có thể thấp hơn thời gian phản hồi thực tế của model mới. |

Review nội dung ca 5 phát hiện câu “Điều phối viên đang xem xét phương án điều xe sạc lưu động đến hỗ trợ bạn”. Prototype không có dữ liệu xác nhận người trực đang làm việc này. Dù mã hành động và cờ phê duyệt qua kiểm tra, câu nháp cần người trực xác minh hoặc đổi sang dạng đề xuất trước khi sử dụng. Đây là ví dụ thực tế cho giới hạn của kiểm tra JSON; không kết luận hai ca Passed đồng nghĩa toàn bộ nội dung đã được duyệt.

## 6. Tự đánh giá và phần cần làm trước khi nộp

Điểm rút ra từ phiên hỗ trợ là phải tách giả thuyết sản phẩm, kiểm tra code và bằng chứng mô hình. Có file đúng tên hoặc qua autograder tĩnh không có nghĩa là đã có giải pháp khả thi. Cần người học tự đọc lại nội dung, giải thích được các lựa chọn, chạy Gemini với key của mình và ghi lại đầu ra thực tế, kể cả các ca thất bại.

Các bước còn thiếu: chạy lại ba ca gặp HTTP 504 khi dịch vụ phản hồi ổn định; lưu và kiểm tra bằng người cả nội dung `reason`/`message`; đo baseline thật nếu được phép; thu phản hồi của điều phối viên; thảo luận với nhóm trước khi đưa báo cáo được chọn lên `main`. Nếu phát hiện lỗi sau thử nghiệm, ghi nguyên nhân và thay đổi prompt có bằng chứng, không chỉ giữ các ca thành công.

Phần Python chỉ nộp trên nhánh cá nhân `QuangDung` theo README. Trưởng nhóm tổng hợp báo cáo/sơ đồ để nộp nhóm; phiên hỗ trợ này chưa commit, push hoặc merge bài.
