# Thin SPEC — Cuối Day 05

Thin SPEC không phải PRD đầy đủ. Đây là bản cam kết đủ rõ để sáng Day 06 nhóm build ngay.

## 1. Track, product/app và user

**Track:** Food Delivery  
**Product/app thật:** Shopee Food / GrabFood  
**User cụ thể:** Người dùng chưa có lịch sử đặt hàng (hoặc ít lịch sử), đang tìm món bằng cụm từ mơ hồ ("ăn nhẹ", "no nhanh", "hôm nay mệt") vào bữa tối trong tuần  
**Nhóm có phải user thật không?** Có một phần — nhóm dùng app được, nhưng đã quen UI nên ít bị cold-start như user mới. Khác ở chỗ: user thật còn bị áp lực thời gian (đói, mệt sau giờ làm), nhóm test trong trạng thái bình tĩnh nên dễ bỏ qua friction nhỏ.

---

## 2. Evidence summary

| Evidence | Nguồn | User/pain nói lên điều gì? | SPEC phải đổi gì? |
|---|---|---|---|
| Tìm "món ăn nhẹ buổi tối" trả về kết quả theo popularity, không theo intent "nhẹ" | Self-use, Grab, Shopee Food | AI chỉ match keyword, không hiểu "nhẹ" là ít calo, ít no, hay nhanh | Build slice phải có bước clarification trước khi render kết quả |
| "App cứ gợi ý mấy quán cũ hoặc quán sponsor, tìm món cụ thể thì ra kết quả lung tung" | Review 2-3 sao, App Store Shopee Food | Recommendation bị bias sponsor, không cá nhân hoá | Auto/Aug: không thể dùng automation thuần — cần user confirm intent |
| Lần đầu mở GrabFood, gợi ý toàn quán popular, không hỏi khẩu vị | Self-use, GrabFood | Cold-start problem: AI không có tín hiệu để cá nhân hoá | 4 paths phải có low-confidence path rõ khi không có lịch sử |
| Baemin hỏi khẩu vị trong onboarding trước khi hiển thị feed | Competitor, self-use Baemin | Explicit preference giải được cold-start đơn giản | Prototype nên có 1-2 câu hỏi clarification thay vì đoán mò |

---

## 3. Pain statement

```
User mới hoặc user không có món cụ thể trong đầu
đang gặp khó ở bước tìm kiếm / browse món ăn,
vì AI chỉ match keyword và trả kết quả theo popularity —
không có bước nào hỏi lại khi query mơ hồ,
dẫn tới user phải scroll nhiều và thường đặt lại quán quen thay vì khám phá được món phù hợp.
Bằng chứng chính là: review 2 sao App Store Shopee Food + self-use observation
khi tìm "món ăn nhẹ buổi tối" trên cả Shopee Food lẫn GrabFood.
```

---

## 4. Build slice

```
Cho user đang gõ query tìm món ăn có dưới 3 từ hoặc dùng tính từ chung ("nhẹ", "no", "nhanh"),
prototype sẽ dùng AI để nhận diện intent mơ hồ và hỏi lại 1 câu clarification ngắn,
tạo ra danh sách gợi ý phù hợp hơn với context thật của user,
và xử lý trường hợp user bỏ qua câu hỏi bằng cách fallback về kết quả mặc định
kèm tag filter để user tự điều chỉnh.
```

---

## 5. Auto/Aug decision

- [x] **Augmentation:** AI gợi ý / hỏi clarification, user quyết cuối.
- [ ] **Conditional automation:** AI tự làm trong case hẹp; case mơ hồ/rủi ro chuyển người.
- [ ] **Automation:** AI tự quyết và tự hành động.

**Lý do chọn:** Sai món đặt = mất tiền thật + trải nghiệm tệ. AI không nên tự quyết thay user. Clarification là bước augment nhỏ — AI đề xuất hướng hiểu, user xác nhận.  
**Human role:** decider — user chọn món cuối từ danh sách AI đề xuất

---

## 6. Four paths

| Path | Prototype phải thể hiện gì? |
|---|---|
| **Happy** | User gõ "ăn nhẹ" → AI hiển thị 1 câu hỏi "Nhẹ theo nghĩa ít calo hay ít no?" → user chọn → ra 5 quán / món đúng intent, conversion tốt |
| **Low-confidence** | Query quá ngắn hoặc quá chung ("ăn", "đồ ăn") → AI không hỏi câu mở mà hiển thị 3-4 tag để user chọn nhanh (ít friction hơn) |
| **Failure** | AI đoán sai intent → user thấy kết quả không liên quan → có nút "Tìm lại / Đổi tiêu chí" rõ ràng, không bắt user gõ lại từ đầu |
| **Correction** | User nhấn "Đổi tiêu chí" → quay về màn hình clarification với câu trả lời trước đã được giữ lại để user chỉ cần sửa 1 điểm |

---

## 7. Failure mode nguy hiểm nhất

```
Nếu user gõ tên món cụ thể ("bún bò Huế", "cơm tấm sườn bì chả", "phở bò tái"),
AI có thể nhận diện nhầm là intent mơ hồ và hỏi clarification không cần thiết,
hậu quả là user bực bội vì họ rõ ràng đã biết mình muốn gì — friction tăng, bounce rate tăng.
Prototype sẽ xử lý bằng: nếu query match tên món trong food DB → skip clarification hoàn toàn,
chỉ kích hoạt clarification khi query là tính từ đơn lẻ hoặc cụm chung không khớp tên món nào.
Owner kiểm thử path này là: [tên thành viên phụ trách test].
```

---

## 8. Owner plan cho sáng Day 06

| Thành viên | Việc phụ trách | Bằng chứng cần có trong repo |
|---|---|---|
| P1 | Prototype UI — màn hình search + clarification flow + kết quả | File code hoặc Figma prototype có thể click |
| P2 | Test failure path — tìm case AI hỏi sai lúc (tên món cụ thể, query tiếng Anh, mix Anh-Việt) | Screenshot test log + danh sách edge case |
| P3 | Evidence pack + thin SPEC hoàn thiện (bổ sung quote thật từ review) | File markdown đã điền đủ, không còn placeholder |
| P4 | Demo script + repo README | README.md mô tả cách chạy + slide demo 3-5 phút |
