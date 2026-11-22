---
name: dung-bao-cao
description: Dùng khi người dùng muốn tạo, sửa, hoặc gỡ một báo cáo/dashboard riêng trên insight — "dựng cho tôi báo cáo…", "thêm cột X vào báo cáo hôm trước", "bỏ biểu đồ này đi".
---

# Dựng báo cáo riêng trên insight

Báo cáo được tạo trong **thư mục riêng của chính người dùng**, mặc định. Mọi thao tác đi qua các tool của
insight (`search`, `read_resource`, `create_question`, `update_dashboard`…). Các tool đó có thể tới từ plugin
hay từ connector của Claude nên **tên đầy đủ khác nhau tuỳ máy** — cứ dùng tool đang có trong phiên, đừng đòi
đúng một tên server. Không có tool nào của insight → nói là chưa kết nối và bảo người dùng gõ `/start`.

## Đọc đúng LÁT CẮT, đừng đọc cả kho

| Câu hỏi về | Đọc đúng file này |
|---|---|
| Doanh thu | `${CLAUDE_PLUGIN_ROOT}/knowledge/chi-so/doanh-thu.md` |
| Số vé bán | `${CLAUDE_PLUGIN_ROOT}/knowledge/chi-so/so-ve.md` |
| Ghi danh / sĩ số / học viên | `${CLAUDE_PLUGIN_ROOT}/knowledge/chi-so/ghi-danh.md` |
| Lead / phễu / nguồn cơ hội | `${CLAUDE_PLUGIN_ROOT}/knowledge/chi-so/co-hoi-tang-pheu.md` |
| Kênh / nguồn (Paid Sale · Organic Sale · Nguồn chưa rõ) | `${CLAUDE_PLUGIN_ROOT}/knowledge/chi-so/co-hoi-tang-pheu.md` |
| Lớp học / lịch / giảng viên | `${CLAUDE_PLUGIN_ROOT}/knowledge/chi-so/lop-hoc.md` |
| Công ty đối tác | `${CLAUDE_PLUGIN_ROOT}/knowledge/chi-so/doi-tac-doanh-nghiep.md` |
| Doanh số của MỘT Sale cụ thể | + `${CLAUDE_PLUGIN_ROOT}/knowledge/chua-chot/quy-ket-sale.md` |
| Người dùng nói số lệch sổ sách | + `${CLAUDE_PLUGIN_ROOT}/knowledge/chua-chot/dinh-nghia-doanh-thu.md` |
| Không khớp chỉ số nào ở trên | `${CLAUDE_PLUGIN_ROOT}/knowledge/quy-tac-chung.md` |
| Cần tên bảng, tên schema, database_id | `${CLAUDE_PLUGIN_ROOT}/knowledge/index.md` |
| Cần đếm lại bằng đường độc lập / danh sách 7 Model có sẵn | `${CLAUDE_PLUGIN_ROOT}/knowledge/doi-soat.md` |

Đọc ĐÚNG một hàng. Không đọc cả thư mục. Không cần đọc `quy-tac-chung.md` khi file chỉ số đã trả lời đủ —
nhưng nếu câu hỏi chạm tới thứ file chỉ số không nói (một cách phân loại, một cột lạ, một khoảng trống dữ
liệu), thì đọc.

## Trước khi dựng

Chạy một truy vấn thử (`execute_sql`, `database_id: 2`) trên bảng liên quan để chắc cấu trúc còn đúng —
đừng dựng bừa trên giả định cũ.

## Hỏi đúng 3 câu nghiệp vụ (đừng hỏi hơn)

1. Báo cáo này trả lời câu hỏi gì?
2. Nhìn theo lát cắt nào — lớp, Sale, khoá học, hay thời gian?
3. Cần lọc gì không (khoảng thời gian, một lớp cụ thể)?

## Dựng

1. Ưu tiên thẻ dựng trên Model đã có sẵn trên insight (xem danh sách ở `knowledge/doi-soat.md`) — thẻ kiểu
   này người dùng bấm vào xem được bản ghi gốc. Dùng `construct_query`/`create_question` trỏ nguồn vào Model
   đó; chỉ viết SQL tay (`construct_native_query`/`execute_sql`) khi Model không đáp ứng được lát cắt cần.
2. Chỉ 5 kiểu hiển thị: `scalar` · `table` · `bar` · `line` · `funnel`.
3. **Chạy thử NGAY từng thẻ sau khi tạo** bằng `execute_question`. Thẻ lỗi thì sửa — **không bao giờ bàn
   giao báo cáo còn thẻ đỏ**. Thẻ có biến `{{…}}` thì `execute_question` từ chối ("takes parameters") —
   chạy thử bằng `execute_sql` với giá trị thật thay vào, đừng bỏ qua bước chạy thử.
4. **Kiểm tra chéo bắt buộc:** với mỗi con số KPI, đếm lại bằng **một đường độc lập** (thẻ dựng trên Model
   thì đếm lại bằng `execute_sql` từ bảng gốc theo đúng công thức trong `chi-so/*.md`, và ngược lại).
   - Khớp → nói ✅ "đã đối chiếu, số khớp".
   - Lệch → nói thẳng: *"⚠ số này em chưa chắc — cách đếm khác ra <X>, lệch <Y>."* **Tuyệt đối không im
     lặng.**
