# Toolkit — Từ Evidence Đến Build Slice


## 1. Gom evidence thành cụm

Theo **workflow/pain**

**Cụm 1 — Không có entry point khi không biết muốn ăn gì**
- Self-use Shopee Food: tìm "món ăn nhẹ buổi tối" → kết quả theo popularity, không theo intent "nhẹ"
- Self-use Shopee Food: ô search bắt buộc dùng tên món/quán cụ thể; không thể tìm theo mood
- Review App Store Shopee Food (2-3 sao): "gợi ý mấy quán cũ hoặc quán sponsor, tìm món cụ thể thì ra kết quả lung tung"
- Self-use tổng hợp: phải lướt list như đi chợ khi không có món trong đầu → decision fatigue

**Cụm 2 — Cold-start problem: AI không có tín hiệu để cá nhân hoá**
- Self-use GrabFood: lần đầu mở app → gợi ý toàn quán popular, không hỏi khẩu vị
- Self-use Shopee Food: feed đầu hiển thị combo discount, flash sale, quán mới — không personalized
- Competitor Baemin: hỏi khẩu vị trong onboarding trước khi hiển thị feed → giải cold-start đơn giản

**Cụm 3 — AI quyết định không minh bạch, user mất kiểm soát** *(backlog)*
- Review Google Play Grab (1 sao): ghép đơn optimize theo lợi nhuận platform, không theo ETA thực tế
- Review App Store GrabFood (3 sao): estimate bị push nhiều lần, cancel đơn tự động không cần user confirm
- Review Google Play Grab (1 sao): cơ chế ghép đơn bị cảm nhận là thiên vị platform



## 2. Viết insight

**Insight của nhóm:**

```text
User mới hoặc user không có món cụ thể trong đầu không chỉ cần "gợi ý món ăn phổ biến".
Họ thật ra cần một điểm khởi đầu phù hợp với trạng thái hiện tại của mình — thu hẹp không gian lựa chọn trước khi render kết quả,
vì cả self-use lẫn review đều chỉ ra cùng một pattern: khi user không có món cụ thể trong đầu,
app không có entry point nào giúp ngoài keyword search và list popularity —
dẫn đến scroll dài, decision fatigue, và cuối cùng đặt lại quán quen thay vì khám phá được món phù hợp.
```


## 3. Viết opportunity

**Opportunity của nhóm:**

```text
Cơ hội là dùng AI để augment bước tìm kiếm —
nhận diện khi query mơ hồ (tính từ chung, cụm ngắn không khớp tên món nào)
→ hỏi 1 câu clarification ngắn hoặc hiển thị 3-4 mood-tag để user chọn nhanh,
giúp user thu hẹp không gian lựa chọn và khám phá được món phù hợp intent thật,
trong khi vẫn kiểm soát rủi ro AI hỏi nhầm bằng cách: nếu query match tên món trong food DB → skip clarification hoàn toàn.
```

## 4. Chọn build slice

| Câu hỏi | Đạt khi | Nhóm đạt chưa? |
|---|---|---|
| User cụ thể chưa? | Nói được ai dùng, trong bối cảnh nào. | ✅ User mới / ít lịch sử, đang tìm món bằng cụm từ mơ hồ vào bữa tối trong tuần |
| Task đủ hẹp chưa? | Demo được trong 3-5 phút. | ✅ Một flow: gõ query mơ hồ → clarification/mood-tag → kết quả; không cần backend thật |
| AI decision rõ chưa? | AI gợi ý/tự làm một việc cụ thể. | ✅ AI nhận diện query mơ hồ → hỏi 1 câu clarification hoặc hiển thị mood-tag → render kết quả theo intent |
| Failure path rõ chưa? | Có một case AI không chắc hoặc sai để test. | ✅ User gõ tên món cụ thể ("bún bò Huế") → AI nhầm là intent mơ hồ → hỏi clarification không cần thiết → friction |
| Có evidence không? | Có bằng chứng từ self-use/review/user/competitor. | ✅ Self-use Shopee/Grab + review 2-3 sao App Store + competitor Baemin/Foody |

