---
name: html-prototype-and-spec
description: Vẽ prototype UI mobile bằng HTML/CSS có đủ animation + flow di chuyển giữa các màn, và xuất ra design spec file (color palette, typography, button radius, size) để dev đọc và implement chính xác
---

# Skill: HTML Prototype + Design Spec cho mobile app

> Skill này hướng dẫn cách tạo một bộ HTML/CSS prototype trực quan cho mobile app — có animation, có flow chuyển màn — kèm một file `design-spec.md` mô tả chi tiết tokens (màu, font, radius, spacing) để dev đọc từ đó code ra giao diện giống y hệt.

---

## 1. Nguyên tắc cốt lõi

**Prototype và Spec là 2 deliverable tách biệt nhưng phải khớp 100%.**
- Prototype = trải nghiệm trực quan (xem được, click được, có animation).
- Spec = nguồn sự thật cho dev (token, số đo, behavior).
- Mọi giá trị (màu, radius, font-size) phải đến từ token đã define trong spec — không hardcode trong HTML.

**Mobile-first, không responsive desktop.**
Khung viewport mặc định: 390×844 (iPhone 14/15) hoặc 360×800 (Android tham chiếu). Không cần media query cho desktop — đây là prototype mobile.

**Dùng CSS variables cho toàn bộ token.**
Tất cả màu, font-size, radius, spacing phải khai báo dưới dạng `--token-name` trong `:root`. Dev sẽ map 1-1 sang Swift/Kotlin/Flutter constants.

---

## 2. Cấu trúc deliverable

Mỗi feature/flow sẽ có folder riêng:

```
prototype-[feature-name]/
├── index.html              ← entry: navigation hub hoặc màn đầu
├── screens/
│   ├── 01-home.html
│   ├── 02-create.html
│   ├── 03-result.html
│   └── ...
├── shared/
│   ├── tokens.css          ← design tokens (CSS variables)
│   ├── components.css      ← button, card, input styles
│   └── animations.css      ← keyframes + transition presets
├── assets/
│   ├── icons/              ← SVG icons
│   └── images/
└── design-spec.md          ← spec file dev đọc để code
```

**Quy tắc đặt tên màn:** `[số thứ tự flow]-[tên-màn].html`. Số thứ tự khớp với Screen Inventory trong SRS (SCR-01 → `01-home.html`).

---

## 3. Quy trình 6 bước

### Bước 1 — Intake: hỏi PO trước khi vẽ

Trước khi mở editor, hỏi PO:
1. **Số màn hình + flow chính**: liệt kê SCR-01 → SCR-N và đường đi giữa chúng
2. **Reference visual**: app nào trên thị trường có style gần giống? (gửi link App Store hoặc screenshot)
3. **Brand color base**: primary color, có dark mode không?
4. **Typography preference**: SF Pro (iOS native), Inter (modern), hay font brand riêng?
5. **Mức độ animation**: minimal (chỉ transition cơ bản) / medium (có spring, scale) / rich (có Lottie, parallax)
6. **Có sẵn Figma chưa?** Nếu có → tham chiếu; nếu chưa → prototype HTML này sẽ là nguồn

Không bắt đầu vẽ khi PO chưa confirm 6 mục trên.

### Bước 2 — Define tokens TRƯỚC khi vẽ

Tạo `shared/tokens.css` đầu tiên. Đây là single source of truth.

**Color tokens** (tối thiểu):
```css
:root {
  /* Brand */
  --color-primary: #6C5CE7;
  --color-primary-pressed: #5A4BD1;
  --color-primary-disabled: #B8B0F0;

  /* Surface */
  --color-bg: #FFFFFF;
  --color-bg-secondary: #F7F7FB;
  --color-surface: #FFFFFF;
  --color-divider: #E8E8EE;

  /* Text */
  --color-text-primary: #1A1A2E;
  --color-text-secondary: #6B6B7B;
  --color-text-disabled: #B0B0BC;
  --color-text-on-primary: #FFFFFF;

  /* Semantic */
  --color-success: #00C853;
  --color-warning: #FFB300;
  --color-error: #FF3B30;
  --color-info: #007AFF;
}

[data-theme="dark"] {
  --color-bg: #0F0F1A;
  --color-bg-secondary: #1A1A2E;
  /* ... full dark palette */
}
```