5. Cần bộ lọc → đọc "Bộ lọc dashboard" bên dưới **trước khi dựng thẻ đầu tiên**. Chọn sai kiểu thẻ ở bước 1
   là phải dựng lại từ đầu.

## Bộ lọc dashboard

MCP **không tạo, không đọc, không sửa được** bộ lọc. Đo 08/09: không tool nào có `parameters`; `dashcards`
chỉ có `add`/`remove`/`move`; `read_resource` trên dashboard chỉ trả về danh sách thẻ. Trường gửi thừa bị
**vứt âm lặng, vẫn trả 200**.

⛔ Gửi `parameters`/`parameter_mappings` vào bất kỳ tool nào rồi coi là xong — nó "thành công" mà bộ lọc
không hề tồn tại.
⛔ Nhét hướng dẫn vào `description` của dashboard rồi bỏ đó (từng có dashboard mắc đúng lỗi này).

Người dùng bấm MỘT lần cho mỗi dashboard, cwi lo toàn bộ phần còn lại:

1. **Dựng thẻ để Metabase tự nối được.** Thẻ MBQL (`construct_query` trỏ Model/bảng), mọi thẻ phơi **cùng
   một cột lọc**. Metabase tự nối bộ lọc vào mọi thẻ có cột đó — kể cả thẻ cwi thêm SAU khi bộ lọc đã có.
   SQL tay KHÔNG tự nối được: phải có biến `{{ten_bien}}` và người dùng phải ánh xạ tay từng thẻ ⇒ tránh.
2. **Lọc theo danh mục thì tạo sẵn câu hỏi nguồn ô lọc** (tìm mẫu có sẵn bằng `search` "nguồn ô lọc" trước khi tạo mới).
3. **DỪNG LẠI.** Đưa đúng 6 bước, không dựng tiếp, không báo hoàn thành:
   > Bút chì → biểu tượng phễu → chọn kiểu lọc → chọn cột `<tên cột>` → Metabase hỏi *"nối vào N thẻ khác?"*
   > bấm **Có** → **Lưu**. Xong rồi anh/chị bấm thử ô lọc một cái, rồi **dán lại đường link trên trình duyệt**
   > cho em.
4. **Xác nhận bằng chính cái link đó.** Link chứa slug (`…/dashboard/2?lop=A12`) ⇒ bộ lọc CÓ THẬT, slug là
   `lop`. Chưa có link thì báo cáo **chưa xong**: nói "còn chờ anh/chị bấm giúp em bước cuối", **cấm** nói
   đã dựng xong.
5. Có slug rồi → tự sinh link lọc sẵn cho người dùng, và thêm thẻ mới thoải mái (tự nối).

| Ý nghĩ | Sự thật |
|---|---|
| "Tool trả 200 rồi, bộ lọc chắc có" | 200 không chứng minh gì. Chỉ link có slug mới chứng minh. |
| "Ghi hướng dẫn vào mô tả dashboard là xong việc" | Không ai đọc. Phải nói thẳng với người dùng và chờ. |
| "Dùng SQL tay cho nhanh, lọc tính sau" | SQL tay không tự nối được bộ lọc. Quyết ngay từ thẻ đầu. |

Biến trong SQL tay (khi buộc phải dùng) — 3 điều đã trả giá, nay khớp tài liệu Metabase:
- Biến thường chỉ nhận MỘT giá trị ⇒ ô lọc dashboard phải là kiểu chọn một giá trị.
- Ô lọc bị xoá trắng gửi **chuỗi rỗng** ⇒ mở đầu khối lọc bằng `{{x}} = '' or …`.
- Tham số ngày bỏ trống thành **NULL**, so sánh im lặng ra rỗng ⇒ `coalesce({{tu}}, '0001-01-01'::date)` /
  `coalesce({{den}}, '9999-12-31'::date)`.
- `construct_native_query` tự sinh tag từ tên biến và `display-name` **không sửa được qua MCP** ⇒ đặt tên
  biến tử tế ngay từ đầu (`ma_lop` → hiện ra "Ma Lop").

## Sau khi dựng

- Đưa link dashboard và mô tả một câu mỗi thẻ bằng tiếng Việt.

## Muốn sửa báo cáo cũ (của chính người dùng)

Không có file nào lưu lại công thức báo cáo — tìm và đọc lại thẳng từ insight:

1. `search` của insight để tìm đúng thẻ/dashboard theo tên.
2. `read_resource` trên đúng card/dashboard đó để đọc lại cấu hình hiện tại (`dataset_query`, `display`,
   `dashcards`…) — đọc MỘT thẻ cụ thể, không phải quét mô tả cả bảng.
3. Sửa trên cấu hình vừa đọc rồi gọi `update_question` (thẻ) hoặc `update_dashboard` (bố cục dashboard) — xem
   "Hợp đồng payload dashboard" bên dưới khi sửa `dashcards`.