**Kết luận:** Build slice đạt cả 5. Giữ nguyên hướng, không cần đổi.

## 5. Quyết định: giữ, giảm scope, hay đổi hướng?

| Tình huống | Quyết định | Nhóm thuộc tình huống nào? |
|---|---|---|
| Evidence yếu, user mơ hồ | Dừng build sâu; quay lại research 20 phút. | Không — evidence đủ từ 3 nguồn độc lập |
| Ý tưởng quá rộng | Giữ domain, cắt xuống một flow. | Có một phần — ban đầu muốn fix cả search lẫn discovery; đã cắt xuống còn clarification + mood-tag trong search flow |
| AI không cần thiết | Dùng rule/manual prototype; ghi rõ vì sao không dùng AI sâu. | Không — intent detection cần AI; rule đơn giản không phân biệt được "ăn nhẹ" theo nghĩa ít calo vs ít no vs nhanh |
| Rủi ro cao | Chọn augmentation hoặc conditional automation. | ✅ Đã chọn Augmentation — sai món = mất tiền thật; AI không tự quyết |
| Không demo được trong 1 ngày | Đưa phần lớn vào backlog, giữ một path nhỏ. | Có một phần — cross-sell, onboarding, correction path tự động đã đưa vào backlog; giữ lại happy + low-confidence + failure path |

**Quyết định cuối: Giữ build slice, giảm scope đúng chỗ.**
- Build Day 06: clarification flow (happy path) + mood-tag fallback (low-confidence path) + nút "Tìm lại" (failure path)


## 6. Câu chốt cuối

```text
Dựa trên self-use (Shopee Food, GrabFood) + review App Store/Google Play + competitor (Baemin, Foody),
nhóm sẽ build một prototype clarification + mood-tag discovery flow trên màn hình tìm kiếm,
cho user mới hoặc user không có món cụ thể trong đầu, đang tìm món bằng cụm từ mơ hồ vào bữa tối trong tuần,
để giải quyết pain "không có entry point phù hợp khi không biết muốn ăn gì — chỉ có keyword search và list theo popularity",
bằng cách AI augment bước tìm kiếm: nhận diện query mơ hồ → hỏi 1 câu clarification ngắn hoặc hiển thị mood-tag → render kết quả phù hợp intent,
và sẽ test failure path: user gõ tên món cụ thể ("bún bò Huế", "cơm tấm sườn") bị AI hỏi clarification không cần thiết → friction tăng, bounce.
```

## 7. Backlog

Những thứ không build:

- **AI cross-sell / upsell thông minh sau khi đặt món** — vấn đề có evidence (Grab gợi ý "bạn có thể gọi thêm" quá hẹp), nhưng cần data lịch sử đơn hàng và meal-context modeling; quá phức tạp cho 1 ngày.
- **Transparency layer cho AI decision** — giải quyết trust issue (ghép đơn không minh bạch, cancel đơn không xin phép user); quan trọng nhưng không thuộc discovery flow đang build, để sau.
- **Context-aware recommendation theo giờ trong ngày** — DoorDash "Tonight's pick" là pattern tốt; cần time-slot logic + đủ lịch sử đơn hàng để cá nhân hoá; đưa vào v2 sau khi có preference data từ onboarding.
- **Onboarding preference collection** — Baemin hỏi khẩu vị trước khi hiển thị feed là pattern đúng; nhưng thay đổi onboarding flow nằm ngoài build slice clarification hiện tại; backlog riêng.
- **Correction path tự động** — giữ lại câu trả lời clarification cũ khi user nhấn "Đổi tiêu chí"; logic UX đã rõ nhưng cần state management phức tạp hơn prototype ngày 1; prioritize happy path trước.