**Typography tokens**:
```css
:root {
  --font-family: -apple-system, "SF Pro Text", "Inter", system-ui, sans-serif;

  --font-size-display: 32px;     /* H1, hero title */
  --font-size-title: 24px;       /* screen title */
  --font-size-headline: 20px;    /* section header */
  --font-size-body: 16px;        /* body text */
  --font-size-callout: 15px;     /* secondary body */
  --font-size-caption: 13px;     /* helper, label */
  --font-size-micro: 11px;       /* badge, tag */

  --font-weight-regular: 400;
  --font-weight-medium: 500;
  --font-weight-semibold: 600;
  --font-weight-bold: 700;

  --line-height-tight: 1.2;
  --line-height-normal: 1.4;
  --line-height-relaxed: 1.6;
}
```

**Spacing tokens** (4pt grid):
```css
:root {
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 12px;
  --space-4: 16px;
  --space-5: 20px;
  --space-6: 24px;
  --space-8: 32px;
  --space-10: 40px;
  --space-12: 48px;
}
```

**Radius tokens**:
```css
:root {
  --radius-sm: 8px;     /* tag, badge */
  --radius-md: 12px;    /* input, small card */
  --radius-lg: 16px;    /* button, card */
  --radius-xl: 24px;    /* hero card, sheet */
  --radius-pill: 999px; /* pill button */
  --radius-circle: 50%;
}
```

**Elevation tokens**:
```css
:root {
  --shadow-sm: 0 1px 2px rgba(0,0,0,0.04);
  --shadow-md: 0 4px 12px rgba(0,0,0,0.08);
  --shadow-lg: 0 12px 32px rgba(0,0,0,0.12);
}
```

**Motion tokens**:
```css
:root {
  --duration-fast: 150ms;
  --duration-normal: 250ms;
  --duration-slow: 400ms;

  --ease-standard: cubic-bezier(0.2, 0, 0, 1);
  --ease-emphasized: cubic-bezier(0.3, 0, 0, 1);
  --ease-spring: cubic-bezier(0.34, 1.56, 0.64, 1);
}
```

### Bước 3 — Build từng màn (HTML/CSS)

Mỗi màn 1 file `screens/XX-name.html`. Boilerplate:

```html
<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=390, initial-scale=1, maximum-scale=1">
  <title>SCR-01 · Home</title>
  <link rel="stylesheet" href="../shared/tokens.css">
  <link rel="stylesheet" href="../shared/components.css">
  <link rel="stylesheet" href="../shared/animations.css">
</head>
<body>
  <div class="device-frame">
    <div class="status-bar">9:41</div>
    <main class="screen" id="screen-home">
      <!-- content -->
    </main>
    <nav class="tab-bar">...</nav>
  </div>
  <script src="../shared/navigation.js"></script>
</body>
</html>
```

**Quy tắc:**
- `device-frame`: width 390px, height 844px, margin auto, border-radius 40px, background var(--color-bg)
- Mọi component dùng class trong `components.css`, không inline style
- Mỗi tương tác có thể click được (button, card, list item) — không để tĩnh

### Bước 4 — Components chuẩn (`components.css`)

**Button — phải có 4 state: default / pressed / disabled / loading**

```css
.btn {
  height: 52px;
  padding: 0 var(--space-6);
  border-radius: var(--radius-lg);
  font-size: var(--font-size-body);
  font-weight: var(--font-weight-semibold);
  transition: transform var(--duration-fast) var(--ease-standard),
              background var(--duration-fast) var(--ease-standard);
}
.btn-primary { background: var(--color-primary); color: var(--color-text-on-primary); }
.btn-primary:active { background: var(--color-primary-pressed); transform: scale(0.97); }
.btn-primary:disabled { background: var(--color-primary-disabled); }
```

