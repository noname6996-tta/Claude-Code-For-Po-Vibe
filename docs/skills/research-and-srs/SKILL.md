---
name: research-and-srs
description: Research thị trường (Sensor Tower) và viết tài liệu SRS chuẩn cho mobile app feature — dùng khi PO yêu cầu spec hoá tính năng mới
---

# Skill: Research & viết tài liệu SRS cho mobile app

> Skill này hướng dẫn Claude cách khai thác thông tin từ PO/PM, research thị trường, và viết tài liệu SRS chuẩn để upload Confluence hoặc dùng cho dev vibecode.

---

## 1. Nguyên tắc cốt lõi

**Không bao giờ assume — chỉ viết khi đủ thông tin.**
Mọi quyết định thiếu thông tin trong SRS sẽ tạo ra ambiguity cho dev và QA. Nhiệm vụ đầu tiên là khai thác đủ thông tin, không phải viết ngay.

**Tách biệt 3 giai đoạn:**
1. Research thị trường & user insight
2. Chất vấn PO để xác nhận product decision
3. Viết tài liệu

Không gộp 3 giai đoạn vào một bước. Viết trước khi đủ thông tin = viết lại nhiều lần.

---

## 2. Giai đoạn 1 — Research thị trường & User Insight

### 2.1 Khi nào cần research

Research bắt buộc khi PO chưa có sẵn user insight hoặc competitive analysis. Không bỏ qua dù PO nghĩ mình đã hiểu thị trường.

### 2.2 Quy trình research

**Bước 1: Xác định competitor list**
- Hỏi PO: "App nào trên thị trường đang làm tính năng tương tự?"
- Nếu PO không rõ: tự search Sensor Tower / App Store theo keyword tính năng
- Ưu tiên app có nhiều rating nhất (= nhiều user nhất = nhiều insight nhất)

**Bước 2: Pull review thực tế từ Sensor Tower**
```
Workflow:
search_apps(term, os=ios) → lấy app_id
get_ratings(app_id) → xem rating distribution, % 1-star
get_reviews(app_id, rating=1, limit=100) → đọc pain points
get_reviews(app_id, rating=5, limit=50) → đọc điều user thích
```

Lấy review từ nhiều thị trường: US, GB, AU, PH, IN, ID, VN (cho app APAC).

**Bước 3: Phân tích và cluster pain points**
Đọc 150–300 reviews, cluster theo tag:
- `payment` / `monetization` — billing complaints
- `performance_and_bugs` — technical issues
- `content` — output quality complaints
- `feature_requests` — user muốn gì thêm
- `customer_support` — support experience

Tính tần suất: pain point nào xuất hiện trong >30% 1-star reviews là critical.

**Bước 4: Synthesize thành persona**
Từ pattern trong reviews, xây dựng persona dựa trên:
- Ngôn ngữ review (detect country)
- Use case họ mô tả ("I wanted to cover this song for TikTok")
- Mức độ tech-savvy (cách họ mô tả vấn đề)
- WTP (họ complain về mức giá nào)

Mỗi persona cần: tên, tuổi/country, goal, trigger, frustration, quote thực từ review, WTP.

### 2.3 Output của giai đoạn 1

Trước khi chuyển sang giai đoạn 2, phải có:
- Competitive landscape (6–10 app, phân tích feature matrix)
- Top 5–8 pain points xếp theo tần suất
- 3–5 validated personas
- Market gaps (tính năng không ai làm tốt)

---

## 3. Giai đoạn 2 — Chất vấn PO

### 3.1 Nguyên tắc chất vấn

**Dùng form/elicit UI** để thu thập thông tin có cấu trúc, không hỏi dạng văn xuôi.
Mỗi câu hỏi phải có option cụ thể để PO chọn — không hỏi open-ended mà không có anchor.

**Hỏi tối đa 6–8 câu mỗi round.** Nếu cần hỏi thêm, làm round 2 sau khi nhận câu trả lời round 1.

