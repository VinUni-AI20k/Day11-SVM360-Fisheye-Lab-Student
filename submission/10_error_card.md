# Error analysis card

## Zone × block

| zone | block | what | count |
|---|---|---|---:|
| center | B4 | ATTRIBUTE | 1 |
| center | B4 | BOX_GEOMETRY | 1 |
| center | B4 | IGNORE_SCOPE | 1 |
| center | B4 | MISSING | 5 |
| center | B4 | SPURIOUS | 13 |
| center | B4 | WRONG_CLASS | 1 |
| edge | B4 | MISSING | 1 |
| edge | B4 | SPURIOUS | 1 |
| mid | B4 | IGNORE_SCOPE | 4 |
| mid | B4 | MISSING | 2 |
| mid | B4 | SPURIOUS | 4 |
| unknown | B4 | MISSING | 2 |
| unknown | B4 | SPURIOUS | 1 |
| unknown | C0 | MISSING | 3 |

## Top defects
- SPURIOUS: 19 (ví dụ frame adasind_295948.jpg)
- MISSING: 13 (ví dụ frame adasind_019560.jpg)
- IGNORE_SCOPE: 5 (ví dụ frame adasind_295948.jpg)

## Phân tích của bạn

Hai bảng trên do `python3 lab11.py card` tính từ `findings.csv`; chạy lại lệnh sẽ cập nhật bảng và giữ nguyên mục này. Viết cho lỗi nổi bật nhất, dẫn frame/`object_ref`.

- **Nguyên nhân khả dĩ (`why`) và lập luận chuyên gia:**
  - Lỗi nổi bật nhất là **SPURIOUS (19 ca)** và **MISSING (13 ca)**, tập trung cao điểm tại zone `center` của block B4 (frame `adasind_295948.jpg`).
  - *Ca SPURIOUS do con người (`E1_annotator_error`):* Tại frame `adasind_295948.jpg`, người gán nhãn đã vẽ box Car tại `(451.92, 934.43)-(467.43, 961.42)` có chiều cao thực tế chỉ đạt 26.99 px, vi phạm ngưỡng $H=40$ px quy định tại R01. Đồng thời, ba box `L5, L6, L7` ở sườn trái bị lấn vào vùng `ignore_region` thân xe `ego_body` (vi phạm R09).
  - *Ca SPURIOUS do mô hình (`E4_model_domain`):* Mô hình YOLO26m huấn luyện trên camera phẳng nên gặp lỗi lệch miền dữ liệu nghiêm trọng, phát hiện ảo hàng loạt mảng bóng râm và kết cấu nắp capo thành xe cộ (`M_only` và `LM_noR`).
  - *Ca MISSING (`E1_annotator_error`):* Tại frame `adasind_019560.jpg` (C0) và `adasind_271039.jpg`, người gán nhãn bỏ sót các xe máy/xe đạp đang đỗ dưới mái che và khuất sau đuôi ô tô do lầm tưởng xe không có người lái thì không cần gán nhãn (vi phạm R01, R04).
- **Cách sửa và phân công trách nhiệm (`owner`):**
  - `owner: annotator (Vai A)`: Tiến hành sửa ngay trong P5 (Rework): Xóa box Car <40px tại frame `295948`, điều chỉnh/loại bỏ các box vi phạm R09 trong vùng `ego_body`, bổ sung box `Bike` occluded tại frame `271039` và gán thuộc tính `truncated=true` cho xe ở mép vòng kính.
  - `owner: data_ops / ai_team`: Đề xuất cập nhật pipeline AI để tự động áp mặt nạ (mask) vùng `ego_body` trước khi suy luận, ngăn chặn việc sinh ra các False Positive trên thân xe.
- **Bằng chứng kỹ thuật:**
  - Minh chứng hình ảnh: [c0_calib_missing_review.png](screenshots/c0_calib_missing_review.png) và file [qa_overlay.html](r2_qa/qa_overlay.html).
  - Đối chiếu dòng findings: Các dòng calib R4, R5, R6 và r1_craft `draft:box1`, `draft:ego_body+L5+L6+L7` trong [findings.csv](findings.csv).
  - Quy tắc quy chuẩn: R01 (ngưỡng kích thước tối thiểu $H \ge 40$px), R02 (ranh giới nhìn thấy), R05 (thuộc tính cắt cụt), R09 (ranh giới vùng cấm ignore).

