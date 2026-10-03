# Đối chiếu chất lượng cục bộ — rectangle

Teaching reference, không phải gold set đã phê duyệt; không có điểm đạt tự động.
Nguồn: export r1_craft đã khóa SHA256 `6997bf13e8daac9ae1cbf02fa422682f9eb3b960cc2812c21d587eaa9293e7f0`; slice `B4-center`.
Ghép hình học greedy một-một theo IoU ≥ 0.50, rồi so class; H ≥ 40 px.
Box trái nằm chủ yếu trong ignore_region reference không tính. Polygon, polyline, track không được chấm.
Đây là phép tính offline của lab, không phải báo cáo hay kết quả tương đương CVAT Premium.

Frame được tính: adasind_270517.jpg, adasind_271039.jpg, adasind_295948.jpg. Frame thiếu trong export: không.
TP=18; FP=4; FN=2; số lần đối chiếu=23; mean IoU của TP=0.963.

| Chỉ số | Micro | Macro | Nhãn thấp nhất |
|---|---:|---:|---:|
| accuracy | 0.783 | 0.948 | 0.913 |
| precision | 0.818 | 0.798 | 0.333 |
| recall | 0.900 | 0.931 | 0.800 |
| jaccard | 0.750 | 0.737 | 0.333 |
| dice | 0.857 | 0.827 | 0.500 |

| Nhãn | TP | FP | FN | Accuracy | Precision | Recall | Jaccard | Dice |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Bike | 3 | 0 | 0 | 1.000 | 1.000 | 1.000 | 1.000 | 1.000 |
| Car | 4 | 0 | 1 | 0.957 | 1.000 | 0.800 | 0.800 | 0.889 |
| Pedestrian | 6 | 1 | 1 | 0.913 | 0.857 | 0.857 | 0.750 | 0.857 |
| ThreeWheeler | 4 | 1 | 0 | 0.957 | 0.800 | 1.000 | 0.800 | 0.889 |
| Truck | 1 | 2 | 0 | 0.913 | 0.333 | 1.000 | 0.333 | 0.500 |

| Frame | TP | FP | FN | Accuracy | Precision | Recall |
|---|---:|---:|---:|---:|---:|---:|
| adasind_270517.jpg | 7 | 0 | 0 | 1.000 | 1.000 | 1.000 |
| adasind_271039.jpg | 9 | 2 | 1 | 0.750 | 0.818 | 0.900 |
| adasind_295948.jpg | 2 | 2 | 1 | 0.500 | 0.500 | 0.667 |

Confusion matrix: hàng = teaching reference; cột = export đã khóa.
`<missing>` là thiếu box; `<extra>` là box thừa. Xem `local_quality_confusion.csv`.

| Reference \ Export | Bike | Car | Pedestrian | ThreeWheeler | Truck | <missing> |
|---|---:|---:|---:|---:|---:|---:|
| Bike | 3 | 0 | 0 | 0 | 0 | 0 |
| Car | 0 | 4 | 0 | 0 | 1 | 0 |
| Pedestrian | 0 | 0 | 6 | 0 | 0 | 1 |
| ThreeWheeler | 0 | 0 | 0 | 4 | 0 | 0 |
| Truck | 0 | 0 | 0 | 0 | 1 | 0 |
| <extra> | 0 | 0 | 1 | 1 | 1 | 0 |

Chi tiết xung đột trong `local_quality_conflicts.csv`; dữ liệu máy đọc trong `local_quality.json`.
Mismatching label đóng góp một FP cho class vẽ và một FN cho class reference; attribute khác được báo riêng.
Micro accuracy đếm mỗi cặp ghép sai class là một lần đối chiếu; Jaccard đếm cả FP và FN.
Macro/worst bỏ nhãn không xuất hiện ở cả hai phía; chỉ số không có mẫu là N/A.
