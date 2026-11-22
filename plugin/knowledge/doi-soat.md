# Đối soát — đường ĐỘC LẬP để đếm lại một con số

Đây KHÔNG phải nơi trả lời câu hỏi trực tiếp — dùng SQL trên bảng gốc (`chi-so/*.md`) để trả lời người
dùng. 7 Model dưới đây chỉ dùng để ĐẾM LẠI, chứng minh một con số khi cần đối soát: gọi `execute_question`
với id Model (tìm id bằng `search`).

⚠ Một số Model có hơn 2.000 dòng gốc (vd `opportunity` × lớp đã gắn) — khi đó `execute_question` bị cắt ở
2.000 dòng đầu và KHÔNG dùng để lấy số tổng được; tìm câu hỏi tổng hợp (aggregate) đã lưu sẵn trên Model đó
thay vào (`search` theo tên chỉ số), hoặc đọc định nghĩa Model bằng `read_resource` rồi tự viết
`execute_sql` tương đương.

| Model | 1 dòng = | Dùng khi cần đối chứng |
|---|---|---|
| `Cơ hội (Lead)` | 1 cơ hội × lớp đã gắn | lead, phễu, nguồn, Sale, công nợ |
| `Cơ hội theo tầng phễu` | 1 cơ hội × tầng đã đạt × lớp đã gắn | phễu cộng dồn C1≥C2≥C3≥C4 |
| `Dòng đơn hàng (Vé)` | 1 dòng đơn hàng | doanh thu, số vé, chiết khấu, VAT |
| `Thanh toán` | 1 lần thanh toán | tiền thực thu, xác nhận kế toán |
| `Ghi danh (HV theo lớp)` | 1 ghi danh | học viên, sĩ số, công ty, tình trạng học |
| `Lớp học (Buổi đào tạo)` | 1 lớp | lịch, giảng viên, sĩ số, doanh thu lớp |
| `Đối tác doanh nghiệp` | 1 công ty có học viên | số HV, lượt, doanh thu theo doanh nghiệp |
