# SPEC draft — Nhom5.2-Room

## Track: VinFast (Track A)

---

## 1. Problem Statement

> **1 câu:** Khách cá nhân không biết chọn dòng xe VinFast nào, hiện phải đến showroom hoặc chờ chat với sale mất 15–30 phút, AI có thể hỏi 4–5 câu về nhu cầu và gợi ý top 2–3 dòng xe phù hợp kèm so sánh ngắn.

Khách hàng cá nhân muốn mua xe VinFast nhưng không biết dòng xe nào phù hợp với nhu cầu thực tế (số người trong gia đình, quãng đường di chuyển chính, ngân sách, ưu tiên cá nhân). Hiện tại, khách phải đến showroom hoặc chat với nhân viên tư vấn — mất 15–30 phút, phụ thuộc giờ làm việc, và chất lượng tư vấn không đồng đều giữa các nhân viên.

AI có thể hỏi 4–5 câu về nhu cầu sử dụng và trả về top 2–3 dòng xe phù hợp kèm bảng so sánh ngắn — giúp khách hàng có điểm khởi đầu rõ ràng trước khi liên hệ showroom hoặc đặt lịch lái thử.

---

## 2. AI Product Canvas

|             | Value                                                                                                                                                                                                                                                                                                                                                                                                        | Trust                                                                                                                                                                                                                                                                                                                                                                                                                          | Feasibility                                                                                                                                                                                                                                                                                       |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Trả lời** | **User:** Khách cá nhân lần đầu tìm hiểu xe VinFast (chưa biết chọn dòng nào). **Pain:** Phải đến showroom hoặc chờ chat với sale, thời gian chờ 15-30 phút, sale có thể thiên vị dòng xe có hoa hồng cao. **AI giải quyết:** Hỏi nhu cầu → gợi ý top 2-3 dòng xe phù hợp kèm so sánh nhanh (giá, số chỗ, quãng đường pin, loại pin/xăng). Khách có bức tranh tổng quan trước khi quyết định bước tiếp theo. | **Khi AI sai:** Gợi ý dòng xe vượt ngân sách, hoặc xe 5 chỗ cho gia đình 7 người → khách mất thời gian tìm hiểu sai hướng, nhưng **không thiệt hại tài chính trực tiếp** (vì chưa mua). **User biết sai:** Khi đọc spec xe thấy không khớp nhu cầu (ít chỗ hơn cần, giá cao hơn budget). **User sửa:** Nhấn "Không phù hợp, tôi cần xe cho 7 người" → AI hỏi lại và gợi ý lại. Luôn có nút "Chat với tư vấn viên" để fallback. | **Cost:** ~$0.003–0.01/lượt (1 cuộc hội thoại 4-5 turn dùng GPT-4o-mini hoặc tương đương). **Latency:** <3 giây/response. **Risk chính:** (1) Dữ liệu giá xe thay đổi theo tháng — KB phải cập nhật; (2) Khách hỏi về tài chính (trả góp, lãi suất) → AI trả lời sai có thể gây hiểu lầm pháp lý. |

### Automation hay Augmentation?

☑ **Augmentation** — AI gợi ý, khách hàng quyết định cuối cùng.

**Justify:** AI chỉ thu hẹp lựa chọn từ ~10 dòng xe xuống 2-3 dòng phù hợp. Quyết định mua xe là quyết định lớn (hàng trăm triệu VNĐ), khách cần lái thử và so sánh tại showroom. AI không thể và không nên thay thế bước đó. Nếu automation (AI tự đặt lịch mua xe) thì sai = thiệt hại tài chính rất lớn → Augmentation là lựa chọn an toàn và hợp lý.

### Learning Signal

