# Evidence Pack — Nhóm [Tên nhóm]

Nộp kèm thin SPEC cuối Day 05.

## 1. Nhóm và track

**Tên nhóm:** [Điền tên nhóm]  
**Track:** Food Delivery  
**Product/app đã chọn:** Shopee Food / GrabFood  
**Build slice đang nghĩ:** AI nhận diện intent mơ hồ khi user tìm món ("ăn gì đó mới", "không biết muốn ăn gì") → hỏi clarification hoặc gợi ý theo mood thay vì chỉ trả kết quả theo popularity/keyword

---

## 2. Self-use evidence

| Observation | Screenshot/link | Path liên quan | Điều học được |
|---|---|---|---|
| **[Grab]** Recommendation phù hợp địa điểm trong bán kính 3-5km — location-aware hoạt động tốt | [screenshot] | Happy | Grab giải được geo-filter nhưng chưa giải được intent filter |
| **[Grab]** Sau khi chọn món Mỳ Quảng, mục "Bạn có thể gọi thêm" chỉ trả về nước uống, bỏ qua tráng miệng / đồ ăn mặn bổ sung — thậm chí một số món còn không có | [screenshot] | Failure | AI cross-sell quá hẹp, không hiểu meal context; optimize theo category đơn giản thay vì theo bữa ăn hoàn chỉnh |
| **[Shopee Food]** Chưa có lịch sử: feed hiển thị combo có discount, flash sale, quán bán chạy, quán mới — không hỏi khẩu vị, không personalized | [screenshot] | Low-confidence (absent) | Cold-start hoàn toàn dựa vào popularity + sponsor, không có explicit preference collection |
| **[Shopee Food]** Tìm kiếm bắt buộc phải dùng từ khoá liên quan đến tên món hoặc tên quán cụ thể — không thể tìm theo mood hay trải nghiệm ("muốn ăn gì đó mới") | [screenshot] | Failure | Search là keyword-only, không có intent layer; user muốn khám phá bị chặn ngay từ bước search |
| **[Shopee Food]** Kết quả search sắp xếp theo "relevance" nhưng không rõ logic — rating, khoảng cách, tốc độ chuẩn bị không được thể hiện rõ khi rank | [screenshot] | Failure | User không biết nên cân nhắc tiêu chí nào để chọn; AI ranking thiếu transparency |
| **[Shopee Food]** Sau khi đặt: hiển thị món hay đặt lên đầu + quán đã xem gần đây — có personalization nhưng chỉ dựa trên lịch sử, không theo context hiện tại (giờ, tâm trạng) | [screenshot] | Happy (limited) | Có memory nhưng không có context-awareness; gợi ý cũ không kích thích khám phá món mới |

---

## 3. User / review / social evidence

| Quote / review / observation | Nguồn | User là ai? | Pain/failure mode |
|---|---|---|---|
| "đặt giao hàng nhanh mà cứ ghép đơn chờ lâu quá trời, để nghị grab đừng có ghép đơn nữa cách 2km mà chờ lâu quá trời mới nhận dc hàng" | Google Play, Grab, 1 sao — Quang Khải Nguyễn, 23/05/2026 | User đặt GrabFood thường xuyên, ưu tiên tốc độ | AI ghép đơn optimize theo lợi nhuận platform, không theo ETA thực tế của user |
| "grab tăng phí nền tảng với khách hàng nhưng lại cắt giảm thưởng cho tài xế và ghép đơn vô tội vạ nhằm lấy lun phần tiền ship của đơn ghép thứ 2 dành cho tài xế" | Google Play, Grab, 1 sao — Đại Nguyễn, 28/05/2026 | User nhận ra cơ chế ghép đơn | AI decision (ghép đơn) không minh bạch, bị cảm nhận là thiên vị platform hơn user |
| "the app will only display the delays as they occur, meaning that the initial estimate of food arriving by 5:30pm can be pushed to 6:30pm and beyond… they cancel the order with no input from our side" | App Store, GrabFood, 3 sao — M!k3Li, ~4 năm trước | Gia đình đặt thường xuyên, cần độ tin cậy cao | AI delivery estimate không có confidence interval; cancel đơn tự động không cần user confirm = mất trust hoàn toàn |
| App tập trung gợi ý đồ ăn nhanh, thiếu đa dạng. Khi không biết muốn ăn gì thì gợi ý rất đơn điệu, không hứng thú — phải lướt list như đi chợ | Self-use tổng hợp, Shopee Food | User muốn khám phá món mới, không có món cụ thể trong đầu | Discovery UX bị broken: không có mood-based hoặc intent-based entry point ngoài keyword search |

