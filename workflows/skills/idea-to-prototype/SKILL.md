---
name: idea-to-prototype
description: Quy trình khép kín 4 bước từ ý tưởng → research đối thủ → phân tích app mentor → vẽ HTML prototype → xuất ảnh PNG. BẮT BUỘC hỏi user xác nhận trước khi sang mỗi bước, không tự chạy liền mạch.
---

# Skill: Idea → Prototype (4-step gated workflow)

> Quy trình khép kín giúp PO đi từ ý tưởng thô đến HTML prototype + ảnh PNG. **Mỗi bước là một cổng (gate) — phải có xác nhận của user mới sang bước tiếp.**

---

## 0. Nguyên tắc tối thượng — KHÔNG BỎ QUA

**Sau MỖI bước phải hỏi user xác nhận trước khi sang bước tiếp.** Đây là rule cứng, không châm chước, kể cả khi user "có vẻ đang vội" hoặc "ngữ cảnh đã rõ".

Câu hỏi gate chuẩn:
> ✅ Đã xong **Bước [N] — [Tên bước]**.
> 👉 Tiếp tục sang **Bước [N+1] — [Tên bước tiếp]**? (trả lời `tiếp tục` / `ok` / `yes` để chạy, `dừng` để dừng, hoặc nhập yêu cầu chỉnh sửa)

**Các cụm trigger user dùng để cho phép đi tiếp:**
- `tiếp tục`, `tt`, `next`, `continue`, `ok`, `đồng ý`, `yes`, `y`, `proceed`, `go`

**Các cụm trigger user dùng để dừng/sửa:**
- `dừng`, `stop`, `wait`, `khoan`, `sửa lại`, `edit`, hoặc bất kỳ feedback chỉnh sửa nào

Nếu user yêu cầu sửa → quay lại bước hiện tại, sửa, rồi hỏi gate lại — KHÔNG tự ý nhảy bước.

**Anti-pattern tuyệt đối tránh:**
- ❌ Chạy bước 1 xong tự động chạy bước 2 vì "rõ ràng cần thiết"
- ❌ Gộp 2 bước vào một response "cho nhanh"
- ❌ Hỏi gate nhưng không chờ trả lời, tự đoán ý

---

## 1. Bước 1 — Research đối thủ + bảng đánh giá ý tưởng

### 1.1 Input
User nhập ý tưởng (1–3 câu mô tả tính năng/app muốn làm).

### 1.2 Hành động của AI

**1.2a — Tìm đối thủ:**
- Search App Store / Play Store / Sensor Tower theo keyword tính năng
- Tìm 5–10 app đang làm tính năng tương tự
- Với mỗi app: lấy tên, publisher, rating, % active marketing

**1.2b — Lấy số liệu (priority order):**

1. **Nếu connect được Sensor Tower MCP** (`mcp__claude_ai_Sensort__*`):
   - `search_apps(term)` → app_id
   - `get_sales_estimates(app_id)` → revenue 3 tháng gần nhất
   - `get_ad_creatives(app_id)` → có chạy Facebook Ads không
   - `get_active_users` → user activity
   - `get_app_details` → pricing model, IAP

2. **Nếu KHÔNG connect được**: fill bảng bằng research thủ công (App Store listing, web search, manual ad library check), đánh dấu `[ước lượng]` cho các số không xác thực được.

**1.2c — Output bảng đánh giá ý tưởng:**

| Cột | Ý nghĩa | Cách điền |
|---|---|---|
| Mô tả | Mô tả ý tưởng/feature đang đánh giá | Lấy từ input user, rút gọn 1 câu |
| Ưu tiên | Cao / Trung / Thấp | Đề xuất dựa trên rev × release × complexity |
| Rev (K) | Revenue tiềm năng / tháng (USD K) | Trung bình rev của top 3 đối thủ × % market share dự kiến |
| Release | ETA tới production | Estimate theo độ phức tạp (S=2 tuần / M=1 tháng / L=2–3 tháng) |
| Độ phức tạp | S / M / L / XL | S=clone UI, M=có AI service, L=AI + custom backend, XL=multi-feature |
| Số đối thủ rev >100K / 3 tháng gần nhất | Đếm app có sales_estimate ≥ $100K trong window này | Từ Sensor Tower; nếu không có, đếm app top chart trong category |
| Doanh thu đối thủ to nhất | Rev tháng cao nhất trong tập đối thủ | Lấy max từ Sensor Tower |
| Model giá | Free trial / Weekly / Monthly / Yearly / Lifetime | Đọc từ App Store listing IAP |
| Có chạy Facebook? | ✅ / ❌ | Sensor Tower ad creatives, hoặc check Meta Ad Library |
| Mua ngay ở OB? | ✅ / ❌ | Cài app, chụp lại, hoặc đọc review để xác nhận onboarding paywall |