| #   | Câu hỏi                                          | Trả lời                                                                                                                                                                                                                                                  |
| --- | ------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | User correction đi vào đâu?                      | Khi user nhấn "Không phù hợp" hoặc thay đổi tiêu chí → ghi lại cặp (input nhu cầu, xe bị reject) để cải thiện ranking algorithm                                                                                                                          |
| 2   | Product thu signal gì để biết tốt lên hay tệ đi? | (a) Tỷ lệ user nhấn "Xem chi tiết" hoặc "Đặt lịch lái thử" sau gợi ý → positive signal; (b) Tỷ lệ user nhấn "Không phù hợp" hoặc thoát chat ngay sau gợi ý → negative signal; (c) Tỷ lệ user chuyển sang chat tư vấn viên sau gợi ý → AI chưa đủ tin cậy |
| 3   | Data thuộc loại nào?                             | ☑ User-specific (nhu cầu mỗi người khác) · ☑ Domain-specific (catalog xe VinFast, giá, spec)                                                                                                                                                             |

**Có marginal value không?** Có — data về nhu cầu thực tế của khách VinFast (ngân sách phổ biến, số người gia đình, quãng đường di chuyển) là data riêng mà model chung không có. Nếu tích lũy đủ sẽ biết được segment khách hàng nào cần dòng xe nào → chạy lại marketing campaign chính xác hơn.

---

## 3. User Stories — 4 Paths

### Path 1: AI đúng — Happy path

> **Kịch bản:** Khách hỏi "Tôi có gia đình 4 người, ngân sách khoảng 600 triệu, chủ yếu đi trong thành phố, muốn xe điện".
>
> **AI trả lời:** Gợi ý VF 6 (5 chỗ SUV compact, ~600tr, phù hợp gia đình 4 người + đi phố) và VF 5 Plus (5 chỗ sedan, ~500tr, tầm pin tốt cho đi phố) kèm bảng so sánh: số chỗ, quãng đường pin, giá niêm yết, khuyến mãi hiện tại. Không gợi ý VF 7 (7 chỗ, ~700tr) vì vượt ngân sách — chỉ mention kèm note "nếu anh/chị linh hoạt thêm 100tr".
>
> **User flow:** Khách đọc so sánh → nhấn "Xem chi tiết VF 6" → được chuyển sang trang spec chính thức → có nút "Đặt lịch lái thử".
>
> **UX:** Hiện bảng so sánh card-style, mỗi xe 1 card, highlight điểm khớp nhu cầu bằng tag xanh ("✓ Phù hợp ngân sách", "✓ Xe điện", "✓ Đủ chỗ"). Xe mention ngoài budget (VF 7) hiện card nhỏ hơn với tag "💡 Nếu linh hoạt thêm 100tr".

### Path 2: AI không chắc — Câu hỏi mơ hồ

> **Kịch bản:** Khách hỏi "Tôi muốn mua xe đẹp, giá hợp lý".
>
> **Vấn đề:** "Đẹp" và "hợp lý" là tiêu chí chủ quan, AI không thể đoán.
>
> **UX đề xuất:** Thay vì đoán, AI hỏi lại bằng quick-reply buttons:
>
> - "Quý khách dùng xe chủ yếu để: Đi làm hàng ngày / Đi gia đình / Chạy dịch vụ?"
> - "Ngân sách dự kiến: Dưới 500tr / 500–800tr / 800tr–1 tỷ / Trên 1 tỷ?"
> - "Ưu tiên: Xe điện / Xe xăng / Không quan trọng?"
>
> Sau 2-3 câu clarify, AI đủ thông tin để gợi ý. Nếu vẫn không rõ → "Em chưa chắc chắn đủ để gợi ý chính xác. Quý khách có muốn chat với tư vấn viên không?"

### Path 3: AI sai — Gợi ý không khớp

