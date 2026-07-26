# Nhật ký wiki (log)

> Nhật ký **chỉ-thêm** (append-only), theo thời gian. Chỉ thêm mục mới, không sửa/xóa mục cũ.
> Mỗi mục bắt đầu bằng tiền tố nhất quán để grep được.
> Lấy 5 mục gần nhất: `grep "^## \[" wiki/log.md | tail -5`.

## [2026-07-26] khởi tạo | Thiết lập hệ thống "Bộ não thứ hai"
Khởi tạo toàn bộ hệ thống wiki tri thức bền vững hôm nay:
- Tạo cấu trúc ba tầng: `raw/` (nguồn thô bất biến), `wiki/` (tri thức biên dịch), `production/` (sản phẩm đầu ra).
- Tạo `CLAUDE.md` (schema + cẩm nang), `wiki/index.md`, `wiki/log.md`, `wiki/tong-quan.md`.
- Tạo các thư mục con của wiki: `thuc-the/`, `khai-niem/`, `tom-tat-nguon/`, `so-sanh/`.
- Hiện `raw/` chưa có nguồn nào. Sẵn sàng nhận nguồn đầu tiên.