**Format bảng cuối:**

```markdown
## Bảng đánh giá ý tưởng

| Mô tả | Ưu tiên | Rev (K) | Release | Độ phức tạp | # ĐT rev>100K/3T | Rev ĐT lớn nhất | Model giá | FB Ads | OB Paywall |
|---|---|---|---|---|---|---|---|---|---|
| [tóm tắt ý tưởng] | Cao | 50 | 1 tháng | M | 4 | $850K/tháng | Weekly + Yearly | ✅ | ✅ |

## Danh sách đối thủ chi tiết

| App | Publisher | Rating | Rev/tháng | Pricing | FB Ads | OB Paywall |
|---|---|---|---|---|---|---|
| [App A] | ... | 4.6 ⭐ | $850K | $4.99/wk | ✅ | ✅ |
| [App B] | ... | 4.2 ⭐ | $320K | $9.99/mo | ✅ | ❌ |
...

## Ghi chú nguồn
- Sensor Tower MCP: ✅ Connected / ❌ Not connected (dùng manual research)
- Số liệu update: [ngày]
- Các ô đánh dấu [ước lượng] = chưa có data trực tiếp
```

### 1.3 GATE 1 → hỏi user

Sau khi present bảng:
> ✅ Đã xong **Bước 1 — Research đối thủ**.
> Tôi đã tìm được [N] đối thủ và fill bảng đánh giá.
> 👉 Tiếp tục sang **Bước 2 — Chọn app mentor + phân tích sâu**?

**KHÔNG sang bước 2 nếu user chưa trả lời.**

---

## 2. Bước 2 — Chọn app mentor + phân tích tính năng

### 2.1 Hành động

**2.1a — Hỏi user chọn:**
> Trong [N] đối thủ ở trên, bạn muốn lấy app nào làm "thầy" để học theo? (gõ số thứ tự hoặc tên app)

Có thể đề xuất top 1–2 app dựa trên: rev cao + UI rõ ràng + phù hợp scope.

**2.1b — Sau khi user chọn, phân tích sâu:**

Phân tích app mentor theo template:

```markdown
## Phân tích app mentor: [Tên app]

### 1. Core Value Proposition
- 1–2 câu user mô tả app này khi review (lấy từ Sensor Tower reviews 5⭐)
- Tagline chính thức trên App Store

### 2. Feature Inventory (tính năng chính → phụ)
| # | Feature | Loại | Premium? | Mô tả |
|---|---|---|---|---|
| 1 | [Feature A] | Core | ❌ | ... |
| 2 | [Feature B] | Core | ✅ | ... |
| 3 | [Feature C] | Differentiator | ✅ | ... |
| 4 | [Feature D] | Hygiene | ❌ | ... |
...

### 3. UX Flow chính
Liệt kê 3–5 flow quan trọng nhất:
- Onboarding flow: [step 1] → [step 2] → ...
- Core action flow: [step 1] → ...
- Paywall flow: [trigger] → [offer screen] → ...

### 4. Monetization
- Free tier limit: ...
- Pricing tiers: ...
- Paywall placement: ngay onboarding / sau N lần dùng / paywall mềm tại feature
- Trial: ✅/❌ duration: ...

### 5. UI/UX Pattern nổi bật
- Style: minimal / playful / professional / dark-first
- Primary color cảm nhận: [hex gần đúng]
- Typography: SF Pro / custom font
- Iconography: outline / filled / 3D
- Animation: minimal / rich / Lottie-heavy
- Screen examples: [link App Store screenshots]

### 6. Pain points từ review (1⭐)
Top 3 pain points để mình *tránh*:
- [pain point A] — [count] reviews
- [pain point B] — [count] reviews
- [pain point C] — [count] reviews

### 7. Đề xuất "học gì" và "tránh gì"
**Học theo:**
- [pattern 1]
- [pattern 2]
- [pattern 3]

**Tránh:**
- [anti-pattern 1] (vì user complain)
- [anti-pattern 2]
```

### 2.2 GATE 2 → hỏi user

> ✅ Đã xong **Bước 2 — Phân tích app mentor [tên]**.
> 👉 Tiếp tục sang **Bước 3 — Vẽ HTML prototype**?

---

## 3. Bước 3 — Vẽ HTML/CSS prototype

### 3.1 Hành động bắt đầu — HỎI TRƯỚC khi vẽ

**HỎI ngay đầu Bước 3, không tự quyết:**