> **Kịch bản:** Khách nói "Tôi cần xe 7 chỗ cho gia đình đông, ngân sách 500 triệu". AI gợi ý VF 9 (giá ~1.5 tỷ) vì đúng 7 chỗ nhưng vượt ngân sách 3 lần.
>
> **User biết sai:** Nhìn giá trên card gợi ý thấy ngay vượt budget.
>
> **UX đề xuất:**
>
> - Mỗi card gợi ý hiện rõ giá + tag cảnh báo nếu vượt budget: "⚠ Vượt ngân sách 1 tỷ".
> - Nút "Không phù hợp — giá quá cao" → AI ghi nhận và gợi ý lại: "Hiện tại trong tầm giá 500tr, VinFast có VF 5 (5 chỗ) và VF 6 (5 chỗ SUV). Dòng 7 chỗ giá thấp nhất là VF 7 từ ~700tr. Quý khách có muốn xem VF 7 hoặc điều chỉnh ngân sách không?"
> - User sửa lại tiêu chí → AI cập nhật và gợi ý lại ngay. Mỗi lần user nhấn "Không phù hợp", data cặp (yêu cầu, xe bị từ chối) được ghi lại để cải thiện ranking.

### Path 4: User mất niềm tin — Exit path

> **Kịch bản:** Khách hỏi 2-3 lần, mỗi lần AI gợi ý đều không đúng ý. Khách bắt đầu không tin.
>
> **UX đề xuất:**
>
> - Sau 2 lần nhấn "Không phù hợp" liên tiếp, AI tự nhận: "Có vẻ em chưa hiểu đúng nhu cầu của anh/chị. Để chính xác hơn, anh/chị có thể:"
>   - 🧑‍💼 **Chat với tư vấn viên** (available 8h–21h)
>   - 📞 **Gọi hotline VinFast:** 1900 23 23 89
>   - 📍 **Tìm showroom gần nhất** (hiện bản đồ)
>   - 📝 **Để lại thông tin, tư vấn viên sẽ gọi lại**
> - Không bao giờ để user bị "treo" — luôn có ít nhất 1 exit path rõ ràng.

---

## 4. Eval Metrics

### Ưu tiên: Precision > Recall

**Lý do:** Gợi ý sai (xe vượt budget, sai số chỗ) sẽ khiến user mất tin ngay. Trong khi bỏ sót 1 dòng xe phù hợp (recall thấp) thì user ít nhận ra — vì user không biết catalog đầy đủ. **Sai nhầm đau hơn bỏ sót → ưu tiên precision.**

### Metrics cụ thể

| Metric                            | Mục tiêu | Cách đo                                                                                                                                                                                                        |
| --------------------------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Precision@3 — ground truth        | ≥ 80%    | Team review thủ công 50–100 sessions/tuần: mỗi gợi ý có khớp **tất cả tiêu chí hard** (ngân sách, số chỗ, loại nhiên liệu) không? Tính precision = số xe khớp / tổng xe gợi ý. Đây là metric chính, đo offline |
| Rejection rate — behavioral proxy | ≤ 20%    | Tỷ lệ user nhấn "Không phù hợp" trên tổng gợi ý. Đây là proxy runtime cho precision, nhưng **không thay thế ground truth** vì user có thể không bấm dù gợi ý sai                                               |
| Click-through rate                | ≥ 40%    | Tỷ lệ user nhấn "Xem chi tiết" hoặc "Đặt lịch lái thử" sau gợi ý                                                                                                                                               |
| Escalation rate                   | ≤ 20%    | Tỷ lệ user chuyển sang chat tư vấn viên. Nếu quá cao = AI chưa đủ tin cậy                                                                                                                                      |
| Session completion                | ≥ 70%    | Tỷ lệ user hoàn thành flow (từ câu hỏi đầu đến nhận gợi ý) mà không bỏ giữa chừng                                                                                                                              |

### Qualitative eval (giai đoạn đầu)

- Review 50–100 cuộc hội thoại đầu tiên bằng mắt
- Phân loại lỗi: NLU hiểu sai intent / ranking sai / thiếu data xe / user hỏi ngoài scope
- Ưu tiên fix theo tần suất lỗi

---

## 5. Failure Modes

