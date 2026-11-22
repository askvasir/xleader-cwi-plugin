# Quy tắc nghiệp vụ XLeaders — đọc trước khi trả lời bất kỳ câu hỏi số liệu nào

Đúc kết từ nhiều tháng đối soát báo cáo thật. Mỗi quy tắc
từng gây SAI SỐ THẬT trên báo cáo — và báo cáo vẫn "chạy tốt", không báo lỗi. Đừng đoán, đọc file này.

⚠ **Mọi mô tả mức độ trong file này là VÍ DỤ ĐO TẠI MỘT THỜI ĐIỂM khi phát hiện lỗi — KHÔNG PHẢI số liệu
hiện tại.** Chỉ giữ lại để minh hoạ mức độ nghiêm trọng của quy tắc.
Người dùng cần con số THẬT thì luôn phải TRUY VẤN (`execute_sql` / `execute_question`) — tuyệt đối không
trích số trong tài liệu này ra trả lời như thể đó là số hiện tại.

Muốn xem SQL chuẩn đã đối soát cho từng chỉ số → `chi-so/*.md`. Muốn xem 2 câu hỏi chưa chốt quy ước →
`chua-chot/dinh-nghia-doanh-thu.md` và `chua-chot/quy-ket-sale.md`.

## Tiền

1. **Doanh thu NET (chưa VAT)** = `Σ _orderItem."thanhTienAmountMicros" / (1 + "vat") / 1e6`.
   ⚠ **BẮT BUỘC `INNER JOIN _order`**: rất nhiều dòng `_orderItem` không gắn đơn hàng nào — đó là dòng
   Sale gắn tạm một lead vào một lớp mục tiêu (trước khi có đơn), **không phải doanh thu**. Bỏ join này là
   thổi doanh thu lên gấp nhiều lần.
2. **Số vé bán = `Σ "soLuong"`, KHÔNG phải `count(*)`.** Dòng điều chỉnh hoàn/đổi/huỷ có `soLuong` âm
   (vd `-1`); đếm dòng thay vì cộng số lượng từng cho ra số vé cao hơn con số đúng.
3. **Mốc ghi nhận doanh thu = `_class."ngayToChuc"`** (ngày tổ chức lớp), không phải ngày khách chuyển tiền.
   Nếu người dùng muốn tính theo ngày thu tiền thì phải hỏi rõ và dùng `_payment."ngayChuyenKhoan"` thay vào.
4. **`company."createdAt"` = ngày import dữ liệu vào CRM** (gần như toàn bộ đều là một tháng) ⇒ cột này
   **vô dụng để cắt kỳ theo thời gian**, kể cả khi câu hỏi nghe như đang hỏi về công ty theo thời gian.

## Học viên & ghi danh

5. **"Ghi danh chính thức" = `_enrollment."trangThai" = 'CHINH_THUC'`.** TUYỆT ĐỐI không được suy ra từ
   `opportunity."phanLoaiLead"` (vd coi C4/C41 là "đã ghi danh"). Ca lỗi thật: suy từ cơ hội đã làm **mất
   hàng trăm ghi danh, trải trên khoảng một nửa số lớp**; có lớp mất trắng toàn bộ ghi danh.
6. **Khách mời / vé tặng / bảo lưu KHÔNG tạo `opportunity`** (`coHoiId` NULL cho toàn bộ 3 loại này). Vì vậy khi
   chỉ cần lấy thêm cột từ `opportunity` (ví dụ nguồn/kênh) cho một tập ghi danh, **luôn `LEFT JOIN`** —
   `INNER JOIN` sẽ âm thầm xoá sạch nhóm khách mời/vé tặng/bảo lưu ra khỏi kết quả mà không báo lỗi.
7. **`_enrollment."active"` là cờ ĐÃ THANH TOÁN** (bật khi cơ hội lên C4/C41, tắt khi còn ở C0–C31), **không
   phải cờ "có mặt trong lớp"**. Dùng cờ này để đếm sĩ số/ghi danh chính thức là lặp lại đúng lỗi ở mục 5.

## Cơ hội & kênh

