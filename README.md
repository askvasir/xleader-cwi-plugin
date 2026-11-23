# Chat With Insight (CWI)

Hỏi số liệu kinh doanh XLeaders bằng tiếng Việt và tự dựng báo cáo riêng trên `insight.xteam.edu.vn`, ngay
trong Claude, không cần biết SQL.

Repo này **chỉ chứa hướng dẫn** để Claude biết cách tính từng chỉ số. Repo **không** chứa số liệu nào. Mọi con
số Claude trả lời đều được đọc trực tiếp từ insight, bằng chính tài khoản insight của bạn: chưa đăng nhập
insight thì không có số nào cả.

## Cài đặt (tài khoản Claude cá nhân, khoảng 2 phút, làm một lần)

Làm trên app Claude (máy tính) hoặc trên claude.ai, cùng một tài khoản là được.

1. Mở **Customize → Plugins**, bấm **Add** → **Add marketplace** → **Add from a repository**, dán đúng dòng sau
   vào ô URL, để nguyên công tắc **Sync automatically** đang bật, rồi bấm **Sync**:
   ```
   askvasir/xleader-cwi-plugin
   ```
2. Trong danh sách vừa hiện, chọn **Xleader cwi** rồi bấm **Add**.
3. Vẫn ở trang plugin **Xleader cwi**, mục **Connectors** có dòng **insight**: bấm **Connect** → **Continue** →
   **Add** (giữ nguyên các lựa chọn Claude điền sẵn). Claude mở trang đăng nhập insight: đăng nhập bằng tài khoản
   insight bạn vẫn dùng rồi bấm đồng ý.
4. Từ đây bản mới tự về máy (công tắc **Sync automatically** ở bước 1 lo việc này), bạn không phải làm gì thêm.
5. Mở một cuộc chat mới, gõ `/start`. Thấy hai dấu ✅ là xong.

Bạn **không** cần tài khoản GitHub, và cũng **không** cần tự thêm "custom connector": kết nối insight đi kèm
sẵn trong plugin.

### Nếu bạn dùng Claude Code (terminal / VS Code)

```
/plugin marketplace add askvasir/xleader-cwi-plugin
/plugin install xleader-cwi@xleader-cwi
/mcp
```

Trong màn hình `/mcp`, chọn dòng `insight` rồi bấm **Authenticate**. Bật **auto update** cho marketplace
`xleader-cwi` trong màn hình `/plugin`.

## Dùng thế nào

Cứ hỏi bình thường:
- *"Doanh thu lớp <mã lớp> là bao nhiêu?"*
- *"Tháng này có bao nhiêu học viên ghi danh chính thức?"*
- *"Lớp nào sắp khai giảng mà còn ít người đăng ký nhất?"*
- *"Dựng cho tôi báo cáo doanh thu theo khoá học năm nay"*

Mỗi câu trả lời đều kèm dòng **"Cách tính:"** để bạn biết con số đó nghĩa là gì.

## An toàn

- Plugin đăng nhập insight bằng **chính tài khoản của bạn** (đăng nhập trên trang insight, plugin không thấy
  và không lưu mật khẩu). Bạn xem được gì trên insight qua plugin thì cũng đúng bằng những gì tài khoản của bạn
  vốn xem được: plugin không mở rộng thêm quyền.
- Báo cáo plugin dựng cho bạn nằm trong thư mục riêng của bạn trên insight. Plugin không tự sửa báo cáo chung
  của công ty. Gỡ báo cáo thì báo cáo vào Thùng rác, khôi phục lại được.

## Gặp trục trặc

- `/start` báo chưa kết nối: làm lại bước 3.
- Bước 1 báo trùng tên **xleader-cwi**: máy bạn còn bản cài thử cũ. Vào **Add → Manage marketplaces**, bấm **…**
  cạnh **xleader-cwi** → **Remove**, rồi làm lại từ bước 1.
- Trước đây bạn từng tự thêm một connector trỏ tới insight (vd tên "xleader insight"): gỡ nó đi ở
  **Customize → Plugins**, chỉ giữ dòng **insight** trong plugin, để khỏi phải đăng nhập hai lần.
- Muốn cập nhật ngay, không chờ: **Add → Manage marketplaces → … → Check for updates**.
