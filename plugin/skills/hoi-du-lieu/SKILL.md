---
name: hoi-du-lieu
description: Dùng khi người dùng hỏi bất kỳ câu nào về số liệu kinh doanh XLeaders — doanh thu, học viên, lớp, lead, hiệu suất Sale, công ty đối tác. Kích hoạt cả khi câu hỏi ngắn gọn kiểu "doanh thu lớp này bao nhiêu" hay "Sale X tháng rồi thế nào".
---

# Trả lời câu hỏi dữ liệu XLeaders

Người hỏi **không biết SQL và không phải người phân tích dữ liệu**. Nhiệm vụ của bạn là trả số ĐÚNG và nói rõ
số đó nghĩa là gì — chứ không phải khoe kỹ thuật. Mọi truy vấn chạy qua tool `execute_sql` của insight
(`database_id: 2`). Tool đó có thể tới từ plugin hay từ connector của Claude nên **tên đầy đủ khác nhau tuỳ
máy** — cứ dùng tool `execute_sql` nào đang có trong phiên, đừng đòi đúng một tên server. Không có tool nào
của insight → nói thẳng là chưa kết nối và bảo người dùng gõ `/start`, TUYỆT ĐỐI không đoán số.

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

## Bắt buộc: đọc trước khi trả lời

1. Mở đúng file `chi-so/*.md` theo bảng trên — đó là nơi có **"Cách tính:"** đã đối soát, câu SQL mẫu, và
   mục "Bẫy của chỉ số này".
2. Câu hỏi đụng tới doanh số/hiệu suất của MỘT Sale cụ thể → đọc thêm `chua-chot/quy-ket-sale.md` **trước
   khi trả lời** — đây là quy ước CHƯA CHỐT giữa các cách quy kết.
3. Người dùng nói số lệch sổ sách/doanh thu tổng → đọc thêm `chua-chot/dinh-nghia-doanh-thu.md`.
4. Không khớp chỉ số nào ở trên → đọc `quy-tac-chung.md` (10 quy tắc nghiệp vụ + khoảng trống dữ liệu).

## Quy trình

1. **Câu hỏi mơ hồ → hỏi lại bằng ngôn ngữ nghiệp vụ, KHÔNG bằng thuật ngữ SQL.**
   "Doanh thu lớp này" → *"Anh/chị muốn tính theo ngày lớp khai giảng, hay theo ngày khách chuyển tiền ạ?"*
   Không bao giờ hỏi kiểu "anh muốn join bảng nào".
2. Chạy câu SQL của file chỉ số vừa đọc qua `execute_sql`, chỉnh lại theo đúng câu hỏi (lọc lớp, khoảng thời
   gian…) nhưng **giữ nguyên cách tính và các điều kiện join** đã đối soát trong file đó.
3. **Không bịa định nghĩa.** Nếu không có file chỉ số nào khớp và `quy-tac-chung.md` cũng không có cách tính
   chuẩn cho chỉ số được hỏi, nói: *"Chỉ số này chưa có cách tính chuẩn. Em đề xuất tính là … — anh/chị xác
   nhận giúp em nhé?"* rồi mới chạy.

## Định dạng câu trả lời — LUÔN có đủ 3 phần

1. **Con số hoặc bảng** (bảng tối đa 20 dòng; nhiều hơn thì tóm tắt + đề nghị dựng báo cáo riêng).
2. **"Cách tính:"** một câu tiếng Việt thường, ví dụ *"Doanh thu = tiền vé đã bán, chưa VAT, tính theo ngày
   tổ chức lớp."* Không nêu tên bảng, tên cột, hay câu SQL trừ khi người dùng chủ động hỏi.
3. **Nếu số liệu có khoảng trống thì nói ra** — ví dụ có lớp thiếu ngày tổ chức nên không nằm trong khoảng
   lọc thời gian đang dùng.

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

## Không bao giờ

- Không đưa ra con số mà không biết nó được tính thế nào.
- Không im lặng bỏ qua dữ liệu thiếu hoặc dữ liệu không khớp khoảng lọc.
- Không tự ý tạo/sửa báo cáo (`create_question`/`create_dashboard`) khi người dùng chỉ đang hỏi (hỏi ≠ muốn
  tạo báo cáo) — đó là việc của skill `dung-bao-cao`.
- Không trả lời câu hỏi về doanh số/hiệu suất của một Sale cụ thể mà chưa hỏi quy kết theo cách nào — xem
  `chua-chot/quy-ket-sale.md`.
