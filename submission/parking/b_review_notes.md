# Ghi chú QA P0 — vai B (chưa chốt nét sát mép)

Nguồn: `submission/parking/annotations.xml` và `submission/parking/observations.md` của Tín trên nhánh `p0-parking-export` (commit `00f969b`); XML trùng từng byte với `D11/annotations.xml` đã nhận. Đối chiếu `assets/parking/parking-lot-core.jpg` và `docs/11-parking-lines-vi.md`. Người soát: Võ Trọng Nghĩa (vai B). Đây là ghi chú bàn giao của B; các nhận xét trên ảnh được Nghĩa cung cấp.

Bằng chứng nguồn: [XML parking đã soát](https://github.com/DTKien2005/Day11-SVM360-Fisheye-Lab-Student/blob/00f969b16d08b95247c7139dbc7bf8917a00879f/submission/parking/annotations.xml), [quan sát của A](https://github.com/DTKien2005/Day11-SVM360-Fisheye-Lab-Student/blob/00f969b16d08b95247c7139dbc7bf8917a00879f/submission/parking/observations.md), [ảnh gốc](../../assets/parking/parking-lot-core.jpg).

- `parking_line` số 1 và 2: Nghĩa quan sát đây khá rõ là vạch chia ô đỗ xe; có thể giữ nguyên.
- `parking_line` số 3 (mép trái, khoảng 30,681–26,720): Đoạn nhìn thấy ngắn; cần Tín chỉ ra trên ảnh vì sao đây là ranh một ô đỗ riêng lẻ. Nếu chỉ thấy phần sơn bị khung cắt mà không xác định được vai trò, ghi ca chưa chắc thay vì đoán.
- `parking_line` số 4 (mép phải, khoảng 922,597–960,603): Tín đã nêu đây là ca chưa chắc trong `observations.md`; cần cùng nhìn ảnh để xác nhận vai trò chia ô. Chưa yêu cầu xóa vạch chỉ vì nó ngắn.
- `free_space`: Phần phủ hiện tại tương đối phù hợp với mặt đường trống phía trước. Cần rà lại biên quanh nét 1 và 2 để tránh lấn sang ô đỗ.
- Phạm vi theo `docs/11-parking-lines-vi.md`: `free_space` là phần mặt đường trống **nhìn thấy được của lối xe chạy trong bãi**, không phải mọi vùng đường trống trong ảnh. Vì vậy không yêu cầu kéo polygon qua dãy ô, xe đỗ, curb hoặc vùng khuất. Cần kiểm biên hiện tại có bao đúng đoạn lối xe chạy thấy rõ không.
- Tín đã giải thích không gán biên sáng ngang phía xa (khoảng y=465) vì nó chạy dọc dãy/lối đi, không chia hai ô riêng lẻ. Lý do này phù hợp định nghĩa trong guideline; B cần xác nhận bằng ảnh.

Kiểm cấu trúc CVAT: đúng ảnh `parking-lot-core.jpg`, 4 polyline `parking_line`, 1 polygon `free_space`; `observations.md` không còn TODO. Đây không phải chứng nhận hình học đúng. Chưa chốt nét 3, 4 hoặc biên `free_space` khi chưa được người soát xác nhận trên ảnh. Ghi chú P0 này không thay `submission/r2_qa/qa_review.md` của vòng P3 fisheye.
