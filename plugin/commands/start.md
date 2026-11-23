---
description: Thiết lập và bắt đầu dùng Chat With Insight
---

Chạy quy trình sau bằng **tiếng Việt thân thiện, không dùng thuật ngữ kỹ thuật**.

1. Đọc `${CLAUDE_PLUGIN_ROOT}/knowledge/index.md` lấy `database_id` và tên schema hiện dùng — ĐỪNG tự nhớ
   hay hardcode hai giá trị này, luôn đọc lại từ file.
2. Thử gọi tool chạy SQL trên insight (`execute_sql`) với `{"database_id": <vừa đọc>, "sql": "select 1 as x"}`.
   - Nếu **không có tool nào của insight** trong phiên, hoặc gọi ra lỗi chưa xác thực / chưa kết nối →
     chạy mục "Chưa kết nối được insight" bên dưới rồi DỪNG. Đừng đoán mò, đừng bịa số.
3. Chạy tiếp một câu kiểm cấu trúc, dùng đúng schema vừa đọc ở bước 1:
   `select count(*)::int from information_schema.tables where table_schema = '<schema vừa đọc>'`
   Kết quả 0 → **đừng khẳng định "chưa thấy dữ liệu"** — có thể chỉ là đang trỏ nhầm Metabase hoặc
   schema đã đổi. Báo: *"Em kết nối được nhưng đếm ra 0 bảng — có thể đang trỏ nhầm Metabase
   hoặc schema đã đổi. Nhờ anh/chị báo bên kỹ thuật kiểm tra giúp em."*
4. Trình bày ✅/⚠ đúng hai dòng: *kết nối insight*, *đọc được dữ liệu XLeaders*.
5. In 5 câu hỏi mẫu:
   - "Lớp nào vừa khai giảng gần nhất, doanh thu bao nhiêu?"
   - "Tháng này có bao nhiêu học viên ghi danh chính thức?"
   - "Mỗi Sale chốt được bao nhiêu vé trong 3 tháng gần đây?"
   - "Lớp nào sắp khai giảng mà còn ít người đăng ký nhất?"
   - "Dựng cho tôi báo cáo doanh thu theo khoá học năm nay"
6. Kết bằng đúng 3 ý, mỗi ý một dòng:
   - Cứ hỏi bằng tiếng Việt bình thường, em sẽ tra số và nói rõ "Cách tính:".
   - Muốn có báo cáo riêng thì bảo em dựng — báo cáo nằm trong thư mục riêng của anh/chị trên insight, dùng
     đúng quyền tài khoản anh/chị đang có (em không mở rộng thêm được, cũng không tự siết lại được).
   - Không cần nữa thì bảo em gỡ — báo cáo vào Thùng rác Metabase, khôi phục lại được, không mất vĩnh viễn.

## Chưa kết nối được insight

**ĐỪNG bảo người dùng gõ `/mcp`** trừ khi họ nói rõ đang dùng bản terminal — khung chat của app Claude không
có lệnh đó, hướng dẫn sai làm họ bế tắc. Nói đúng một lần, ngắn, rồi dừng:

> *"Em chưa nối được vào insight ạ. Anh/chị mở **Customize → Plugins**, chọn **Xleader cwi**; ở mục
> **Connectors** có dòng **insight**, bấm **Connect** → **Continue** → **Add** (hoặc **Reconnect**) rồi đăng nhập bằng đúng tài khoản
> insight anh/chị vẫn dùng. Xong quay lại đây gõ `/start` giúp em."*

Kết nối insight đi kèm sẵn trong plugin — người dùng **không** cần tự thêm "custom connector", cũng không
cần nhờ ai. Nếu họ không thấy plugin **Xleader cwi** trong Customize → Plugins thì plugin chưa được cài: chỉ họ
tới hướng dẫn cài ở `https://github.com/askvasir/xleader-cwi-plugin`.

Nếu người dùng nói họ đang dùng **Claude Code bản terminal / VS Code**: gõ `/mcp`, chọn dòng `insight`, bấm
**Authenticate**, đăng nhập rồi đồng ý.

Không in ra id kỹ thuật, đường dẫn file, hay tên tool cho người dùng.