> Để vẽ prototype, bạn có muốn cung cấp:
> - 📷 **Ảnh UI mẫu**: screenshot của app mentor hoặc design mood-board (gửi link/path file). Tôi sẽ phân tích style và áp dụng.
> - 🎨 **Custom theo brand**: nếu chưa có ảnh, tôi sẽ propose 2–3 hướng style dựa trên feature + competitor.
>
> Bạn chọn cách nào? (gửi ảnh / custom)

### 3.2 Branch A — User cung cấp ảnh UI mẫu

**Dùng skill phân tích UI để extract style:**

1. Đọc ảnh bằng Read tool (nếu là path local) hoặc WebFetch (nếu URL)
2. Trích xuất:
   - Color palette (primary, secondary, bg, text, accent) — đề xuất hex
   - Typography (font feel: sans / serif, weight range)
   - Radius (button, card — tight 8px / medium 12-16px / pillowy 24px+)
   - Spacing density (compact / comfortable / spacious)
   - Iconography style (outline / filled / 3D)
   - Shadow / elevation style (flat / soft / dramatic)
   - Animation cues (nếu là video/GIF)
3. Generate `tokens.css` từ phân tích

**Output trước khi build:**
```markdown
## Style extraction từ ảnh mẫu
- Primary color: #6C5CE7 (tím nhạt)
- Bg: #FFFFFF / #F7F7FB
- Radius: 16px (button), 24px (card)
- Font: SF Pro feel, weight 400/600
- Shadow: soft, 0 4px 12px @ 8% opacity
- Spacing: comfortable (4pt grid, padding 16-24px)

→ Dùng các token này cho prototype.
```

### 3.3 Branch B — Custom

Đề xuất 2–3 hướng style dựa trên:
- Feature category (productivity → minimal, creative → playful, finance → professional)
- Đối tượng (gen-Z → vibrant, professional → restrained)
- Mentor app style (học theo nhưng đổi accent color để tránh clone)

User chọn 1 hướng → generate tokens.css.

### 3.4 Build prototype

**Tham chiếu skill `html-prototype-and-spec`** trong [UI/skills/](../../../UI/skills/html-prototype-and-spec/SKILL.md).

Tóm tắt nhanh các bước:
1. Tạo folder `prototype-[feature-slug]/`
2. Sinh `shared/tokens.css`, `shared/components.css`, `shared/animations.css`
3. Sinh `screens/01-*.html` … `screens/NN-*.html` cho từng màn trong flow
4. Wire navigation `data-nav` giữa các màn
5. Sinh `design-spec.md`
6. Sinh `index.html` làm hub

**Số màn tối thiểu cho mọi feature mobile:**
- Onboarding/intro (nếu có)
- Home / entry
- Create / input flow
- Loading / processing state
- Result / output
- Error / empty state
- Paywall (nếu monetization yêu cầu)

### 3.5 GATE 3 → hỏi user

> ✅ Đã xong **Bước 3 — Prototype HTML** ([N] màn).
> Bạn có thể `open prototype-[slug]/index.html` để xem.
> 👉 Tiếp tục sang **Bước 4 — Xuất ảnh PNG từng màn**?

---

## 4. Bước 4 — Export PNG từng màn

### 4.1 Hành động

**Cách 1 — Headless Chrome (preferred, không cần GUI):**

```bash
# Cài 1 lần
npm i -g puppeteer-cli
# Hoặc dùng: npx playwright install chromium
```

Script export `scripts/export-screens.js`:

```js
const puppeteer = require('puppeteer');
const fs = require('fs');
const path = require('path');

(async () => {
  const browser = await puppeteer.launch();
  const page = await browser.newPage();
  await page.setViewport({ width: 390, height: 844, deviceScaleFactor: 3 });

  const screensDir = path.resolve(__dirname, '../screens');
  const outDir = path.resolve(__dirname, '../exports');
  fs.mkdirSync(outDir, { recursive: true });

  for (const f of fs.readdirSync(screensDir).filter(n => n.endsWith('.html'))) {
    const url = 'file://' + path.join(screensDir, f);
    await page.goto(url, { waitUntil: 'networkidle0' });
    await page.screenshot({
      path: path.join(outDir, f.replace('.html', '.png')),
      fullPage: false,
    });
    console.log('exported', f);
  }
  await browser.close();
})();
```

Chạy: `node scripts/export-screens.js`

**Cách 2 — Playwright CLI (gọn hơn):**

```bash
npx playwright screenshot \
  --viewport-size=390,844 \
  --device-scale-factor=3 \
  prototype-[slug]/screens/01-home.html \
  prototype-[slug]/exports/01-home.png
```