Tương tự define: `.btn-secondary`, `.btn-ghost`, `.btn-icon`, `.btn-pill`.

**Input** — radius `--radius-md`, height 48px, padding `--space-4`, border 1px solid `--color-divider`, focus ring 2px primary.

**Card** — radius `--radius-lg`, padding `--space-4`, shadow `--shadow-md`.

**List item** — height 56px, divider bottom, ripple/highlight on press.

### Bước 5 — Animation & transitions (`animations.css`)

**3 lớp animation cần build:**

**1. Micro-interactions (component level)**
```css
.btn:active { transform: scale(0.97); }
.card:hover { transform: translateY(-2px); transition: var(--duration-normal); }
.list-item:active { background: var(--color-bg-secondary); }
```

**2. Screen transitions (giữa các màn)**
```css
@keyframes slide-in-right {
  from { transform: translateX(100%); }
  to   { transform: translateX(0); }
}
@keyframes fade-in-up {
  from { opacity: 0; transform: translateY(20px); }
  to   { opacity: 1; transform: translateY(0); }
}
.screen-enter { animation: slide-in-right var(--duration-normal) var(--ease-emphasized); }
.modal-enter  { animation: fade-in-up var(--duration-normal) var(--ease-spring); }
```

**3. State animations (loading, success, error)**
```css
@keyframes pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.5; }
}
@keyframes shimmer {
  0% { background-position: -200px 0; }
  100% { background-position: 200px 0; }
}
.skeleton { animation: shimmer 1.5s infinite linear;
            background: linear-gradient(90deg, #eee 0%, #f5f5f5 50%, #eee 100%);
            background-size: 200px 100%; }
```

**Mô tả rõ trong spec:** mỗi animation phải có entry trong `design-spec.md` với: tên, trigger, duration, easing, properties thay đổi.

### Bước 6 — Wire navigation flow

Tạo `shared/navigation.js` (vanilla JS, không framework):

```js
// Click bất kỳ phần tử có data-nav="path" để chuyển màn
document.addEventListener('click', (e) => {
  const target = e.target.closest('[data-nav]');
  if (target) {
    const path = target.dataset.nav;
    window.location.href = path;
  }
});
```

Trong HTML màn:
```html
<button class="btn btn-primary" data-nav="03-result.html">Generate</button>
```

**`index.html` là navigation hub:** liệt kê tất cả màn với thumbnail/link, để PO/dev/QA dễ duyệt qua từng state.

---

## 4. Design spec file (`design-spec.md`)

**Đây là file dev đọc để implement.** Cấu trúc bắt buộc:

