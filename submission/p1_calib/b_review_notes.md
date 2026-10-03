# Nhận xét P1 C0 — vai B

- Người soát: Võ Trọng Nghĩa.
- Chủ nhãn: Nguyễn Trí Tín.
- Nguồn: nhánh `p0-parking-export`, commit `56488ef` của repo nhóm.
- Frame: `adasind_019560.jpg`.
- Mã khóa bản được soát: `78D7-86D3`.
- SHA-256 XML: `78d786d384cf9e3f6ed5af0b07f5147826f4fb4c98f5637424b63cf023411019` (đã kiểm khớp khóa).
- Căn cứ: Nghĩa đã xem ảnh gốc cùng overlay nhãn A trên máy và xác nhận trực tiếp các nhận xét dưới đây; đối chiếu [luật gán nhãn](../../docs/02-rules-vi.md).
- Bằng chứng nguồn: [XML C0 đã soát](https://github.com/DTKien2005/Day11-SVM360-Fisheye-Lab-Student/blob/56488ef147cb6fdb8829d65479b93cce435fed60/submission/p1_calib/annotations.xml), [mã khóa](https://github.com/DTKien2005/Day11-SVM360-Fisheye-Lab-Student/blob/56488ef147cb6fdb8829d65479b93cce435fed60/submission/p1_calib/lock.txt), [ảnh gốc](../../assets/images/adasind_019560.jpg).

## Nhận xét đã xác nhận trên ảnh

| Object ref | Rule | Quan sát của Nghĩa | Nhận xét gửi A |
|---|---|---|---|
| L1 — `ThreeWheeler` | R02, R04 | Class tương đối phù hợp; box bám đúng đối tượng. | Có thể giữ theo quan sát hiện tại. |
| L2 — `Bike` | R03 | Người áo đỏ đang đứng/dắt xe máy, không ngồi điều khiển. | Cần tách thành hai box: `Pedestrian` cho người và `Bike` cho xe; mỗi box bám phần nhìn thấy theo R02. |
| L3 — `Bike` | R03 | Người áo vàng đang ngồi trên xe. | Có thể giữ một box `Bike` chung cho cả người và xe. |

## Những điểm còn cần rà soát

| Vị trí / phạm vi | Rule | Điều cần kiểm | Trạng thái |
|---|---|---|---|
| Các xe hai bánh phía phải, dưới mái che; chưa có mã L tương ứng | R01 | Kiểm từng đối tượng có cao từ 40 px trên ảnh gốc và thuộc vùng hợp lệ hay không; nếu thỏa điều kiện thì cần box. | Chưa xác nhận đối tượng cụ thể hoặc lỗi thiếu nhãn; chưa đo chiều cao. |
| L1–L3 | R05 | Không thấy box hiện tại bị vòng kính cắt nên `truncated=false` nhìn chung hợp lý. Rà riêng `occluded` nếu phần thân xe/người bị vật khác che đáng kể. | Chưa chốt lại từng giá trị `occluded`. |
| Hai polygon `lens_border` | R06, R08 | Biên phải bám phần viền tối ngoài trường nhìn. | Chưa xác nhận lỗi hình học cụ thể. |
| Polygon `ego_body` | R02, R06, R07, R09 | Chỉ phủ phần thân/cụm xe camera nhìn thấy ở mép dưới; không lấn sang mặt đường hoặc đối tượng giao thông. | Chưa xác nhận lỗi hình học cụ thể. |

## Bàn giao và trạng thái

Nhận xét P1 C0 của B đã được ghi nhận. L2 là yêu cầu sửa rõ ràng theo đánh giá của Nghĩa; A cần phản hồi và xử lý trên bản làm việc trong CVAT. Chưa có bằng chứng A đã sửa hoặc B đã kiểm lại bản sửa.

Giữ bản XML và mã khóa đã soát để truy vết. Nếu khóa lại C0, làm theo quy trình relock và ghi lý do vào decision log; không ghi đè âm thầm bản đã khóa.

Đây là ghi chú P1 theo guideline B, không phải báo cáo QA P3 và không được tính thành các dòng `round=r2_qa` trong `findings.csv`.
