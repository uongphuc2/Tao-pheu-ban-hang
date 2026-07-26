# CLAUDE.md — Schema & Cẩm nang "Bộ não thứ hai"

> File cấu hình then chốt cho dự án. Mọi phiên làm việc (session) của trợ lý AI
> đều phải đọc file này **trước tiên** để hiểu cách tổ chức và vận hành wiki.

---

## 1. Triết lý

Thay vì mỗi lần hỏi lại đi truy xuất tài liệu thô từ đầu (kiểu **RAG** — *retrieval augmented
generation, sinh nội dung có truy xuất tài liệu*), hệ thống này **DUY TRÌ MỘT WIKI** liên kết
chéo, tích lũy dần.

Mỗi khi nạp nguồn mới, trợ lý **ĐỌC** nó, trích thông tin then chốt, rồi **TÍCH HỢP** vào wiki
hiện có:

- cập nhật trang thực thể,
- chỉnh tóm tắt chủ đề,
- ghi chú mâu thuẫn với dữ liệu cũ.

Kiến thức được **biên dịch MỘT LẦN rồi giữ cập nhật**, không suy lại mỗi lần hỏi. Wiki là **tài
sản bền vững**, lớn dần theo thời gian.

---

## 2. Ba tầng (kiến trúc thư mục)

### `raw/`
Nguồn thô, **BẤT BIẾN**. Chỉ đọc, **KHÔNG BAO GIỜ** sửa. Đây là **nguồn chân lý** (source of
truth). Dự án đặt các file nguồn (ví dụ `.srt` — *phụ đề, subtitle*) tại đây; tài sản đính kèm
(ảnh, tệp tin) đặt trong `raw/assets/`.

### `wiki/`
Các file markdown do trợ lý (**LLM** — *mô hình ngôn ngữ lớn, large language model*) sinh ra và
sở hữu hoàn toàn: trang tóm tắt, trang thực thể, trang khái niệm, bảng so sánh, tổng quan, tổng
hợp. Trợ lý tạo, cập nhật, giữ liên kết chéo nhất quán.

Cấu trúc con:

| Thư mục | Chứa gì |
|---|---|
| `wiki/thuc-the/` | Trang **thực thể** (người, tổ chức, sản phẩm, địa điểm…) |
| `wiki/khai-niem/` | Trang **khái niệm** (ý tưởng, phương pháp, thuật ngữ) |
| `wiki/tom-tat-nguon/` | Trang **tóm tắt nguồn** (mỗi file nguồn trong `raw/` → 1 trang) |
| `wiki/so-sanh/` | Trang **so sánh** (bảng đối chiếu nhiều thực thể/khái niệm) |
| `wiki/index.md` | Danh mục mọi trang trong wiki |
| `wiki/log.md` | Nhật ký chỉ-thêm (append-only) |
| `wiki/tong-quan.md` | Trang tổng quan toàn wiki |

### `CLAUDE.md`
Schema (file này): quy tắc tổ chức wiki, quy ước, và quy trình làm việc.

### `production/`
Các file markdown do quá trình làm việc sinh ra **sau này**, khi người dùng ra lệnh — ví dụ:
blog, bài đăng Facebook, kịch bản video, file PDF, hoặc các tài liệu được tạo ra khác.

---

## 3. Quy ước ngôn ngữ

- Toàn bộ vault và schema viết bằng **TIẾNG VIỆT**.
- Nếu buộc phải dùng thuật ngữ nước ngoài, luôn kèm chú thích tiếng Việt trong ngoặc **ngay lần
  đầu xuất hiện**. Ví dụ: "embedding (vector nhúng)".

---

## 4. Quy ước trang wiki

### Tên file
Chữ thường, **không dấu**, nối bằng gạch ngang. Ví dụ: `pham-thanh-long.md`.

### Frontmatter YAML
Mỗi trang mở đầu bằng khối frontmatter (siêu dữ liệu ở đầu file, đặt giữa hai dòng `---`):