**Luôn confirm lại trước khi viết.** Sau khi nhận đủ câu trả lời, tóm tắt lại toàn bộ decision và hỏi "Confirm là mình generate SRS theo những quyết định này nhé?"

### 3.2 Danh sách câu hỏi bắt buộc cho mỗi tính năng mobile

| Câu hỏi | Tại sao cần hỏi |
|---|---|
| Input mode là gì? (text / upload / select / combo) | Quyết định toàn bộ Create screen layout |
| AI xử lý gì? User kiểm soát gì? | Phân biệt one-shot vs iterative |
| Sau generate user làm được gì? (edit / regen / readonly) | Quyết định post-generate UX |
| Monetization model? | Ảnh hưởng paywall placement, free tier |
| Output format? (mp3 / mp4 / share link / v.v.) | Export flow, permission iOS |
| Lưu trữ ở đâu? Expire không? | Library design, storage policy |
| Background processing không? | Queue architecture, notification |
| Ngôn ngữ / thị trường target? | Localization scope |

### 3.3 Câu hỏi chất vấn kỹ thuật (hỏi sau khi PO confirm flow)

Sau khi PO confirm UX flow, hỏi thêm:
- "Service AI là 3rd party hay self-hosted?" → ảnh hưởng timeout handling
- "Callback model hay polling?" → ảnh hưởng mobile architecture
- "Push notification dùng APNs trực tiếp hay qua server?" → ảnh hưởng NFR
- "File output serve từ CDN hay presigned URL?" → ảnh hưởng expire policy

### 3.4 Xử lý khi PO chưa quyết định

Nếu PO trả lời "chưa biết" / "để sau":
1. Đánh dấu thành **Open Item** trong tài liệu
2. Ghi rõ: Owner (ai quyết), Target (phase mấy)
3. Tiếp tục viết phần còn lại, placeholder rõ ràng

Không block toàn bộ tài liệu vì 1–2 open item.

### 3.5 Đề xuất format tài liệu trước khi viết

**Luôn đề xuất outline trước, chờ PO duyệt, rồi mới generate.**

Format chuẩn:
```
1. Goal (không có Persona section — merge vào research doc riêng)
2. Flow Description (bảng happy path + exception flows + kỹ thuật notes)
3. Screen Inventory (bảng đầy đủ: ID, trigger vào/ra, FR liên quan, state diagram)
4. Business Rules (BR-XX)
5. Functional Requirements (FR-XX, Given/When/Then)
6. Non-Functional Requirements (NFR-XX)
7. Open Items
```

Nếu PO muốn bỏ hoặc thêm section — cập nhật trước khi generate.

---

## 4. Giai đoạn 3 — Viết tài liệu SRS

### 4.1 Section 1: Goal

Viết ngắn gọn, tập trung vào:
- **Mục tiêu tính năng** (1–2 câu): user làm được gì mà trước đây không làm được?
- **Success metrics** (bảng): 3–5 metric đo lường được, gồm cả cách đo
- **Out of scope** (info box màu đỏ): liệt kê rõ những gì KHÔNG thuộc Phase 1

Không viết personas vào đây — personas thuộc research doc riêng.

### 4.2 Section 2: Flow Description

**Quy tắc quan trọng:**
- Happy path viết dạng bảng (Actor / Hành động / Kết quả) — không viết prose
- Exception flows viết bảng riêng — không nhét vào happy path
- Technical notes (callback model, queue architecture) viết trong info box riêng
- Diagram (BPMN/flowchart) vẽ trong Figma/Miro — trong docx chỉ có text description

**Về timeout và background processing:**
- Mobile không tự quản lý timeout — nếu dùng 3rd party service, timeout = một dạng failed callback
- Mô tả rõ: "Mobile lắng nghe callback success/failed. Failed bao gồm cả server timeout nội bộ"
- Không vẽ timeout branch riêng trên flow diagram