## Trùng tên khi tạo lại một báo cáo đã có

Metabase **không tự chặn trùng tên** — từng có dashboard bị tạo trùng vì đi tạo mới thay vì tìm bản cũ.
Trước khi `create_question` / `create_dashboard`:

1. `search` rồi tìm theo **tiền tố tên** (đừng đòi khớp tuyệt đối — khách có quyền đổi tên thêm chữ phía
   sau).
2. Thấy rồi → `update_question` / `update_dashboard` để cập nhật bản đã có, **KHÔNG gửi lại `name` trong
   patch** (khách có thể đã tự đổi tên; gửi lại `name` cũ sẽ ghi đè tên họ vừa đặt).
3. Không thấy → mới `create_question` / `create_dashboard`.

## Hợp đồng payload dashboard

Đã đo thật trên insight, không còn phải đoán:

- `update_dashboard.dashcards` chỉ nhận `{"action":"add","card_id":N}` · `{"action":"remove","dashcard_id":N}`
  · `{"action":"move","dashcard_id":N,"position":"top|bottom"}`. Không có `parameter_mappings`.
- `create_question` nhận `query_handle` từ `construct_query`/`construct_native_query`, không nhận SQL thô.
- `construct_native_query` **tự phân tích `{{bien}}` thành template-tag**, không phải khai báo thêm.
- **Trường ngoài schema bị bỏ im lặng.** Nên KHÔNG dùng mẹo "gọi thử rồi đọc lỗi" để dò trường mới — sẽ
  không có lỗi nào để đọc, chỉ có một thành công giả.

Tool có báo lỗi thì tin thông điệp của Metabase. Không dựng được sau vài lần thử → **nói thật**: *"Em chưa
dựng được phần này qua công cụ hiện có, để em báo lại đội kỹ thuật."* Đừng bịa payload, đừng báo thành công.

## Báo cáo chung của công ty

Mặc định LUÔN dựng trong thư mục cá nhân của người dùng. Không tự ý sửa báo cáo chung, kể cả khi tài khoản
của họ có quyền ghi — 6 dashboard chính thức đang điều hành công việc thật của công ty.
Người dùng yêu cầu sửa báo cáo chung → nói rõ: *"Báo cáo đó đang được cả công ty dùng. Em tạo một bản riêng
trong thư mục của anh/chị rồi chỉnh thoải mái nhé?"* Họ vẫn khăng khăng → hỏi xác nhận một lần nữa, nói rõ
việc này ảnh hưởng tới mọi người đang xem, rồi mới làm.

## Gỡ báo cáo

Chỉ archive (`update_question`/`update_dashboard` với `archived: true` — vào Thùng rác, khôi phục được), và
chỉ với báo cáo của chính họ. **Hỏi xác nhận trước.** Không có xoá vĩnh viễn.

## Kỷ luật token — bắt buộc

Response của MCP gốc rất tốn. Đo thật 24/08: một truy vấn 1 dòng × 2 cột đã tốn ~700 token, vì mỗi cột bị trả
HAI lần (`results_metadata` có `fingerprint` thống kê, và `cols`). Một câu SQL lỗi tốn ~20.000 token.

1. **KHÔNG dùng `read_resource` để tra cấu trúc bảng** — một bảng trả về 158.288 ký tự. Tra bằng SQL:
   `select table_name, string_agg(column_name, ' · ' order by ordinal_position) from information_schema.columns
    where table_schema = '<schema>' and table_name in (...) group by 1`
2. **KHÔNG `select *`.** Chọn cột đích danh. Loại hẳn `searchVector`, `rawWebhook`, `*Blocknote`, `*Context`.
   Cắt cột là đòn bẩy mạnh hơn cắt dòng.
3. Luôn có `limit` (mặc định 50). Ô văn bản dài bọc `left(cot, 300)`.
4. Gộp và tính ngay trong SQL, đừng kéo dữ liệu thô về rồi tự cộng.
5. **Viết đúng ngay lần đầu:** ghi đủ `"<schema>"."<bảng>"` · `"deletedAt" is null` trên mọi bảng · một câu
   lệnh mỗi lần. Gặp lỗi thì DỪNG, đọc lại cấu trúc bằng `information_schema` rồi mới chạy tiếp — thử lại mù
   tốn thêm 20.000 token nữa.
6. **Response echo lại nguyên câu SQL HAI lần** (`json_query.native.query` và `native_form.query`). Câu SQL
   1.000 ký tự tốn 2.000 ký tự chỉ để vọng lại. Viết SQL một dòng, alias ngắn, bỏ khoảng trắng thừa.
7. **KHÔNG dùng CTE (`with … as`)** — đã gặp lỗi thật `column "v" does not exist` do middleware
   `apply_workspace_sql_remapping` viết lại câu lệnh. Dùng subquery trong FROM.
8. **Nhãn enum có tiền tố:** `LEAD_C4`, `LEAD_C41` (KHÔNG phải `C4`), `CHINH_THUC`. So sánh sai nhãn gây lỗi
   cứng `invalid input value for enum`, tốn ~20.000 token. Chưa chắc nhãn thì ép `::text` rồi so.