```markdown
# Design Spec — [Feature Name]
> Version 1.0 · Source of truth cho dev. Tất cả số đo lấy từ đây.

## 1. Color Palette
| Token | Hex | RGB | Dùng cho |
|---|---|---|---|
| --color-primary | #6C5CE7 | 108,92,231 | CTA chính, brand accent |
| --color-bg | #FFFFFF | ... | Background chính |
| ... | | | |

(Có cả bảng dark mode riêng)

## 2. Typography
| Token | Size | Weight | Line-height | Dùng cho |
|---|---|---|---|---|
| display | 32px | 700 | 1.2 | Hero title màn onboarding |
| title | 24px | 600 | 1.2 | Title của màn |
| body | 16px | 400 | 1.4 | Body chính |
| ... | | | | |

Font family: `SF Pro Text` (iOS) / `Inter` (Android fallback)

## 3. Spacing
4pt grid. Tokens: 4 / 8 / 12 / 16 / 20 / 24 / 32 / 40 / 48

## 4. Radius
| Token | Value | Dùng cho |
|---|---|---|
| sm | 8px | Tag, badge |
| md | 12px | Input, small card |
| lg | 16px | Button, card chính |
| xl | 24px | Bottom sheet, hero card |
| pill | 999px | Pill button (filter, chip) |

## 5. Components

### 5.1 Button
| Property | Primary | Secondary | Ghost |
|---|---|---|---|
| Height | 52px | 52px | 44px |
| Padding ngang | 24px | 24px | 16px |
| Radius | 16px | 16px | 12px |
| Font | body / 600 | body / 600 | callout / 500 |
| BG default | --color-primary | --color-bg-secondary | transparent |
| BG pressed | --color-primary-pressed | divider | bg-secondary |
| BG disabled | --color-primary-disabled | --color-bg-secondary @ 50% | - |
| Text color | on-primary | text-primary | text-primary |

**State transitions:**
- Default → Pressed: scale 0.97, duration 150ms, easing standard
- Loading: spinner thay text, button vẫn giữ size, không cho click

### 5.2 Input
- Height: 48px · Padding: 16px · Radius: 12px
- Border: 1px solid --color-divider
- Focus: border 2px solid --color-primary, có 4px ring 20% opacity
- Error: border --color-error + error text 13px bên dưới

### 5.3 Card
- Padding: 16px · Radius: 16px · Shadow: --shadow-md
- Press feedback: scale 0.98, shadow giảm xuống --shadow-sm

(Tiếp tục cho List item, Tab bar, Modal, Toast, Snackbar, Bottom sheet...)

## 6. Animation Specs

| Animation | Trigger | Duration | Easing | Properties |
|---|---|---|---|---|
| Button press | :active | 150ms | standard | transform scale 1 → 0.97 |
| Screen push | navigation | 250ms | emphasized | translateX 100% → 0 |
| Modal in | open modal | 250ms | spring | opacity + translateY 20 → 0 |
| Skeleton shimmer | loading state | 1500ms loop | linear | background-position |
| Success check | task complete | 400ms | spring | scale 0 → 1.1 → 1 |

## 7. Screen-by-screen specs

### SCR-01 — Home
- Status bar: light content, transparent
- Header: 56px height, title "Home" font-title, padding 16px
- Content: padding 16px, scroll vertical
- Tab bar: 80px height (gồm safe area 34px), 4 items
- Reference HTML: `screens/01-home.html`

(Lặp lại cho mọi màn)

## 8. Asset specs
- Icons: SVG 24×24 stroke 1.5px, color currentColor để inherit
- Images: WebP, @2x và @3x, lazy load
- Lottie (nếu có): list từng file + size + loop behavior
```

---

## 5. Output cuối cùng deliver cho dev

**3 thứ:**
1. Folder `prototype-[feature]/` đầy đủ HTML/CSS/JS — chạy được offline bằng `open index.html`
2. File `design-spec.md` — tài liệu dev đọc
3. Video screen recording 30–60s đi hết flow chính (xuất bằng QuickTime hoặc dùng Cleanshot) — để PO trình stakeholder

**Khi handoff cho dev:**
> "Mở `index.html` trong Chrome, mở DevTools → device toolbar → iPhone 14 Pro. Click qua các màn để xem flow. Mọi số đo lấy từ `design-spec.md`, không lấy từ HTML."

---

## 6. Checklist trước khi handoff

### Checklist tokens
- [ ] Mọi màu trong HTML đều dùng `var(--color-*)` — không có hex literal
- [ ] Mọi font-size dùng `var(--font-size-*)` — không có px literal
- [ ] Mọi radius dùng `var(--radius-*)` — không có px literal
- [ ] Đã có dark mode token (kể cả Phase 1 chưa làm dark mode)

### Checklist screens
- [ ] Mỗi screen ID trong SRS có 1 file `.html` tương ứng
- [ ] Mỗi state khác (loading / empty / error) có file riêng `XX-name-loading.html` v.v.
- [ ] Status bar + safe area được mock đúng (notch iPhone)
- [ ] Tất cả button/card click được, có data-nav