### 4.3 Section 3: Screen Inventory

Bảng đầy đủ với các cột:
- **Screen ID** (SCR-01, SCR-01b, SCR-02, v.v.)
- **Tên màn hình**
- **Trigger vào** (từ đâu đến màn này)
- **Trigger ra** (từ màn này đi đâu)
- **FR liên quan** (FR-01, FR-02, v.v.)

Thêm **State diagram** cho màn hình có nhiều state (ví dụ: in-progress card có states: In-progress → Done / Error → Cleared).

Screen ID convention:
- `SCR-01` = màn chính
- `SCR-01b`, `SCR-01c` = variant state của cùng màn
- Đánh số theo thứ tự flow, không theo alphabet

### 4.4 Section 4: Business Rules

Mỗi rule format:
```
BR-XX — Tên rule
• Bullet point 1
• Bullet point 2
• ...
```

Nhóm theo chủ đề: Input validation → Naming logic → Selection behavior → Processing → Storage → Output format.

**Đặt tên rule chính xác:** "Timeout & Retry" — sai nếu không có timeout phía mobile. Đặt đúng: "Failed callback & Retry policy".

### 4.5 Section 5: Functional Requirements

Format **Given / When / Then** cho mỗi FR:
- **GIVEN**: trạng thái ban đầu (màn hình, dữ liệu, điều kiện)
- **WHEN**: hành động xảy ra (user action hoặc system event)
- **THEN**: kết quả kỳ vọng (UI thay đổi, data thay đổi, navigation)

Mỗi FR thường có 2–3 GWT block: happy path, edge case 1, edge case 2.

**Viết THEN chi tiết đủ để dev implement không cần hỏi thêm:**
- Xấu: "Hiển thị thông báo lỗi"
- Tốt: "Hiển thị inline error bên dưới textarea: 'Please enter at least 5 characters'. Nút [Create] trở về disabled state. Input không bị xóa"

### 4.6 Section 6: Non-Functional Requirements

5 nhóm bắt buộc cho mobile app:
1. **Performance**: latency targets (API call ≤ 3s, audio playback start ≤ 1.5s)
2. **Reliability**: behavior khi mất mạng, app kill, sync on relaunch
3. **Storage & Format**: file format, bitrate, expire policy, CDN
4. **Compatibility**: iOS version minimum, dark mode, accessibility (VoiceOver)
5. **Security & Privacy**: auth token, access control, data logging policy

### 4.7 Section 7: Open Items

Bảng với các cột: ID · Topic · Mô tả · Owner · Target (Phase X hoặc Sprint X).

Không để Open Item không có Owner — nếu chưa biết owner, ghi "TBD" và flag cho PO.

---

## 5. Output formats

### 5.1 File `.docx` — dùng để upload Confluence / present stakeholder

- Generate bằng `docx-js` (Node.js)
- Dùng color-coded GWT blocks (Given = xanh nhạt, When = vàng nhạt, Then = xanh lá nhạt)
- Có header với page number
- Version bump mỗi lần update: v1.0 → v1.1 → v1.2
- Validate bằng `python scripts/office/validate.py` trước khi deliver

### 5.2 File `.md` — dùng để dev vibecode với Claude

Cấu trúc file `.md` tối ưu cho vibecode:

```markdown
# SRS — [Feature Name]
## Context
[1 đoạn tóm tắt feature]

## Business Rules
### BR-01 — [Tên]
- rule 1
- rule 2

## Screen: [Screen Name]
### Flow
1. Step 1
2. Step 2

### Functional Requirements
#### FR-01 — [Tên]
**Given** [điều kiện]
**When** [action]
**Then** [kết quả]
```

Khi dev dùng với Claude, prompt mẫu:
```
Based on this SRS, implement the [Screen Name] screen in SwiftUI.
Focus on [FR-01, FR-02]. Use [architecture pattern].
```