| #   | Trigger                                                                                             | Hậu quả                                                    | Mitigation                                                                                                                                                                                           |
| --- | --------------------------------------------------------------------------------------------------- | ---------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | **Câu hỏi mơ hồ:** "Xe nào tốt nhất?"                                                               | AI đoán lung tung, gợi ý không liên quan                   | Hỏi lại bằng quick-reply buttons trước khi gợi ý. Không bao giờ đoán khi thiếu ≥2 tiêu chí bắt buộc (ngân sách, mục đích sử dụng)                                                                    |
| 2   | **Ngân sách không khớp catalog:** User muốn 7 chỗ giá 400tr nhưng VinFast không có dòng nào đáp ứng | AI im lặng hoặc gợi ý xe vượt budget rất xa                | Trả lời thẳng: "Hiện tại VinFast chưa có xe 7 chỗ trong tầm giá 400tr. Dòng 7 chỗ thấp nhất là VF 7 từ ~700tr. Anh/chị có muốn điều chỉnh tiêu chí không?"                                           |
| 3   | **Hỏi ngoài scope:** User hỏi về trả góp, lãi suất, bảo hiểm, so sánh với Toyota/Hyundai            | AI trả lời sai thông tin tài chính, gây hiểu lầm pháp lý   | Nhận diện intent ngoài scope → "Thông tin trả góp/lãi suất phụ thuộc ngân hàng đối tác. Để được tư vấn chính xác, anh/chị vui lòng liên hệ showroom hoặc hotline." Không bao giờ đưa con số lãi suất |
| 4   | **Data xe cũ:** Giá xe thay đổi theo campaign khuyến mãi, hoặc có dòng xe mới ra mắt                | AI gợi ý giá cũ, user đến showroom thấy giá khác → mất tin | Hiện timestamp: "Giá cập nhật tháng 04/2026". Đặt lịch sync data từ trang chủ VinFast mỗi tuần                                                                                                       |
| 5   | **User cố tình phá:** Prompt injection, hỏi lặp lại, yêu cầu AI đóng vai nhân viên sale             | AI bị khai thác hoặc nói sai thông tin                     | System prompt chặt: chỉ trả lời về catalog VinFast. Rate limit 20 tin/session. Sau 3 câu ngoài scope → tự động escalate                                                                              |

---

## 6. ROI 3 kịch bản

### Giả định chung

- Chi phí mỗi cuộc hội thoại AI: ~5.000 VNĐ (~$0.003–0.01 inference + hosting share)
- Chi phí mỗi cuộc tư vấn bằng nhân viên sale: ~50.000 VNĐ (15 phút × lương nhân viên)
- Số lượt hỏi mua xe trên website VinFast: ước tính ~5.000 lượt/tháng

|                        | Conservative                    | Realistic                       | Optimistic                                              |
| ---------------------- | ------------------------------- | ------------------------------- | ------------------------------------------------------- |
| **Giả định**           | 20% khách dùng AI, 50% hài lòng | 40% khách dùng AI, 70% hài lòng | 60% khách dùng AI, 85% hài lòng                         |
| **Lượt AI/tháng**      | 1.000                           | 2.000                           | 3.000                                                   |
| **Chi phí AI**         | 5 triệu VNĐ/tháng               | 10 triệu VNĐ/tháng              | 15 triệu VNĐ/tháng                                      |
| **Giảm tải nhân viên** | 500 cuộc (25 triệu)             | 1.400 cuộc (70 triệu)           | 2.550 cuộc (127 triệu)                                  |
| **Net benefit**        | +20 triệu/tháng                 | +60 triệu/tháng                 | +112 triệu/tháng                                        |
| **Bonus**              | —                               | Tăng tỷ lệ đặt lịch lái thử 10% | Data nhu cầu khách hàng → cải thiện marketing targeting |

### Kill criteria

- Nếu escalation rate > 50% sau 2 tháng liên tục → AI không đủ tin cậy, cần đánh giá lại NLU hoặc KB
- Nếu click-through < 15% sau 1 tháng → gợi ý không đủ chất lượng, dừng và review lại ranking logic
- Nếu chi phí AI > chi phí nhân viên tương đương (tức mất ý nghĩa tiết kiệm) → dừng scale, tối ưu cost

---

## 7. Mini AI Spec

### Scope

AI advisor **chỉ** tư vấn chọn dòng xe VinFast từ catalog hiện tại (xe điện + xe xăng đang bán). Không tư vấn tài chính (trả góp, lãi suất), không so sánh với thương hiệu khác, không đặt hàng/thanh toán.

