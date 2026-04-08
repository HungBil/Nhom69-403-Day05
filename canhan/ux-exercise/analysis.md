# UX exercise — Vietnam Airlines NEO bot

## Sản phẩm: Vietnam Airlines — Chatbot NEO (Hỗ trợ khách hàng)

## 4 paths

### 1. Path 1: Khi AI đúng
- **Case:** User hỏi về hoàn tiền / vé chưa nhận được sau thanh toán (câu hỏi đúng FAQ).
- **Trải nghiệm:** Bot trả lời chuẩn xác, đưa ra checklist kiểm tra email, hướng dẫn liên hệ hỗ trợ, cung cấp link vào quản lý đặt chỗ, link hoàn vé và nêu rõ SLA là 15 ngày.
- **Nhận xét:** Bot hoạt động hiệu quả khi intent rõ ràng, đúng domain và gần với các câu hỏi FAQ. Hệ thống có bước hướng dẫn rõ ràng.

### 2. Path 2: Khi AI không chắc
- **Case:** User hỏi mơ hồ: "Tôi là du khách nước ngoài, muốn hỏi cách di chuyển bằng xe tải".
- **Trải nghiệm:** Bot không hỏi lại để làm rõ ý người dùng, mà đoán luôn intent. Bot hiểu "xe tải" theo nghĩa vận chuyển hàng hóa xuất nhập khẩu, tư vấn lệch sang dịch vụ vận chuyển xe tải châu Âu, lệch hoàn toàn domain (người dùng muốn biết cách di chuyển cho con người).
- **Vấn đề:** Trải nghiệm yếu vì không có bước clarify intent, không có boundary check (xem yêu cầu có thuộc hỗ trợ hàng không hay không). Nên hỏi lại: "Quý khách muốn hỏi vận chuyển hành khách hay hàng hóa?".

### 3. Path 3: Khi AI sai (Vấn đề nghiêm trọng nhất)
- **Case:** User muốn gặp người thật: "gọi nhân viên tới đây, tôi không muốn nói chuyện với AI".
- **Trải nghiệm:** Bot hứa hẹn "nhân viên hỗ trợ sẽ kết nối với quý khách trong giây lát". Nhưng thực tế:
  - Có lúc màn hình chỉ hiện emoji `:(`.
  - Có lúc lại tiếp tục xưng là "NEO rất vui...".
  - Có lúc bot tự nhận "Chúng tôi là tư vấn viên".
- **Vấn đề:** Không có bằng chứng rõ ràng chứng minh đã chuyển cho tư vấn viên (Handoff giả). Không có ETA chờ đợi, danh tính mập mờ, làm mất hoàn toàn niềm tin (trust) của user.

### 4. Path 4: Khi user mất niềm tin
- **Case:** Cuối hội thoại bot im lặng, mập mờ vai trò, user hỏi xác nhận nhiều lần nhưng không được đáp ứng.
- **Trải nghiệm:** Hệ thống không có "exit path" (lối thoát) sạch. User bị treo, không biết nên chờ tiếp, gọi hotline, gửi email hay mở ticket.
- **Vấn đề:** Khi NEO bot thất bại liên tiếp, hệ thống không cung cấp hướng giải quyết tiếp theo. User bối rối.

## Path yếu nhất: Path 3 (Handoff sang tư vấn viên) + Path 4 
- Lỗi nặng nhất là bot hứa chuyển cho con người nhưng UI không chứng minh được, sau đó im lặng hoặc giả danh người thật mập mờ. 
- Không có một cơ chế fallback / escape hatch rõ ràng khi AI hoàn toàn bế tắc.

## Nhận xét gap giữa marketing và thực tế sử dụng
- **Marketing:** NEO được quảng bá là trợ lý ảo thông minh, và trên trang web ghi rõ: "nếu NEO chưa giải đáp được thì sẽ chuyển hướng gặp tư vấn viên."
- **Thực tế:** Tính năng chuyển tư vấn viên (handoff) không hoạt động ổn định. Trong hội thoại thực tế, bot hứa sẽ có nhân viên hỗ trợ nhưng sau đó lại quay về xưng là NEO hoặc xưng mập mờ là "chúng tôi là tư vấn viên", khiến user không biết mình đang nói chuyện với AI hay con người.
- **Quan sát bổ sung từ FE source:** chatbot có dấu hiệu nhúng live chat bên thứ ba và để lộ cấu hình client-side mang nhãn UAT/test. Đây chưa đủ để kết luận có lỗ hổng chiếm quyền session, nhưng làm tăng cảm giác thiếu minh bạch và làm khoảng cách giữa lời hứa marketing với trải nghiệm thực tế càng lớn hơn.

## Sketch (As-is & To-be)
**(Xem ảnh đính kèm: `sketch.jpg`)**

- **As-is (hiện tại):**
  - User: "Gọi nhân viên tới đây" -> Bot hứa kết nối.
  - Sau đó không rõ ai đang trả lời, màn chat trống hoặc hiển thị `:(`.
  - Bot nhầm vai trò, lúc xưng là NEO lúc xưng là tư vấn viên, user không xác định được trạng thái chờ.
- **To-be (đề xuất):**
  - Khi user yêu cầu gặp nhân viên, xuất hiện System card hiện rõ tình trạng: "Có sẵn/Không có sẵn tư vấn viên, thời gian chờ dự kiến 2 phút".
  - Header chat hiển thị Badge rõ ràng: `Đang chat với: NEO` hoặc `Đã kết nối với tư vấn viên: Lan`. Hiển thị vị trí trong hàng đợi (Ví dụ: Bạn đang ở vị trí số 3).
  - Nếu thất bại/Không có tư vấn trực tuyến: Cung cấp 3 lối thoát (Escape Hatch) rõ ràng qua nút bấm: Gọi Hotline 1900 1100, Gửi Email, hoặc Để lại yêu cầu. Không để user tình trạng "treo".

## Data Flywheel — Đề xuất cho NEO

![Data Flywheel — NEO Chatbot](NEO%20Data%20Flywheel.png)

**Hiện tại (NEO):** Không có vòng lặp này. User bỏ chat, chấm 1 sao, gọi hotline — nhưng hệ thống không ghi nhận và phân tích. AI không biết mình đã sai → không cải thiện → vòng xoáy đi xuống (Alexa Effect).

**Đề xuất:** Thu signal từ hành vi thực tế của user (bấm escalate, chấm sao thấp, hỏi lại cùng câu, dropout) → phân tích câu nào / session nào thất bại → cải thiện model → bot tốt hơn → user tin dùng → nhiều data hơn → vòng lặp tự củng cố.

---

## Ghi chú extras
- Các ghi chú nghiên cứu và ghi chú security/technical evidence được tách riêng ở thư mục `canhan/extras/`.
- Mục đích của phần extras là bổ sung bằng chứng và bonus, không thay cho bài chính `analysis.md`.
