# Ghi chú bổ sung — trust và security concern

## Quan sát từ FE source
- Chatbot NEO đang nhúng dịch vụ live chat bên thứ ba qua `livechat.fpt.ai`.
- Trong source phía client xuất hiện các cấu hình như app code, app name, socket URL, cùng dấu hiệu môi trường UAT/test.
- Phần web tổng thể cũng có dấu hiệu gắn với trạng thái phiên làm việc và profile, ví dụ popup "phiên làm việc đã hết hạn" và endpoint profile.

## Nhận xét
- Việc người dùng xem được source FE bằng DevTools là hành vi bình thường của web app, không tự nó là lỗ hổng.
- Tuy nhiên, việc chatbot phụ thuộc dịch vụ bên thứ ba, để lộ cấu hình client-side mang nhãn UAT/test, và không minh bạch trạng thái bot/người thật tạo ra một `potential risk` về trust và cảm nhận an toàn của hệ thống.


## Ý nghĩa đối với bài UX
- Điểm đáng chú ý không chỉ nằm ở câu trả lời sai, mà còn ở cách sản phẩm biểu diễn vai trò và trạng thái hệ thống.
- Khi chatbot hứa chuyển sang người thật nhưng không xác minh được đã handoff thành công, mức độ mất niềm tin tăng lên rõ rệt.
- Vì vậy, vấn đề kỹ thuật ở đây chủ yếu củng cố nhận xét về trust breakdown: user không biết mình đang nói chuyện với bot hay người thật, và cũng không biết dữ liệu đang được xử lý qua hệ thống nào.