Lặp cho từng file (hoặc viết script).

**Cách 3 — Nếu user chỉ có browser**: hướng dẫn dùng Chrome DevTools → Cmd+Shift+P → "Capture full size screenshot" cho từng màn. Slow nhưng không cần install.

### 4.2 Output

```
prototype-[slug]/
├── exports/
│   ├── 01-home.png       (1170×2532, @3x retina)
│   ├── 02-create.png
│   ├── 03-result.png
│   └── ...
└── ...
```

### 4.3 GATE 4 (kết thúc)

> ✅ **Hoàn thành toàn bộ workflow.**
> - Bảng đánh giá ý tưởng: `[link/path]`
> - Phân tích app mentor: `[link/path]`
> - HTML prototype: `prototype-[slug]/`
> - PNG exports: `prototype-[slug]/exports/`
>
> Bạn muốn:
> 1. Quay lại sửa bước nào?
> 2. Chạy tiếp skill khác (vd. `research-and-srs` để viết SRS chính thức)?
> 3. Kết thúc?

---

## 5. State machine — tóm tắt

```
[START]
  ↓ user nhập ý tưởng
[B1] Research đối thủ + bảng đánh giá
  ↓ ❓ GATE 1 — chờ user xác nhận
[B2] User chọn app mentor → phân tích sâu
  ↓ ❓ GATE 2
[B3] Hỏi có ảnh UI mẫu không → phân tích / custom → build HTML
  ↓ ❓ GATE 3
[B4] Export PNG từng màn
  ↓
[END] tổng kết + đề xuất next skill
```

Mỗi GATE = một message hỏi user, dừng lại chờ trả lời. Không có gate ngầm.

---

## 6. Checklist cho AI khi chạy workflow

Trước khi response cho user ở mỗi bước, self-check:

- [ ] Tôi đã hoàn thành đầy đủ output của bước hiện tại?
- [ ] Tôi đã hỏi gate đúng format (✅ Đã xong + 👉 Tiếp tục sang)?
- [ ] Tôi KHÔNG vô tình bắt đầu bước tiếp trong cùng response?
- [ ] Nếu user chỉ nói "ok" → tôi có đang ở đúng bước họ confirm không?
- [ ] Nếu user nói "sửa X" → tôi quay lại bước hiện tại sửa, KHÔNG nhảy bước?
- [ ] Output bước này có nằm trong file/folder rõ ràng để user đọc lại được?

---

## 7. Anti-patterns

| Anti-pattern | Tại sao sai | Làm đúng |
|---|---|---|
| Chạy 2 bước trong 1 response | Mất quyền kiểm soát của user, không thể can thiệp giữa chừng | 1 bước = 1 message + gate hỏi |
| Gate hỏi nhưng vẫn để code/output bước tiếp ở dưới | User bị overload, không biết đã sang chưa | Gate là dòng cuối response |
| Tự suy luận "user chắc đồng ý" | Không có confirmation thật | Luôn chờ trả lời |
| Skip bước 1 vì user đã có research sẵn | Mỗi bước có giá trị riêng (bảng đánh giá khác research dạng prose) | Hỏi user có muốn skip; nếu muốn, vẫn sinh bảng từ data có sẵn |
| Build HTML không hỏi style trước | Vẽ xong user mới nói "không phải style này" → vẽ lại | HỎI ảnh mẫu / custom TRƯỚC khi vẽ — Bước 3.1 |
| Export PNG nhưng quên @3x | Ảnh mờ khi xem trên retina | Luôn `deviceScaleFactor: 3` |

---

## 8. Cách invoke skill này

User có thể trigger bằng các cụm:
- "tôi có ý tưởng [X], chạy workflow đầy đủ"
- "chạy idea-to-prototype cho [feature]"
- "/idea-to-prototype" (nếu wired vào .claude/skills/)

Sau khi nhận trigger, AI lập tức bắt đầu **Bước 1**, fill bảng đánh giá, rồi GATE 1.

**Không hỏi "bạn muốn bắt đầu không?" trước Bước 1** — đã được trigger nghĩa là start. Gate đầu tiên là sau khi xong Bước 1.

---

*Skill này là orchestrator gọi qua các skill khác trong workspace: [research-and-srs](../../../docs/skills/research-and-srs/SKILL.md) (cho phần research), [html-prototype-and-spec](../../../UI/skills/html-prototype-and-spec/SKILL.md) (cho phần build UI), [visual-verdict](../../../UI/skills/visual-verdict/SKILL.md) (nếu cần QA so sánh ảnh sau).*
