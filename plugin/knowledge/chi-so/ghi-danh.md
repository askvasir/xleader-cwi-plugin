# Ghi danh chính thức

> ⚠ Con số trong dòng đối soát dưới đây là ẢNH CHỤP tại thời điểm đối chứng, KHÔNG phải số hiện tại — dữ
> liệu đổi hằng giờ. Muốn có số thật thì luôn chạy lại câu SQL. Tuyệt đối không trích số trong tài liệu này
> ra trả lời.

Cách tính: đếm `_enrollment` có `trangThai = 'CHINH_THUC'`, bắt buộc join sang `_class` còn tồn tại
(`deletedAt is null`) — hệ thống có ghi danh CHÍNH THỨC trỏ tới lớp không còn tồn tại; bỏ join này là đếm dư
so với con số đúng.

```sql
select count(*)::int as ghi_danh_chinh_thuc
  from "workspace_6ytci89jf3kflsl9aaaxrr5hw"."_enrollment" e
  join "workspace_6ytci89jf3kflsl9aaaxrr5hw"."_class" c on c.id = e."lopHocId" and c."deletedAt" is null
 where e."deletedAt" is null
   and e."trangThai" = 'CHINH_THUC'
```

Đã đối chứng CÔNG THỨC với Model "Ghi danh (HV theo lớp)" (định nghĩa đã lưu của câu hỏi "Số ghi danh chính
thức", đọc qua `read_resource` vì câu hỏi có tham số nên `execute_question` không chạy trực tiếp được — chạy
lại đúng công thức đó qua `execute_sql`) ngày 2026-08-24 — hai đường cho cùng một kết quả.

## Bẫy của chỉ số này

- **"Ghi danh chính thức" = `_enrollment."trangThai" = 'CHINH_THUC'`.** TUYỆT ĐỐI không được suy ra từ
  `opportunity."phanLoaiLead"` (vd coi C4/C41 là "đã ghi danh"). Ca lỗi thật: suy từ cơ hội đã làm **mất
  hàng trăm ghi danh, trải trên khoảng một nửa số lớp**; có lớp mất trắng toàn bộ ghi danh.
- **Khách mời / vé tặng / bảo lưu KHÔNG tạo `opportunity`** (`coHoiId` NULL cho toàn bộ 3 loại này). Vì vậy khi
  chỉ cần lấy thêm cột từ `opportunity` (ví dụ nguồn/kênh) cho một tập ghi danh, **luôn `LEFT JOIN`** —
  `INNER JOIN` sẽ âm thầm xoá sạch nhóm khách mời/vé tặng/bảo lưu ra khỏi kết quả mà không báo lỗi.
- **`_enrollment."active"` là cờ ĐÃ THANH TOÁN** (bật khi cơ hội lên C4/C41, tắt khi còn ở C0–C31), **không
  phải cờ "có mặt trong lớp"**. Dùng cờ này để đếm sĩ số/ghi danh chính thức là lặp lại đúng lỗi bên trên.
- Có ghi danh **chưa điền học viên** (bán vé trước, gán tên học viên sau) ⇒ "lượt ghi danh" cao hơn "số học
  viên" (theo đầu người) là **ĐÚNG nghiệp vụ**, không phải lỗi đếm trùng hay đếm thiếu.