8. **Phân loại Kênh của một cơ hội:** `nguon like '%ADS'` → **Paid Sale** · `nguon is null` → **Nguồn chưa
   rõ** · còn lại → **Organic Sale**. Dùng đúng 3 tên nhãn này, đừng tự dịch khác.
   ⚠ **Nhánh NULL riêng là BẮT BUỘC** — `NULL like '%ADS'` cho kết quả NULL nên rơi thẳng vào `else`, khiến
   hàng trăm cơ hội chưa nhập nguồn bị đếm nhầm thành "miễn phí". Ảnh hưởng không đều theo tầng phễu, có tầng
   từng lệch tới hơn một phần sáu.
9. **Đường nối Cơ hội → Lớp đi qua `_orderItem."coHoiId"` + `_orderItem."lopHocId"`.** KHÔNG được join qua
   `_order` — cách đó chỉ thấy cơ hội đã có đơn hàng (từ tầng C3 trở lên) và âm thầm bỏ sót toàn bộ C1/C2.
   Lưu ý: `opportunity` không còn cột `lopHocId` lẫn `donHangId` nữa (đã bỏ từ 2026-07).
10. **Một cơ hội có thể mua vé cho NHIỀU lớp** (thực tế trên hệ thống có hàng chục cơ hội như vậy). Khi đếm cơ hội
    theo từng lớp, đừng lọc kiểu `distinct on (coHoiId)` để gán mỗi cơ hội về đúng 1 lớp — cách đó làm cơ hội
    biến mất khỏi mọi lớp còn lại của nó; lỗi thật đã làm mất hơn trăm lượt (cơ hội × lớp) trên hàng chục lớp.

## Khoảng trống dữ liệu — phải nói ra với người dùng, không im lặng bỏ qua

- **Có lớp thiếu ngày tổ chức** (`_class."ngayToChuc"` NULL) — có lớp như vậy chứa hàng chục ghi danh — nên
  **không rơi vào bất kỳ khoảng lọc thời gian nào**. Khi câu hỏi lọc theo kỳ (tháng/quý/năm), phải nhắc điều
  này nếu số liệu có khả năng bỏ sót các lớp dạng đó.
- Có ghi danh **chưa điền học viên** (bán vé trước, gán tên học viên sau) ⇒ "lượt ghi danh" cao hơn "số học
  viên" (theo đầu người) là **ĐÚNG nghiệp vụ**, không phải lỗi đếm trùng hay đếm thiếu.
- **Tổng số học viên cộng dồn theo từng năm sẽ lớn hơn tổng số học viên của cả giai đoạn** — vì một người học
  nhiều năm chỉ được tính 1 lần khi gộp cả giai đoạn. Đừng cộng các con số theo-năm lại rồi so với tổng chung.

## Luôn luôn

- Viết SQL trên bảng gốc: MỌI bảng phải ghi đủ `"<schema>"."<bảng>"` — tên bảng trần (vd `"_class"` một mình)
  báo lỗi `relation does not exist`. Tên schema hiện tại: `workspace_6ytci89jf3kflsl9aaaxrr5hw` (tự kiểm bằng
  câu ở `index.md`).
- Lọc `"deletedAt" IS NULL` trên MỌI bảng khi viết SQL trực tiếp.
- Bảng của object tuỳ chỉnh có tiền tố `_` (`_class`, `_course`, `_order`, `_orderItem`, `_payment`,
  `_enrollment`, `_salesPolicy`); object chuẩn của CRM thì không có tiền tố (`opportunity`, `person`,
  `company`).
- Các cột tiền tệ có hậu tố `AmountMicros` lưu đơn vị micro — chia cho 1.000.000 (1e6) để ra VND.
- Không dùng `with … as (…)` (CTE) — qua server `insight` thật, middleware `apply_workspace_sql_remapping` từng
  viết lại câu CTE và gãy với lỗi `column "v" does not exist` (không phải lỗi cú pháp của câu gốc). Viết phẳng,
  dùng subquery lồng trong `FROM`/`JOIN`, không dùng `WITH`, cho MỌI câu SQL.