### 5.3 Upload Confluence

Nếu có Atlassian connector: tạo page trực tiếp qua API, không cần upload file.
Nếu không có connector: dùng **Confluence Import Word Document** feature (···  → Import Word Document).

---

## 6. Checklist trước khi deliver tài liệu

### Checklist nội dung
- [ ] Mỗi màn hình đã có Screen ID trong Screen Inventory
- [ ] Mỗi màn hình đã có FR tương ứng
- [ ] Mỗi BR đã được reference trong ít nhất 1 FR
- [ ] Exception flows (mất mạng, invalid input, failed callback) đã có GWT block
- [ ] Open Items có đầy đủ: mô tả, owner, target phase
- [ ] Out of Scope đã liệt kê rõ — không để PO assume

### Checklist kỹ thuật
- [ ] Không có "timeout phía mobile" nếu dùng 3rd party callback
- [ ] Background processing đã mô tả: "job tiếp tục khi app bị kill"
- [ ] Retry policy rõ: max N lần, trigger là user action hay automatic
- [ ] Push notification: có fallback nếu user deny permission không?
- [ ] File export: direct download hay presigned URL? Expire không?

### Checklist format
- [ ] Version số đúng (v1.0, v1.1...)
- [ ] Section numbers đúng thứ tự
- [ ] GWT blocks có đủ 3 dòng (không thiếu Given hoặc Then)
- [ ] Tên BR chính xác với logic thực (không dùng "Timeout" nếu là callback model)

---

## 7. Anti-patterns cần tránh

| Anti-pattern | Tại sao sai | Làm đúng |
|---|---|---|
| Viết SRS trước khi hỏi đủ câu hỏi | Tài liệu sẽ sai và phải viết lại | Chất vấn đủ, confirm, rồi mới viết |
| Gộp persona vào SRS | SRS là tài liệu kỹ thuật, không phải research doc | Persona để ở research doc riêng |
| Viết "timeout 15 phút" khi dùng 3rd party service | Mobile không quản lý timeout — sai về mặt kỹ thuật | Viết "failed callback bao gồm server timeout" |
| THEN quá chung: "hiển thị thông báo lỗi" | Dev phải hỏi lại: lỗi gì? ở đâu? | THEN phải đủ chi tiết để implement ngay |
| Open Item không có owner | Sẽ không ai resolve | Luôn gán owner, dù là "TBD" |
| Section numbering sai sau khi thêm section | Gây nhầm lẫn khi reference | Dùng script để auto-update numbers |
| Dùng prose thay vì bảng cho happy path | Khó scan, dễ miss bước | Happy path phải là bảng Actor/Action/Result |

---

## 8. Template nhanh — kick-off một SRS mới

Khi PO yêu cầu viết SRS cho tính năng mới, chạy theo thứ tự:

```
1. [Research] Hỏi PO: competitor nào đang làm tính năng này?
2. [Research] Pull Sensor Tower reviews cho top 3–5 competitor
3. [Research] Cluster pain points → gửi insight summary cho PO
4. [Intake] Gửi form chất vấn (input mode, edit level, monetization, export, storage, language)
5. [Intake] Nhận câu trả lời → hỏi thêm câu kỹ thuật nếu cần
6. [Confirm] Tóm tắt toàn bộ decision → chờ PO confirm
7. [Outline] Đề xuất format tài liệu → chờ PO duyệt
8. [Write] Generate docx đầy đủ
9. [Review] PO review → nhận comment → update version
10. [Deliver] Deliver docx + md + (optional) upload Confluence
```

Tổng thời gian: Research (30–60 phút) + Intake (15–30 phút) + Writing (30–60 phút).

---

*Skill này được xây dựng từ thực tế viết SRS cho AI MV Maker — một iOS app gồm AI Song Generator, AI Song Cover, và AI MV Maker. Áp dụng được cho mọi mobile feature có AI generation component.*
