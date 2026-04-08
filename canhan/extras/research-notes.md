# Research notes — Vietnam Airlines NEO

## Mục đích
Ghi lại các quan sát bổ sung dùng để hỗ trợ bài UX exercise và làm bằng chứng bonus. Đây là phần tóm tắt tinh hoa từ ngữ cảnh làm bài trước đó, không phải deliverable chính.

## Evidence files
- `neo-intent-drift-truck.png`: screenshot cho thấy user hỏi về "di chuyển bằng xe tải" nhưng bot hiểu lệch sang dịch vụ vận chuyển hàng hóa/trucking service.
- `neo-false-handoff.png`: screenshot cho thấy bot hứa kết nối nhân viên hỗ trợ, sau đó hiện `:(`, rồi quay lại xưng là NEO.
- Hai ảnh này nên đặt cùng thư mục `canhan/extras/` để đi kèm note này như bằng chứng trực tiếp.

## Những gì đã quan sát được từ hội thoại

### 1. FAQ rõ thì bot trả lời tương đối ổn
- Khi hỏi về hoàn tiền hoặc chưa nhận vé sau thanh toán, bot trả lời đúng domain.
- Bot đưa được checklist, hotline, email, link quản lý đặt chỗ và thời gian xử lý.

### 2. Câu hỏi mơ hồ thì bot không hỏi lại
- Câu: "Tôi là du khách nước ngoài, muốn hỏi cách di chuyển bằng xe tải".
- Bot không clarify intent.
- Bot hiểu "xe tải" theo hướng vận chuyển hàng hóa/trucking service, rồi trả lời lệch domain của nhu cầu thực tế.
- Evidence screenshot: `neo-intent-drift-truck.png`.

### 3. Handoff sang người thật bị mập mờ
- Bot nói sẽ kết nối nhân viên hỗ trợ.
- Sau đó có lúc chỉ hiện `:(`.
- Có lúc bot quay lại xưng là NEO.
- Có lúc lại xưng "chúng tôi là tư vấn viên".
- Vấn đề chính không phải chỉ là câu trả lời sai, mà là UI không xác minh được trạng thái hiện tại của cuộc trò chuyện.
- Evidence screenshot: `neo-false-handoff.png`.

### 4. Khi fail liên tiếp, hệ thống không cho lối thoát rõ
- User không biết nên chờ tiếp, gọi hotline, gửi email hay dừng.
- Không có ETA, không có hàng đợi, không có trạng thái "đã kết nối/chưa kết nối".

## Những gì học được từ FE source
- Chatbot đang nhúng dịch vụ live chat bên thứ ba qua `livechat.fpt.ai`.
- Trong source có app config hiển thị ở phía client như `FptAppCode`, `FptAppName`.
- Có dấu hiệu môi trường UAT/test xuất hiện trong code client-side như `VNA UAT Vietnam` và `test.vna.vn`.
- Có các endpoint tích hợp liên quan profile/session ở phía web tổng thể, cho thấy trải nghiệm chat và trải nghiệm đăng nhập/profile có liên hệ ở tầng UI.

## Ý nghĩa đối với bài UX
- Trust breakdown không chỉ đến từ model trả lời sai, mà đến từ cách sản phẩm biểu diễn vai trò và trạng thái.
- Khi hệ thống hứa chuyển người thật nhưng không chứng minh được, user mất niềm tin nhanh hơn nhiều so với một lỗi FAQ đơn thuần.
- Khoảng cách "marketing vs thực tế" nằm ở lời hứa hỗ trợ và handoff, không chỉ ở chất lượng câu trả lời.

## Kết luận ngắn
- `prompt1.md` và `chatbot` có thể bỏ khỏi bản nộp cuối.
- Nên giữ tinh hoa của chúng dưới dạng `extras/research-notes.md` hoặc `extras/security-note.md`.
- Không nên viết “lộ toàn bộ session” như một sự thật đã được chứng minh; nên viết là nghi ngờ/potential risk, còn bằng chứng chắc hiện tại là third-party livechat + UAT/test config exposed client-side + UX trust breakdown.