### Input → Output

|            | Mô tả                                                                                                                                                       |
| ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Input**  | Nhu cầu user qua 4–5 câu hỏi: số người, mục đích sử dụng chính, ngân sách, ưu tiên (điện/xăng), quãng đường di chuyển chính                                 |
| **Output** | Top 2–3 dòng xe phù hợp, mỗi xe hiện: tên, giá niêm yết, số chỗ, quãng đường pin/100km, tag khớp nhu cầu. Kèm nút CTA: "Xem chi tiết" và "Đặt lịch lái thử" |

### Knowledge Base

- **Nguồn:** Catalog xe VinFast từ vinfast.vn (spec, giá niêm yết, khuyến mãi)
- **Format:** Structured JSON, mỗi dòng xe 1 record gồm: tên, phân khúc, số chỗ, loại nhiên liệu, giá min–max, quãng đường pin, kích thước
- **Cập nhật:** Sync thủ công mỗi tuần hoặc khi có campaign mới. Hiện timestamp trên mỗi gợi ý

### Guardrails

| Rule                                               | Hành vi                                                                     |
| -------------------------------------------------- | --------------------------------------------------------------------------- |
| Thiếu ≥ 2 tiêu chí bắt buộc (ngân sách, mục đích)  | Hỏi lại bằng quick-reply, KHÔNG đoán                                        |
| Câu hỏi về tài chính (trả góp, lãi suất, bảo hiểm) | Từ chối trả lời, chuyển hotline/showroom                                    |
| So sánh với thương hiệu khác (Toyota, Hyundai)     | "Em chỉ tư vấn về dòng xe VinFast"                                          |
| Prompt injection / yêu cầu đóng vai                | Bỏ qua, trả fallback hoặc escalate                                          |
| Không có xe nào khớp toàn bộ tiêu chí              | Nói thẳng giới hạn catalog, gợi ý điều chỉnh tiêu chí hoặc liên hệ showroom |
| User không hài lòng sau 2 lần gợi ý                | Tự động hiện escape hatch: hotline / showroom / để lại thông tin            |

### Fallback

Khi AI bế tắc (intent ngoài scope, user không hài lòng, lỗi hệ thống), luôn có ít nhất 1 trong 4 lối thoát:

1. Chat với tư vấn viên (8h–21h)
2. Gọi hotline VinFast: 1900 23 23 89
3. Tìm showroom gần nhất
4. Để lại thông tin, tư vấn viên gọi lại

---

## Chốt sản phẩm (Topic Summary)

**Topic:** AI Advisor tư vấn chọn xe VinFast — chatbot augmentation hỏi nhu cầu và gợi ý dòng xe phù hợp.

**Prototype ngày mai sẽ demo:** Chatbot web đơn giản, hỏi 4–5 câu qua quick-reply buttons → trả bảng so sánh 2–3 dòng xe card-style. Backend dùng GPT-4o-mini/Gemini Flash cho conversational layer, rule-based filter cho matching xe từ KB structured.

**Scope giới hạn:** Chỉ tư vấn chọn xe từ catalog VinFast hiện tại. Không đặt hàng, không tài chính, không so sánh hãng khác. Success action = user nhấn "Xem chi tiết" hoặc "Đặt lịch lái thử".

---

## Phân công

| Thành viên        | MSSV        | Phần phụ trách                                   |
| ----------------- | ----------- | ------------------------------------------------ |
| Nguyễn Đông Hưng  | 2A202600392 | Canvas + Problem Statement                       |
| Lưu Lương Vi Nhân | 2A202600120 | User Stories 4 paths                             |
| Khuất Văn Vương   | 2A202600087 | Eval metrics + ROI, Failure modes + Mini AI Spec |
| Huỳnh Văn Nghĩa | 2A202600085 | Frontend prototype + prompt testing |

> **Lưu ý:** Bảng phân công sẽ được cập nhật đầy đủ họ tên + MSSV khi nhóm chốt thành viên.
