# Tự soát (Slice B4-center — 3 frame: adasind_270517, adasind_271039, adasind_295948)

## Cảnh báo tự động phát hiện
- adasind_295948.jpg L1: chiều cao < H=40 px (Car tại ~451-467 xtl/xbr = 15.5 px rộng, xem xét xóa hoặc đặt ignore_region)
- adasind_295948.jpg L5, L6, L7: box có thể nằm trong ignore_region ego_body (bên trái frame, x=0–229) — đã ghi vào findings.csv để rework
- Tên task trong CVAT không chứa raw_fisheye — lỗi tên task (đã ghi findings)

## Checklist thủ công
- [x] Phạm vi H=40 và vật cần vẽ — Soát từng frame: adasind_270517 có xe Car, ThreeWheeler, Pedestrian đủ 40px; adasind_271039 không có ego_body (đúng theo rule); adasind_295948 có Truck, Bike, Pedestrian, box Car nhỏ ~16px đã ghi finding P1
- [x] lens_border và ego_body — lens_border từ prefill hiện diện đủ cả 3 frame (2 polygon/frame); ego_body: adasind_270517 không thấy thân xe nên không vẽ; adasind_271039 không thấy thân xe; adasind_295948 có ego_body polygon bên trái đúng vị trí gương/capo
- [x] Class sáu nhãn — Kiểm danh sách: Car, Truck, Bike, Pedestrian, ThreeWheeler dùng đúng; Bus không xuất hiện trong slice này (không có xe buýt); không nhầm lớp
- [x] Rider và Bike — adasind_270517: không có rider; adasind_295948: Bike source=file có người lái gộp đúng 1 box Bike; Pedestrian riêng đã tách đúng
- [x] Geometry trên ảnh fisheye gốc — Box bám phần nhìn thấy thực tế; các box ở rìa vòng kính bám theo viền cong, không nắn thẳng; lens_border phủ đúng vành đen
- [x] truncated và occluded — Kiểm từng box: Bike source=file truncated=true đúng (bị cắt biên); Pedestrian occluded=true khi bị xe che; không dùng lẫn lộn hai thuộc tính
- [x] Vật thiếu hoặc box trùng — Soát lại 3 frame: không có box trùng trên cùng vật; đã ghi finding về Car nhỏ <40px ở adasind_295948 (P1 rework); không bỏ sót vật ≥40px trong vùng hợp lệ
- [x] ignore_region có reason — Mỗi polygon ignore_region đều có attribute reason hợp lệ (lens_border hoặc ego_body); không có polygon nào thiếu reason
- [x] Tên task raw_fisheye và export CVAT 1.1 — Tên task trong CVAT thiếu "raw_fisheye"; export đúng định dạng CVAT for images 1.1; đã ghi finding P3 để sửa lần sau

## Fill ratio (K12)
chưa vẽ polygon K12 (degrade — thiếu thời gian trong lab)
