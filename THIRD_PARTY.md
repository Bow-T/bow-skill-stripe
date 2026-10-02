# Nguồn bên ngoài

Skill chép từ repo khác được ghi ở đây: lấy từ đâu, tại commit nào, giấy phép gì, và đã sửa gì.
Bản chép là **ảnh chụp tại một commit** — bow-agent không tự kéo bản mới của nguồn. Muốn cập nhật
thì làm lại các bước ở cuối file, đọc lại nội dung, rồi mới gắn tag.

Cả hai skill của repo này đều từ nguồn ngoài; không có skill nào do team viết.

| Skill | Nguồn | Commit | Giấy phép | Đã sửa |
| --- | --- | --- | --- | --- |
| `stripe-best-practices` | [stripe/ai](https://github.com/stripe/ai) `skills/stripe-best-practices` | `27db051dba7cee2e13d2dbf6d65939cf412f5b3c` (2026-10-02) | MIT | Không — nguyên văn, đủ `SKILL.md` + 6 file `references/` |
| `upgrade-stripe` | [stripe/ai](https://github.com/stripe/ai) `skills/upgrade-stripe` | `27db051dba7cee2e13d2dbf6d65939cf412f5b3c` (2026-10-02) | MIT | Không — nguyên văn |

Văn bản giấy phép: [`LICENSES/stripe-ai-MIT.txt`](LICENSES/stripe-ai-MIT.txt) (chép từ `LICENSE` ở gốc nguồn).

Lấy từ thư mục `skills/` ở GỐC nguồn. Các bản dưới `providers/{claude,codex,cursor,agent-plugins}/plugin/skills/`
là bản trùng cho từng công cụ — đã `diff -r` với bản gốc tại commit trên: giống hệt.
Hai skill không có số phiên bản riêng trong frontmatter; plugin `stripe` của nguồn tại commit này ghi `0.11.6`.

## Vì sao không bỏ gì

Hai thư mục skill chỉ có Markdown: không `CHANGELOG`, không file dành cho người soạn skill
(`_template`, `_contributing`…), không ảnh, và không mục nào bảo agent gửi phản hồi / mở issue. Không
có gì thuộc diện được phép bỏ, nên chép đủ và không sửa chữ nào.

## Đã soát gì trước khi chép (2026-10-02)

- Không có script / file thực thi nào trong hai skill — chỉ Markdown (8 file), không file nào có quyền chạy.
- Không có ký tự vô hình (tag Unicode, zero-width, bidi). Ký tự ngoài ASCII chỉ là dấu nháy cong,
  gạch dài, dấu ba chấm, mũi tên `→`.
- Đường link trỏ tới `docs.stripe.com`, `dashboard.stripe.com`, `support.stripe.com`, `stripe.com`,
  `js.stripe.com`, `api.stripe.com`. Link ngoài miền Stripe DUY NHẤT: một link tài liệu `vercel.com`
  (biến môi trường "sensitive") trong `stripe-best-practices/references/security.md`.
- **Lệnh cài đặt kéo từ mạng** skill bảo agent chạy (không có `curl … | sh`):
  - `stripe-best-practices`: `npm i -g @stripe/cli` rồi `stripe sandbox create` (tạo tài khoản sandbox
    + khoá API test, không cần đăng ký), `stripe sandbox claim`.
  - `upgrade-stripe`: `npm install @stripe/stripe-js`, `npm update stripe`, `pip install --upgrade stripe`
    (ví dụ trong checklist nâng cấp), một lệnh `curl https://api.stripe.com/v1/customers` mẫu để thử header `Stripe-Version`.
- **Nạp chỉ dẫn từ mạng lúc chạy**: `stripe-best-practices/references/billing.md` bảo agent dùng skill
  `metronome`, "nếu chưa cài thì đọc từ `https://docs.stripe.com/.well-known/skills/metronome/SKILL.md`".
  Repo này cố ý không mang `metronome`, nên với câu hỏi về usage-based billing agent sẽ đi đọc file đó
  từ mạng — nội dung không ghim theo commit.
- **Link trông như hỏng do khâu đồng bộ của nguồn** (giữ nguyên vì chép nguyên văn): trong
  `stripe-best-practices/SKILL.md`, 11 link tới file kèm theo ghi dạng
  `[references/payments.md](https://docs.stripe.com/references/payments.md)` — chữ hiển thị là đường dẫn
  file kèm theo (đúng), còn URL nhiều khả năng không phải trang tài liệu thật (chưa kiểm — không gọi mạng);
  `references/tax.md` có 4 link `https://docs.stripe.com/undefined.md#…` (neo nội bộ của chính file đó).
- `upgrade-stripe` và `stripe-best-practices` mang số phiên bản "mới nhất" tại ngày sinh skill
  (API `2026-09-30.endive`, bảng phiên bản SDK). Ảnh chụp này sẽ cũ dần — `upgrade-stripe` tự dặn agent
  tra lại trên `docs.stripe.com`, `stripe-best-practices` thì không.

## Đã xem nhưng KHÔNG lấy

| Skill | Vì sao |
| --- | --- |
| `stripe-projects` | Bundle cũ có. Bản hiện tại bảo agent chạy `stripe projects init --accept-tos --yes` (chấp nhận điều khoản thay người dùng), cấp phát dịch vụ bên thứ ba, đặt bí mật qua `--value <value>` ngay trên dòng lệnh (lộ trong thẻ duyệt + audit), và CLI tự cài skill `stripe-projects-cli` vào `.claude/skills/` của repo — nội dung đi theo plugin CLI, không ghim commit, không ai soát. Frontmatter còn khai `allowed-tools: Bash(stripe *)`; nó có làm lệnh vượt cổng duyệt của bow hay không thì CHƯA kiểm. Muốn dùng lại: thử trên repo nháp trước |
| `metronome` | Tự nó trỏ tiếp tới 8 skill khác tải từ `docs.metronome.com/.well-known/skills/…` lúc chạy, trong đó nhiều skill GHI vào môi trường thật (tạo khách hàng, hợp đồng, kết thúc subscription Stripe). Chỉ đáng thêm khi dự án thật sự dùng Metronome |
| `stripe-apps` | Có `references/feedback.md` bảo agent chạy `stripe feedback …` gửi báo cáo về Stripe — nếu thêm sau phải bỏ file đó |
| `connect-recommend`, `connect-required-verification-information`, `stripe-directory`, `stripe-docs`, `stripe-pay` | Chưa có dự án cần; chưa đọc kỹ |

## Cập nhật từ nguồn

```bash
git clone --depth 1 https://github.com/stripe/ai /tmp/up && cd /tmp/up && git rev-parse HEAD
# so từng skill với bản ở đây, ĐỌC phần khác biệt (đặc biệt lệnh / link / frontmatter / mục mới), rồi chép đè
for s in stripe-best-practices upgrade-stripe; do diff -r /tmp/up/skills/$s skills/$s; done
```
Sửa bảng ở trên (commit + ngày), tăng `version` trong `bow-skill.json`, gắn tag mới.
