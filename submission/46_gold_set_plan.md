# Đề xuất gold set theo camera — tình huống giả lập

**Đầu bài:** 50.000 frame từ bốn camera SVM, ngân sách chọn 200 frame để review/gold. Đây là tình huống trên slide,
**không phải** 50.000 frame có trong repo. Phân bổ đúng 200 ở `45_sampling_plan.csv` cho bốn camera, mỗi camera có
normal và hard slice. “Gold set” ở đây là **kế hoạch tạo** reference sau kiểm chứng, không phải teaching reference
ADASIND hoặc nhãn bạn vừa vẽ. Nếu cần, dùng `notebooks/day11-svm360-colab.ipynb` để thử tổng phân bổ; notebook
không làm thay phần lý do.

| camera_id | Hard case cần chọn | Vì sao dễ sai | Annotation space / calibration cần giữ | Cách review trước khi gọi là gold |
|---|---|---|---|---|
| front | Normal: đường thoáng với vật ở giữa ảnh. Hard: vật nhỏ xa, chói sáng, vật ở góc trước. | Méo rìa và che khuất có thể làm lệch box hoặc bỏ sót vật. | Giữ ảnh fisheye gốc, phiên bản calibration, timestamp và quy tắc vùng hợp lệ. | Hai người gán nhãn độc lập trên cả normal/hard; phân xử theo ảnh gốc và rule trước khi khóa. |
| rear | Normal: cảnh lùi rõ. Hard: vật sát đuôi xe, mép kính hoặc bị thân xe che. | Vật gần biến dạng lớn; `ego_body` và `truncated` dễ bị lẫn với `occluded`. | Giữ ảnh gốc, calibration, timestamp và ranh thân xe nhìn thấy. | Soát độc lập rồi đối chiếu các ca mép kính, thân xe và vật gần. |
| left | Normal: vật ở giữa trường nhìn bên trái. Hard: vật ở góc trước/sau trái thuộc vùng chồng camera. | Cùng vật có thể hiện trên hai camera với hình dạng và kích thước khác nhau. | Giữ ảnh gốc từng camera, calibration, timestamp đồng bộ và định nghĩa seam. | Review nhãn từng camera trước; chỉ xét ghép xuyên camera sau khi thống nhất policy. |
| right | Normal: vật ở giữa trường nhìn bên phải. Hard: vật ở góc trước/sau phải thuộc vùng chồng camera. | Méo ở rìa và che khuất có thể gây thiếu nhãn hoặc box không khớp camera kề. | Giữ ảnh gốc từng camera, calibration, timestamp đồng bộ và định nghĩa seam. | Review nhãn từng camera trước; ca bất đồng seam cần người phân xử. |

- Chọn 200 frame theo `45_sampling_plan.csv` từ tình huống giả lập 50.000 frame; lấy mẫu rải theo chuyến/thời điểm/ánh sáng và tách frame gần nhau để tránh lặp cảnh. Mỗi camera có normal và hard; không coi ảnh ADASIND một camera là dữ liệu bốn camera thật.
- Trước khi gọi là gold, hai người soát độc lập trên ảnh gốc; ghi bất đồng theo frame, camera, object và rule, rồi để người phân xử chốt kèm lý do. Khóa phiên bản ảnh, guideline, calibration và nhãn sau phân xử.
- Refresh gold set khi đổi camera/vị trí lắp, calibration, đồng bộ thời gian, guideline hoặc phân bố cảnh; chọn lại ca đại diện và soát lại các ca mép/seam bị ảnh hưởng.
- Ca seam cần policy: một người đi bộ hiện ở góc trước trái của cả front và left. Cần cặp ảnh cùng timestamp, calibration/vùng chồng và quy tắc đầu ra trước khi quyết định giữ hai box theo camera hay ghép thành một object xuyên camera. Không tự tính hai box là nhãn trùng.
- Peer agreement hoặc quality report trên ADASIND một camera chỉ đo mức đồng thuận trên dữ liệu đó; nó chưa kiểm tra camera sau/hai bên, đồng bộ thời gian, calibration hay lỗi ở seam của hệ bốn camera.
- Sau P4: bổ sung ví dụ lỗi fisheye đã quan sát và điều chỉnh tỷ lệ hard nếu bằng chứng cho thấy rủi ro camera/zone khác giả định ban đầu. Các lý do hiện tại là giả thuyết thiết kế mẫu, chưa phải kết luận đo từ 50.000 frame.
