# BaseClaudeForPo

Workspace setup cho Product Owner. Gồm 3 khu vực chính: **UI**, **docs**, **notes**.
Mỗi khu vực có thư mục `skills/` chứa các skill tham khảo (nội dung skill giữ nguyên tiếng Anh).

---

## 📁 Cấu trúc

```
BaseClaudeForPo/
├── workflows/  — quy trình end-to-end nhiều bước (orchestrator gọi qua skill khác)
├── UI/         — nơi làm việc liên quan đến giao diện, mockup, QA design
├── docs/       — nơi viết spec, PRD, requirement, knowledge base
├── notes/      — nơi ghi chú nhanh, lưu lại kiến thức sau mỗi session
└── code/       — guideline/skill khi PO tự vibecode hoặc review code dev
```

---

## 🔁 workflows/skills

Các quy trình khép kín nhiều bước (orchestrator) — gọi qua các skill khác trong workspace.

| Skill | Công dụng |
|---|---|
| [idea-to-prototype](workflows/skills/idea-to-prototype) | **(custom — tiếng Việt)** Workflow 4 bước có gate: (1) Research đối thủ + bảng đánh giá ý tưởng (Sensor Tower hoặc manual fallback) → (2) Chọn app mentor + phân tích feature/UX/monetization → (3) Vẽ HTML prototype (hỏi có ảnh UI mẫu để học style không, nếu có thì extract style) → (4) Export PNG từng màn @3x retina. **Bắt buộc hỏi user trước khi sang mỗi bước.** |

---

## 🎨 UI/skills

Khu vực dành cho công việc UI/UX: dựng ý tưởng, so sánh thiết kế với bản build.

| Skill | Công dụng |
|---|---|
| [html-prototype-and-spec](UI/skills/html-prototype-and-spec) | **(custom — tiếng Việt)** Quy trình dựng prototype mobile bằng HTML/CSS có animation + flow chuyển màn, kèm `design-spec.md` (color palette, typography, button radius, size, animation specs) để dev đọc và implement chính xác. Dùng khi PO tự vibecode UI mà không có designer Figma. |
| [visual-verdict](UI/skills/visual-verdict) | So sánh screenshot UI thực tế với ảnh reference (Figma/design), trả về verdict dạng JSON để biết cần sửa gì. Dùng khi QA giao diện sau khi dev build xong. |
| [brainstorming](UI/skills/brainstorming) | Đối thoại kiểu cộng tác để biến ý tưởng thô thành design/spec rõ ràng. Dùng trước khi bắt tay vẽ flow mới hoặc thêm tính năng. |

---

## 📝 docs/skills

Khu vực viết tài liệu sản phẩm: PRD, spec, knowledge base, research.

| Skill | Công dụng |
|---|---|
| [research-and-srs](docs/skills/research-and-srs) | **(custom — tiếng Việt)** Quy trình chuẩn cho PO mobile: research thị trường qua Sensor Tower → chất vấn PO bằng form có cấu trúc → viết SRS đầy đủ (Goal, Flow, Screens, BR, FR-GWT, NFR, Open Items). Dùng khi cần spec hoá một tính năng mobile mới. |
| [writing-plans](docs/skills/writing-plans) | Viết plan/spec nhiều bước có cấu trúc. Dùng khi đã có requirement và cần biến thành kế hoạch triển khai chi tiết. |
| [deep-interview](docs/skills/deep-interview) | Phỏng vấn kiểu Socratic để bóc tách yêu cầu mơ hồ thành spec đo được. Dùng khi user/stakeholder đưa ra ý tưởng còn chung chung. |
| [deep-dive](docs/skills/deep-dive) | Pipeline 2 bước: trace (truy nguyên nhân) → deep-interview (chốt yêu cầu). Dùng cho vấn đề phức tạp cần điều tra sâu trước khi viết spec. |
| [wiki](docs/skills/wiki) | Knowledge base dạng markdown bền vững qua nhiều session. Dùng để lưu kiến thức sản phẩm/domain dài hạn, tra cứu lại khi cần. |

---

## 🗒️ notes/skills

Khu vực ghi chú nhanh, chốt lại điều đáng nhớ sau mỗi buổi làm việc.

| Skill | Công dụng |
|---|---|
| [remember](notes/skills/remember) | Soát lại kiến thức/quyết định trong session và quyết định cái gì nên lưu vào project memory, notepad, hay docs chính thức. Dùng cuối mỗi session để không mất context. |

---

---

## 💻 code/skills

Khu vực dành cho lúc PO tự vibecode (prompt Claude/Cursor để build prototype) hoặc review code dev. Tập hợp guideline để code AI sinh ra gọn, đúng yêu cầu, không over-engineer.

| Skill | Công dụng |
|---|---|
| [karpathy-guidelines](code/skills/karpathy-guidelines) | 4 nguyên tắc của Andrej Karpathy để hạn chế lỗi phổ biến của LLM khi viết code: (1) Think before coding — nói rõ assumption thay vì đoán mò, (2) Simplicity first — viết code tối thiểu giải quyết vấn đề, (3) Surgical changes — chỉ sửa đúng chỗ cần, không "tiện tay" refactor, (4) Goal-driven execution — đặt success criteria rõ trước khi code. Có kèm [EXAMPLES.md](code/skills/karpathy-guidelines/EXAMPLES.md) minh hoạ. |

Nguồn: [forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills)

---

## ⚙️ Lưu ý kỹ thuật

- Các skill ở đây chỉ là **kho tham khảo theo chủ đề**, chưa được Claude Code tự load.
- Để Claude Code tự nhận skill và gọi qua `/skill-name`, cần copy/symlink skill vào:
  - `.claude/skills/` — chỉ áp dụng cho project này
  - `~/.claude/skills/` — áp dụng global cho mọi project
- Nguồn skill: `/Users/trantheanh/Downloads/claude-skill/vibe-code-skills/`
  - `superpowers/skills/` — bộ skill nền tảng
  - `oh-my-claudecode/skills/` — bộ skill mở rộng (OMC)