### Checklist animation
- [ ] Mọi button có press feedback (scale + bg change)
- [ ] Có ít nhất 1 screen transition giữa các màn
- [ ] Loading state có skeleton hoặc spinner
- [ ] Success state có animation feedback (check mark, confetti, v.v.)
- [ ] Mọi animation có entry trong `design-spec.md` Section 6

### Checklist spec file
- [ ] Color palette đầy đủ cả light + dark
- [ ] Typography table có size + weight + line-height
- [ ] Component spec có đủ default / pressed / disabled state
- [ ] Mỗi screen có section riêng + link đến HTML file
- [ ] Animation table khớp với CSS thực tế (không lệch duration/easing)

---

## 7. Anti-patterns cần tránh

| Anti-pattern | Tại sao sai | Làm đúng |
|---|---|---|
| Hardcode `#6C5CE7` trong HTML | Dev không biết đâu là token chính, đâu là one-off | Luôn `var(--color-primary)` |
| Vẽ HTML trước khi define tokens | Tokens sẽ "tự sinh" lộn xộn, sửa lại tốn thời gian | Bước 2 (tokens.css) phải xong trước Bước 3 |
| Animation chỉ có trong CSS, không ghi vào spec | Dev implement theo cảm giác, không nhất quán | Mọi animation phải có entry trong spec table |
| Spec viết "button bo góc lớn" | Dev không biết bao nhiêu px | Spec phải có số: "border-radius: 16px (--radius-lg)" |
| 1 file HTML duy nhất với tất cả màn | Khó navigate, không phản ánh flow thật | Mỗi màn 1 file, nối bằng data-nav |
| Quên safe area / notch / tab bar | Layout mobile sẽ bị tràn khi dev port sang native | Device frame phải mock đủ status bar 44px + home indicator 34px |
| Spec và HTML lệch nhau (sửa HTML quên update spec) | Dev đọc spec ra số khác, code sai | Quy ước: sửa spec TRƯỚC, sửa HTML SAU |

---

## 8. Template kick-off — bắt đầu một prototype mới

```
1. [Intake] Hỏi PO 6 câu (xem Bước 1)
2. [Setup] Tạo folder prototype-[feature]/ với cấu trúc chuẩn
3. [Tokens] Define tokens.css (colors / typography / spacing / radius / motion)
4. [Components] Build components.css với button/input/card/list-item
5. [Screens] Build từng file screens/XX-*.html theo Screen Inventory trong SRS
6. [Animations] Add animations.css với 3 lớp (micro / screen / state)
7. [Navigation] Wire data-nav cho tất cả interactive element
8. [Spec] Generate design-spec.md từ tokens + components + screens + animations
9. [Review] PO duyệt prototype + spec → nhận feedback → update version
10. [Handoff] Deliver folder + spec.md + video walkthrough cho dev
```

Tổng thời gian dự kiến: Tokens + Components (1–2h) + Screens (1h/màn) + Animations (1–2h) + Spec (1h).

---

## 9. Khi nào dùng skill này vs Figma

| Tình huống | Dùng |
|---|---|
| PO muốn nhanh chóng test flow + animation thực tế | **HTML prototype** (skill này) |
| Cần collaborate với designer chuyên nghiệp | **Figma** |
| Dev muốn copy code CSS thẳng | **HTML prototype** |
| Cần component library, design system phức tạp | **Figma + Code Connect** |
| PO không có designer support, tự vibecode prototype | **HTML prototype** |

Skill này phù hợp nhất khi PO làm việc với Claude/Cursor để tự dựng visual prototype mà không cần design tool chuyên dụng.

---

*Skill này được thiết kế cho PO mobile làm việc trực tiếp với LLM để tạo prototype trước khi handoff cho dev. Prototype HTML không thay thế Figma — nó là deliverable bổ sung khi cần test flow + animation nhanh và xuất spec đo được cho dev.*
