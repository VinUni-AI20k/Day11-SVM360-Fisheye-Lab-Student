# C0 — Kết quả calibration từ bản export job 95

## Bản đã khóa và thứ tự thực hiện

- Nguồn: `c0-export.zip`, đúng ảnh `adasind_019560.jpg`, ba box và ba polygon.
- Mã khóa: **78D7-86D3**; thời điểm khóa: 2026-09-29 11:59:39 +07:00.
- Reference C0 được mở lúc 11:59:45 +07:00, sau khóa. Không mở reference hoặc worked overlay của slice chính.
- Chạy tuần tự `lock calib`, `reference calib`, `compare calib`. XML đã khóa được giữ nguyên để bảo toàn bằng chứng của lượt calibration này.
- Ghi chú soát ảnh trước khi mở reference nằm ở `a_review_notes.md`. Khóa phiên bản không đồng nghĩa nhãn đã đạt toàn bộ checklist.

## Khác biệt cần giải thích theo ảnh và rule

`compare.md` báo 3 box ghép được và 3 box thiếu so với teaching reference, không báo box thừa. Center có 2 ghép/1 thiếu; mid có 0 ghép/2 thiếu; edge có 1 ghép/0 thiếu. Đây là số của một frame C0 theo matcher của lab, không phải điểm đạt hoặc kết quả trên bốn camera.

| Object reference | Quan sát trên ảnh | Quyết định đề xuất |
|---|---|---|
| R4 — Bike | Xe đỗ phía phải; thấy bánh và khung, box reference cao 94 px. Export chưa vẽ vật này. | Thiếu nhãn theo R01. Bổ sung box phần nhìn thấy theo R02; kiểm che khuất bằng mắt. |
| R5 — ThreeWheeler | Xe đỏ có mui ở cửa hàng phía trái người lái áo đỏ; cao 128 px theo reference. Có blur và xe hai bánh ở phía trước. | Có vật chưa được gán nhãn theo R01. Khi sửa phải xác nhận class và ranh riêng theo R02/R04, không sao chép nguyên box reference. |
| R6 — ThreeWheeler | Xe màu sáng bên trái xe đỏ; cao 82 px theo reference, có mui/thân phân biệt được. | Bổ sung nhãn theo R01/R04; xe đang đỗ vẫn nằm trong phạm vi. |

Bằng chứng: `../screenshots/c0_calib_missing_review.png` và `compare.html`. Ba dòng đầu của `../findings.csv` là các ca trên. Giữ nguyên `what=MISSING` và `cell=na` do compare tạo; chỉ bổ sung nguyên nhân, rule, chủ xử lý và phương án sửa.

Một bất đồng giải thích được: bản A không có box cho xe đạp R4; reference có. Trên ảnh vẫn thấy bánh/khung xe và chiều cao vượt 40 px, nên việc không có người lái không phải lý do loại vật theo R01 và định nghĩa Bike. Đây là đề xuất sửa dựa trên ảnh, không chỉ vì reference có box.

Ca còn cần B/C phân xử: người áo vàng và xe đạp đang được gộp một Bike ở cả bản A và reference. Matcher ghép được không chứng minh tư thế rider đã đúng R03; blur che chân/yên nên vẫn cần người soát. Hình học ego_body/lens_border cũng phải soát bằng mắt; không suy rằng không có finding polygon nghĩa là polygon đã chuẩn. R11 không chấm occluded hoặc reason bằng reference.

## Tín hiệu pre-label tổng hợp sau khóa

Đã xem `assets/worked/prelabel-quality.png` sau khóa C0. Hình mô tả một lần thử YOLO26m trên **48 frame**, ghi accuracy 0.392; khớp 226, thiếu 49, thừa 204; 400 conflict gồm extra_annotation 204, mismatching_label 98, covered_annotation 49 và missing_annotation 49. Không cộng 400 conflict thành số object lỗi duy nhất hoặc gán các số này cho C0.

Hình còn nêu 68/152 box pre-label không có reference nằm ≥50% trong ignore_region: ego_body 49, unreadable 13, crowd_or_group 6. Khác biệt xử lý ignore có thể làm số CVAT khác công cụ lab; phải kiểm frame, vùng ignore và cách ghép trước khi kết luận lỗi model. Không dùng một box lệch để khẳng định E4_model_domain.

## Sáu ngộ nhận — ghi chú chuẩn bị thảo luận

1. ThreeWheeler có thể gọi Truck: **sai**, R04 quy định class riêng.
2. Thêm Pedestrian cho người ngồi sau xe máy: **sai**, R03 gộp xe và mọi người ngồi trên xe thành Bike.
3. Xe ba bánh chở hàng không phải ThreeWheeler: **mơ hồ ngoài quy ước; lab gộp cả chở người và hàng**, R04.
4. Chỉ vẽ ego khi rõ tay lái/gương: **sai**, R07 yêu cầu vẽ khi thấy bất kỳ phần thân xe ego; không vẽ ở hai frame ngoại lệ không có ego.
5. Vẽ mới lens_border thay bản import: **mơ hồ về chất lượng nhưng hành động theo R08 là soát/sửa bản import**, không tạo mới.
6. Năm xe máy đỗ sát nhau vẫn vẽ năm Bike: **đúng nếu tách được từng vật**, R06 chỉ dùng crowd_or_group khi không phân biệt được ranh riêng.

## Bàn giao vai A

Đã có đủ năm file kỹ thuật P1 và ba finding có dẫn frame/object. Ba ca thiếu được đề xuất rework; chưa sửa nhãn trong CVAT, chưa xác nhận đạt toàn bộ self-QC. B cần review độc lập; C ghi kết luận chung sau thảo luận. Tài liệu này không giả định B/C đã đồng ý hoặc đã tham gia clinic.
