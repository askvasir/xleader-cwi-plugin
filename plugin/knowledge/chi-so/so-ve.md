# Số vé bán

> ⚠ Con số trong dòng đối soát dưới đây là ẢNH CHỤP tại thời điểm đối chứng, KHÔNG phải số hiện tại — dữ
> liệu đổi hằng giờ. Muốn có số thật thì luôn chạy lại câu SQL. Tuyệt đối không trích số trong tài liệu này
> ra trả lời.

Cách tính: tổng số lượng vé (`Σ soLuong`, không phải đếm dòng) trong các dòng đơn ở cơ hội đã "chốt"
(tầng phễu C4 hoặc C41).

```sql
select sum(oi."soLuong")::int as so_ve
  from "workspace_6ytci89jf3kflsl9aaaxrr5hw"."_orderItem" oi
  join "workspace_6ytci89jf3kflsl9aaaxrr5hw"."_order"      o  on o.id = oi."donHangId" and o."deletedAt" is null
  join "workspace_6ytci89jf3kflsl9aaaxrr5hw"."opportunity" op on op.id = oi."coHoiId"  and op."deletedAt" is null
 where oi."deletedAt" is null
   and op."phanLoaiLead" in ('LEAD_C4', 'LEAD_C41')
```

Đã đối chứng CÔNG THỨC với Model "Dòng đơn hàng (Vé)" (câu hỏi "Vé đã bán (dòng đơn chốt)") ngày 2026-08-24
— hai đường cho cùng một kết quả.

## Bẫy của chỉ số này

- **Số vé bán = `Σ "soLuong"`, KHÔNG phải `count(*)`.** Dòng điều chỉnh hoàn/đổi/huỷ có `soLuong` âm
  (vd `-1`); đếm dòng thay vì cộng số lượng từng cho ra số vé cao hơn con số đúng.
- **BẮT BUỘC `INNER JOIN _order`**: rất nhiều dòng `_orderItem` không gắn đơn hàng nào — đó là dòng
  Sale gắn tạm một lead vào một lớp mục tiêu (trước khi có đơn), **không phải vé đã bán**. Bỏ join này là
  thổi số vé lên gấp nhiều lần.

⚠ Số này tính theo đơn ĐÃ CHỐT (tầng LEAD_C4/LEAD_C41). Khi trả lời phải nói rõ điều đó. Người dùng thắc
mắc lệch sổ sách → đọc chua-chot/dinh-nghia-doanh-thu.md.
