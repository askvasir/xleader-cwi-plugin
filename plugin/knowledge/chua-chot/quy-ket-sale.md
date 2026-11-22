# ⚠ CHƯA CHỐT: doanh số quy cho ai?

⚠ Con số cụ thể trong file này là VÍ DỤ ĐO TẠI MỘT THỜI ĐIỂM khi phát hiện lỗi/khác biệt — không phải số
liệu hiện tại. Cần số THẬT thì luôn TRUY VẤN lại.

Cùng một đơn đang có HAI đáp án khác nhau, chênh lệch thật:
- `opportunity."sale"` — trường quy kết chính thức
- người tạo đơn `_order."createdByName"`

Ca thật: một đơn do **Sale A** tạo, cơ hội do **Sale B** tạo, nhưng `sale` lại gán cho **Sale C** — người
tạo đơn khác người được gán. Tuỳ tính theo cách nào mà doanh số tuần đó của Sale C chênh **vài vé / hàng
chục triệu** chỉ vì một quy ước chưa chốt.

Model `Dòng đơn hàng (Vé)` còn thêm một tầng suy diễn: khi `sale` trống thì Model suy Sale từ email `picId`
theo một bảng ánh xạ cố định.

**Bắt buộc:** câu hỏi nào đụng tới hiệu suất/doanh số của một Sale cụ thể thì phải HỎI LẠI người dùng tính
theo cách nào, và nói rõ mình đang tính theo cách đó. Tuyệt đối không tự chọn rồi trả số. Chưa chốt quy tắc
thì chưa dựng báo cáo KPI cho Sale.
