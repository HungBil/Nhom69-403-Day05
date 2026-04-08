# AI Product Canvas — Nhom5.2-Room

## Canvas

|   | Value | Trust | Feasibility |
|---|-------|-------|-------------|
| **Trả lời** | **User:** Khách cá nhân lần đầu tìm hiểu xe VinFast, chưa biết nên chọn dòng nào. **Pain:** Phải đến showroom hoặc chờ chat với sale 15-30 phút, chất lượng tư vấn không đồng đều và có thể thiên vị theo doanh số. **AI giải quyết:** Hỏi 4-5 câu về số người, mục đích sử dụng, ngân sách, ưu tiên điện/xăng, quãng đường di chuyển rồi gợi ý top 2-3 dòng xe phù hợp kèm so sánh nhanh về giá, số chỗ, quãng đường pin và tag khớp nhu cầu. | **Khi AI sai:** Có thể gợi ý vượt ngân sách hoặc sai số chỗ, khiến user đi sai hướng và mất thời gian nhưng chưa gây thiệt hại tài chính trực tiếp vì chưa mua. **User biết sai:** Đọc spec, thấy giá hoặc số chỗ không khớp nhu cầu. **User sửa:** Nhấn “Không phù hợp” hoặc đổi tiêu chí để AI hỏi lại và gợi ý lại; luôn có nút “Chat với tư vấn viên” để fallback. | **Cost:** khoảng $0.003–0.01/lượt cho 1 hội thoại 4-5 turn bằng GPT-4o-mini hoặc tương đương. **Latency:** dưới 3 giây mỗi response. **Risk chính:** dữ liệu giá xe thay đổi theo campaign; câu hỏi về tài chính như trả góp/lãi suất có thể gây hiểu lầm nếu AI trả lời sai, nên phải chặn ngoài scope. |

---

## Automation hay augmentation?

☐ Automation — AI làm thay, user không can thiệp

☑ Augmentation — AI gợi ý, user quyết định cuối cùng

**Justify:** Đây là quyết định mua xe trị giá lớn nên AI chỉ nên thu hẹp lựa chọn từ nhiều dòng xe xuống 2-3 dòng phù hợp. User vẫn cần đọc spec, cân nhắc thực tế và lái thử trước khi quyết định. Nếu AI sai, user vẫn có thể tự sửa hoặc chuyển sang tư vấn viên.

---

## Learning signal

| # | Câu hỏi | Trả lời |
|---|---------|---------|
| 1 | User correction đi vào đâu? | Khi user nhấn “Không phù hợp” hoặc thay đổi tiêu chí, lưu lại cặp (nhu cầu đầu vào, xe bị reject) để cải thiện ranking và rule matching. |
| 2 | Product thu signal gì để biết tốt lên hay tệ đi? | Positive: user nhấn “Xem chi tiết” hoặc “Đặt lịch lái thử”. Negative: user nhấn “Không phù hợp”, thoát chat ngay sau gợi ý, hoặc chuyển sang tư vấn viên. |
| 3 | Data thuộc loại nào? ☐ User-specific · ☑ Domain-specific · ☐ Real-time · ☐ Human-judgment · ☐ Khác: ___ | Chủ yếu là user-specific và domain-specific: nhu cầu mỗi người khác nhau, nhưng catalog xe VinFast, giá và spec là dữ liệu miền riêng của product. |

**Có marginal value không?** Có. Dữ liệu nhu cầu thực tế của khách VinFast như ngân sách phổ biến, số người trong gia đình, quãng đường di chuyển và mức độ chấp nhận xe điện là dữ liệu model chung không có. Nếu tích lũy đủ, nhóm có thể tối ưu ranking và hiểu segment khách hàng tốt hơn.

---

*AI Product Canvas — Ngày 5 — VinUni A20 — AI Thực Chiến · 2026*
