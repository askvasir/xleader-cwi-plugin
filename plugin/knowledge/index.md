# Mục lục dữ liệu XLeaders

## Đích Metabase

`database_id = 2`, schema `workspace_6ytci89jf3kflsl9aaaxrr5hw`:

```sql
select current_database(), table_schema from information_schema.tables
 where table_schema like 'workspace%' limit 1
```

## Bảng gốc

- `_class` lớp học
- `_course` khoá học
- `_order` đơn hàng
- `_orderItem` dòng đơn hàng
- `_payment` thanh toán
- `_enrollment` ghi danh
- `_salesPolicy` chính sách bán hàng
- `opportunity` cơ hội/lead
- `person` người
- `company` công ty

## Tra cấu trúc rẻ

Đừng `select *`/`read_resource`. Gộp cột qua `string_agg`:

```sql
select table_name, string_agg(column_name, ' · ') as cols
  from information_schema.columns
 where table_schema = 'workspace_6ytci89jf3kflsl9aaaxrr5hw' and table_name in ('_orderItem','_order')
 group by 1
```

## Hỏi về X → đọc file nào

- Doanh thu → `chi-so/doanh-thu.md`
- Số vé bán → `chi-so/so-ve.md`
- Ghi danh / sĩ số → `chi-so/ghi-danh.md`
- Lead / phễu / nguồn → `chi-so/co-hoi-tang-pheu.md`
- Kênh / nguồn (Paid Sale · Organic Sale · Nguồn chưa rõ) → `chi-so/co-hoi-tang-pheu.md`
- Lớp học / lịch → `chi-so/lop-hoc.md`
- Công ty đối tác → `chi-so/doi-tac-doanh-nghiep.md`
- Quy tắc chung → `quy-tac-chung.md`
- Muốn đếm lại một con số bằng đường độc lập → `doi-soat.md`
- Số liệu lệch sổ sách, doanh thu tổng → `chua-chot/dinh-nghia-doanh-thu.md`
- Doanh số/hiệu suất của một Sale cụ thể → `chua-chot/quy-ket-sale.md`