---

## 4. Competitor / analog evidence

| App / mô hình tham khảo | Họ xử lý task này thế nào? | Pattern học được | Có áp dụng trong 1 ngày không? |
|---|---|---|---|
| **Baemin** | Onboarding hỏi khẩu vị ("Bạn thích món gì?") trước khi hiển thị feed lần đầu | Explicit preference collection giải cold-start; đơn giản, không cần model phức tạp | Có — 2-3 câu hỏi onboarding, lưu preference tag |
| **DoorDash** (Mỹ) | "Tonight's pick" thay đổi theo giờ trong ngày + lịch sử; "Reorder" one-tap cho món hay đặt | Context-aware (time of day) + friction-reduction cho returning user | Có — time slot + order history đã có sẵn trong data |
| **Foody / Now** | Tag lọc theo mood: "ăn nhanh", "ăn lành", "no lâu", "ăn vặt" | Intent-based browse song song với keyword search; user không cần biết tên món cụ thể | Có — tag đơn giản, không cần model, implement nhanh |
| **Netflix** (analog) | "Play Something" button khi user không biết xem gì — AI tự chọn 1 nội dung dựa trên lịch sử | Reduce decision paralysis bằng 1 action dứt khoát thay vì list vô tận | Có — "Gợi ý ngay 1 món" button là version food delivery |

---

## 5. Evidence → Insight

```
Evidence nổi bật nhất:
Cả self-use lẫn review đều chỉ ra cùng một pattern:
khi user không có món cụ thể trong đầu, app không có entry point nào giúp
— chỉ hiện list dài theo popularity/sponsor hoặc bắt buộc search bằng keyword.
Review App Store (GrabFood) còn cho thấy thêm vấn đề trust:
AI quyết định (ghép đơn, cancel đơn) không minh bạch, user không có quyền kiểm soát.

Insight:
User không chỉ cần "gợi ý món ăn".
Thật ra họ cần một điểm khởi đầu phù hợp với trạng thái hiện tại của mình —
vì khi đói và không biết muốn ăn gì, lướt list vô tận không giải quyết được vấn đề,
mà còn tạo thêm decision fatigue dẫn đến đặt lại quán quen (không khám phá).

Opportunity:
AI có thể hỏi 1-2 câu context ngắn ("Hôm nay muốn ăn nhẹ hay no?", "Muốn thử món mới hay ăn quen?")
hoặc cung cấp mood-tag để user chọn nhanh —
thu hẹp không gian lựa chọn trước khi render kết quả, giảm scroll, tăng khả năng khám phá.
```

---

## 6. Evidence đổi SPEC như thế nào?

- [ ] Đổi user chính.
- [x] Đổi pain statement — từ "AI không hiểu keyword" → "không có entry point cho user không biết muốn ăn gì".
- [x] Đổi build slice — thêm "mood/intent tag" song song với clarification; không chỉ xử lý search mà xử lý luôn discovery flow.
- [x] Đổi Auto/Aug decision — giữ Augmentation; evidence về AI cancel đơn không input (GrabFood) xác nhận không nên để AI tự quyết trong food context.
- [x] Đổi 4 paths — bổ sung Correction path: user chọn tag sai mood → phải có cách đổi nhanh không cần reload.
- [x] Đổi failure mode — thêm case "user có món cụ thể trong đầu nhưng bị hỏi clarification không cần thiết" (rút ra từ Grab cross-sell observation).
- [ ] Đổi owner/test plan.

```
Trước evidence, nhóm định build clarification flow chỉ cho search query mơ hồ.

Sau evidence, nhóm đổi thành: xử lý cả hai entry point —
(1) search intent clarification khi query mơ hồ,
(2) mood-tag discovery khi user không gõ gì / mở app không biết muốn ăn gì.

Lý do: self-use Shopee Food cho thấy vấn đề lớn hơn không phải ở search mà ở
discovery flow — user thậm chí không biết gõ gì vào ô search vì chưa có món trong đầu.
Fix search thôi thì chỉ giải được 50% vấn đề.
```
