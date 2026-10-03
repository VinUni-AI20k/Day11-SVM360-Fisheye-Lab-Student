# Zone table (slice của bạn)

Lệnh `python3 lab11.py model` tự ghi bảng số (cùng cách đếm với `r1_craft/compare.md` và `model_compare.md`); chạy lại lệnh sẽ cập nhật bảng và giữ nguyên phần nhận xét. Bạn chỉ viết mục Nhận xét.

| Zone | n_ref | L missing | L spurious | M missing (`LR_noM` + `R_only`) | M thừa (`LM_noR` + `M_only`) | Lỗi L chính (`what`) |
|---|---:|---:|---:|---:|---:|---|
| center | 12 | 2 | 4 | 4 | 7 | SPURIOUS (3) |
| mid | 5 | 0 | 0 | 2 | 4 | — |
| edge | 3 | 0 | 0 | 1 | 1 | — |

## Nhận xét

- **Zone người (L) và model (M) gãy nhiều nhất:** Cả người gán nhãn (L) và mô hình (M) đều gặp lỗi nhiều nhất tại zone **`center`**:
  - Đối với người (L): Có 2 ca missing (bỏ sót) và 4 ca spurious (dư thừa), lỗi chính là `SPURIOUS` (3 ca do vẽ box Car nhỏ dưới ngưỡng $H=40$px và box lấn vào vùng `ego_body` ở frame `295948`). Ở zone `mid` và `edge`, người gán nhãn làm rất tốt (0 missing, 0 spurious).
  - Đối với mô hình (M): Zone `center` bị suy giảm nặng nhất với 4 ca missing và 7 ca spurious (dư thừa). Ở zone `mid` và `edge`, model tiếp tục bỏ sót (2 ca ở `mid`, 1 ca ở `edge`) và phát sinh 4 box thừa ở `mid`, 1 box thừa ở `edge`.
- **Giả thuyết nguyên nhân & Giới hạn dữ liệu:**
  - *Nguyên nhân người (L):* Vùng trung tâm có mật độ phương tiện phức tạp, nhiều lớp xe đỗ chồng chéo dẫn đến khó phân định ranh giới vật thể nhỏ ở phía xa và sơ suất để box lấn vào vùng thân xe `ego_body`.
  - *Nguyên nhân mô hình (M):* YOLO26m huấn luyện trên ảnh phối cảnh phẳng (pinhole perspective), không có khả năng thích ứng với biến dạng thấu kính fisheye và không được gán mặt nạ thân xe (`ego_body`), dẫn đến việc phát hiện nhầm các mảng kết cấu xe/mặt đường thành phương tiện (`M_only`).
  - *Giới hạn:* Đánh giá dựa trên tập con 3 frame của slice `B4-center`, chưa phản ánh đầy đủ mọi điều kiện thời tiết, góc chiếu sáng và các tình huống di chuyển động xuyên suốt hành trình xe.

