# bow-skill-stripe

Bộ skill cho công nghệ **Stripe**, dùng với [bow-agent](https://github.com/Bow-T/bow-agent).
Theo hướng **mỗi công nghệ một repo**: bộ này không gắn với framework nào, một dự án tick nó CÙNG
với bộ framework của mình (Flutter, Next.js, React Native…) ở ô **Công nghệ**.

## Cấu trúc

```
bow-skill-stripe/
├── bow-skill.json        # manifest: id "stripe" + quy tắc tự nhận diện repo
├── THIRD_PARTY.md        # skill nào chép từ đâu, commit nào, giấy phép gì, đã sửa gì
├── LICENSES/             # văn bản giấy phép của nguồn bên ngoài
└── skills/
    ├── stripe-best-practices/   # ⬇ nguồn chính thức — chọn API, Connect, Billing, Tax, bảo mật (kèm 6 file references/)
    └── upgrade-stripe/          # ⬇ nguồn chính thức — nâng phiên bản API / SDK / Stripe.js
```

| Skill | Dùng khi | Nguồn |
| --- | --- | --- |
| `stripe-best-practices` | lên kế hoạch / viết / sửa / soát bất kỳ tích hợp Stripe nào: Checkout Sessions hay PaymentIntents, Connect (Accounts v2), subscription, Stripe Tax, Treasury, webhook, quản lý khoá API | Stripe (MIT) |
| `upgrade-stripe` | nâng phiên bản API Stripe, webhook endpoint, SDK phía server, Stripe.js, SDK mobile | Stripe (MIT) |

Cả hai chép **nguyên văn** từ [stripe/ai](https://github.com/stripe/ai) — không sửa câu chữ, không bỏ
file nào. Repo này không có skill do team viết. Chi tiết commit + những gì đã soát: `THIRD_PARTY.md`.

Hai skill này trước đây nằm trong bundle `bow-skill-flutter` (`monorepo/skills/`). Bản cũ của
`stripe-best-practices` có thêm một mục do team viết ("Stripe Notifications & Localization Best
Practices") nói về thông báo của MỘT sản phẩm; bản ở đây là nguyên văn của Stripe nên **không có**
mục đó — kiến thức riêng của sản phẩm để ở repo sản phẩm.

**Không lấy `stripe-projects`** (bundle cũ có): skill đó bảo agent chấp nhận điều khoản thay người
dùng, cấp phát dịch vụ bên thứ ba, và để Stripe CLI tự cài thêm một skill chưa ai soát vào
`.claude/skills/` của repo — xem `THIRD_PARTY.md`. Cũng không lấy 7 skill còn lại của nguồn:
`connect-recommend`, `connect-required-verification-information`, `metronome`, `stripe-apps`,
`stripe-directory`, `stripe-docs`, `stripe-pay`. Thêm sau: chép thư mục `skills/<tên>` từ nguồn (cùng commit ghi ở
`THIRD_PARTY.md`, hoặc cập nhật cả bộ lên commit mới), ĐỌC HẾT rồi mới thêm tên vào `skills` của
`bow-skill.json` (xếp ABC), thêm dòng vào `THIRD_PARTY.md`, tăng `version`, gắn tag.

## Tự nhận diện

bow-agent tự tick bộ này cho dự án chưa khai công nghệ khi repo khớp một trong các quy tắc ở
`targets.detect`:

| Quy tắc | Nghĩa |
| --- | --- |
| `package.json` chứa `"stripe"` | backend / web dùng SDK `stripe` (Node) |
| `package.json` chứa `"@stripe/` | web / React Native dùng `@stripe/stripe-js`, `@stripe/react-stripe-js`, `@stripe/stripe-react-native`… |
| `pubspec.yaml` chứa `flutter_stripe` | app Flutter dùng gói `flutter_stripe` |

Chỉ soi file git đã track, tìm cả trong thư mục con (monorepo: `apps/<tên>/package.json`).

**Giới hạn:** quy tắc chỉ soi `package.json` / `pubspec.yaml`. Edge function Supabase gọi Stripe qua
`npm:stripe` (hoặc `esm.sh/stripe`) ngay trong file `.ts` sẽ KHÔNG được nhận ra — khi đó tick tay bộ
này ở ô **Công nghệ**. Backend không phải Node (Python, Ruby, PHP, Go…) cũng không khớp.

## Đăng ký vào bow-agent

Thêm vào `stacks` của `~/.bow-agent/registry.json` (bow chỉ tải repo có trong danh sách này, đúng tag đã ghim):

```json
{ "id": "stripe", "label": "Stripe", "repo": "github.com/Bow-T/bow-skill-stripe", "ref": "v1.0.0" }
```

Lưu ý trong lúc chuyển tiếp: bundle ghép cũ "Flutter + Supabase" cũng mang hai skill cùng tên (bản cũ
hơn). Tick cả hai bộ thì bản của mục đứng TRƯỚC trong danh sách được dùng — đặt mục `stripe` lên
trước nếu muốn dùng bản ở đây.

Thử local trước khi đẩy GitHub: `"repo": "file:///đường/dẫn/tới/bow-skill-stripe"`.

## Phát hành phiên bản mới

Sửa skill qua PR → tăng `version` trong `bow-skill.json` → gắn tag (`git tag v1.0.1 && git push --tags`)
→ cập nhật `ref` trong registry. bow tải theo tag và cache theo `<id>@<ref>`, nên sửa nội dung mà giữ
nguyên tag sẽ KHÔNG được tải lại. Cập nhật skill từ nguồn bên ngoài: xem cuối `THIRD_PARTY.md`.