```yaml
---
tieu_de: Tên đầy đủ có dấu của trang
loai: thuc-the        # thuc-the | khai-niem | tom-tat-nguon | so-sanh | tong-quan | tong-hop
ngay_tao: YYYY-MM-DD
ngay_cap_nhat: YYYY-MM-DD
nguon:                # danh sách file nguồn tham chiếu
  - ten-nguon.srt
tags:
  - tag-mot
  - tag-hai
---
```

### Liên kết chéo
Giữa các trang dùng cú pháp `[[ten-file-khong-duoi]]` (không kèm đuôi `.md`).

### Trích dẫn
Mọi khẳng định rút ra từ nguồn phải **TRÍCH DẪN** theo dạng:

`(nguồn: tên-file.srt, mốc thời gian nếu có)`

Ví dụ: `(nguồn: buoi-01.srt, 00:12:30)`.

---

## 5. Quy trình "NẠP NGUỒN" (Ingest)

Khi nạp một nguồn mới, làm theo checklist:

1. **Đọc** file nguồn trong `raw/`.
2. **Trao đổi** với người dùng vài ý chính.
3. **Viết** trang tóm tắt trong `wiki/tom-tat-nguon/`.
4. **Cập nhật** `wiki/index.md`.
5. **Cập nhật / tạo** các trang thực thể và khái niệm liên quan khắp wiki.
6. **Ghi chú** nếu nguồn mới mâu thuẫn nguồn cũ.
7. **Thêm 1 dòng** vào `wiki/log.md`.

> ⚠️ Một nguồn có thể chạm **10–15 trang**. Mặc định nạp **TỪNG** nguồn một, **có giám sát**;
> hỗ trợ nạp hàng loạt nếu người dùng yêu cầu.

---

## 6. Quy trình "TRUY VẤN" (Query)

1. Đọc `wiki/index.md` **trước** để tìm trang liên quan.
2. Đọc các trang đó.
3. Tổng hợp câu trả lời **CÓ TRÍCH DẪN**.
4. Nếu câu trả lời có giá trị (so sánh, phân tích, phát hiện mối liên hệ), **đề nghị lưu ngược
   lại** thành trang wiki mới (thường là `loai: tong-hop` hoặc `so-sanh`) để tích lũy.

---

## 7. Quy trình "RÀ SOÁT" (Lint)

Kiểm tra định kỳ sức khỏe của wiki:

- Mâu thuẫn giữa các trang.
- Khẳng định lỗi thời.
- Trang **mồ côi** (orphan — không có liên kết nào trỏ đến).
- Khái niệm quan trọng chưa có trang riêng.
- Thiếu liên kết chéo.
- Khoảng trống dữ liệu.

Sau rà soát: **đề xuất câu hỏi mới** và **nguồn cần tìm**.

---

## 8. Vai trò `index.md` và `log.md`

### `wiki/index.md` — bản đồ wiki
Danh mục **MỌI** trang, tổ chức theo hạng mục (Thực thể / Khái niệm / Tóm tắt nguồn / So sánh /
Khác). Mỗi trang một dòng: `- [[ten-file]] — tóm tắt một dòng`. **Cập nhật MỖI lần nạp nguồn.**
Đây là điểm khởi đầu của mọi truy vấn.

### `wiki/log.md` — nhật ký chỉ-thêm
Nhật ký **append-only** (chỉ thêm, không sửa/xóa mục cũ), theo thời gian. Mỗi mục bắt đầu bằng
tiền tố nhất quán để **grep** được:

- `## [YYYY-MM-DD] ingest | Tên nguồn`
- `## [YYYY-MM-DD] query | ...`
- `## [YYYY-MM-DD] lint | ...`

Lấy 5 mục gần nhất: `grep "^## \[" wiki/log.md | tail -5`.

---

## 9. Khởi động mỗi phiên

1. Đọc `CLAUDE.md` (file này).
2. Đọc `wiki/tong-quan.md` để nắm chủ đề.
3. Đọc `wiki/index.md` để biết wiki đang có gì.
4. Xem 5 mục nhật ký gần nhất trong `wiki/log.md`.
5. Hỏi người dùng muốn làm gì: **nạp nguồn**, **truy vấn**, hay **rà soát**.
