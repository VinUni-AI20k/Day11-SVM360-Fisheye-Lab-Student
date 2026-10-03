# C0 — Ghi chú soát trước khóa của vai A

Đã nhận `c0-export.zip` từ job 95 và soát trên ảnh gốc trước khi mở reference. Bản này có 3 box (1 ThreeWheeler, 2 Bike), hai polygon lens_border và một polygon ego_body. Khóa bản nộp ban đầu để phục vụ calibration; việc khóa không xác nhận nhãn đã đúng/đủ. Đây là ghi chú hỗ trợ vai A, chưa phải nhận xét độc lập hoặc kết luận của B/C.

## Quan sát trên bản export trước khi mở reference

- L1: ThreeWheeler bên trái, box (36.68, 767.12)–(100, 850.30), cao 83.18 px. Class phù hợp R04; vẫn cần kiểm biên dưới sát bánh xe theo R02.
- L2: Bike gồm người áo đỏ và xe máy, box (415.39, 732.86)–(559.39, 919.86), cao 187 px. Cách gộp đúng R03, cạnh phải có khoảng nền thừa cần soát R02.
- L3: Bike gồm người áo vàng và xe đạp, box (598.23, 744.86)–(723.23, 917.86), cao 173 px. Giữ quyết định gộp của bản nộp để phân xử calibration; tư thế chân/yên bị blur khiến chưa đủ chắc để xác nhận R03.
- Chỉ có ba box trong export. Các phương tiện đỗ cạnh cửa hàng chưa được gán nhãn; cần đối chiếu từng vật với ngưỡng 40 px, không suy rằng không có người lái thì ngoài phạm vi R01.
- Ego_body đã có nhưng đường bao thô ở mép trái; cần bám sát thân xe, tránh nền đường/bóng xe theo R07. Hai lens_border giữ từ prefill; vành trên/dưới cần soát độ khớp thực tế theo R08.
- Cả ba box có truncated=false và occluded=false. Không thấy ba box bị khung/vòng kính cắt; occluded phải soát bằng ảnh theo R05/R11, không lấy reference làm đáp án thuộc tính này.
- Đã xác nhận export có đúng một ảnh C0, đủ bảy label khai báo và định dạng CVAT 1.1. Không chỉnh XML export để thay nhãn đã vẽ trong CVAT.

- Task: `Day11 · ADASIND · C0 · raw_fisheye`
- Job: http://localhost:8080/tasks/95/jobs/95
- Ảnh: `assets/images/adasind_019560.jpg` (1080 × 1920 px).
- Labels: `assets/labels.json`; prefill: `assets/prefill/C0.xml`.
- Prefill có hai polygon `ignore_region` với `reason=lens_border`, không có box đối tượng. Import prefill mới là bước khởi đầu.

## Các quyết định cần áp dụng trên ảnh

1. **Người áo đỏ đội mũ trên xe máy, gần giữa ảnh** (khoảng x=420–540, y=730–925): gộp người và xe vào một box `Bike` theo R03. Box bám phần nhìn thấy của cả cụm trên ảnh gốc theo R02; không thêm `Pedestrian` cho người ngồi lái.
2. **Người áo vàng cạnh xe đạp, bên phải cụm xe máy** (khoảng x=590–730, y=745–915): phóng to để quyết định người đang ngồi lái hay đứng/dắt xe. Nếu đứng/dắt thì tách `Pedestrian` và `Bike`; nếu ngồi lái thì gộp một `Bike` theo R03. Phần bị làm mờ khiến tư thế khó đọc; không tự coi mọi người cạnh xe đạp là rider.
3. **Xe ba bánh có mái vàng ở phía trái đường** (khoảng x=40–100, y=770–855): dùng `ThreeWheeler` theo R04, không gộp vào `Car` hoặc `Truck`. Kiểm lại biên box và chiều cao trên ảnh gốc theo R01–R02.
4. **Thân xe gắn camera ở mép trái, kéo xuống góc dưới trái**: cần polygon `ignore_region` với `reason=ego_body` theo R06–R07. Chỉ bao phần thân xe/gương nhìn thấy; không bao bóng đổ trên mặt đường. Frame này không thuộc hai ngoại lệ không thấy ego.
5. **Hai polygon `lens_border`**: mở từng polygon đã import, kiểm quanh vành đen, sửa điểm lệch theo R08. Không tạo thêm polygon vành kính. Đặc biệt kiểm phần đỉnh và đáy vòng kính trên ảnh gốc.
6. **Các xe đỗ cạnh cửa hàng**: quét riêng từng vật từ trái sang phải, đo chiều cao ≥40 px và xác định class theo R01/R04. Các vị trí x≈290–440 và x≈890–970 cần phóng to; không dùng một box phủ cả dãy hoặc dùng `crowd_or_group` khi còn tách được từng vật.

Tọa độ trên chỉ giúp tìm đối tượng, chưa phải tọa độ box cuối đã tự soát.

## Tự kiểm trước khi khóa

Các ô còn mở vì bản soát có ca thiếu/không chắc nêu trên; chưa xác nhận đạt toàn bộ checklist. Giữ bản calibration ban đầu để đối chiếu và ghi phương án sửa sau khi phân xử.

- [ ] Phạm vi: quét đủ sáu class, đo ngưỡng 40 px trên ảnh gốc.
- [ ] Soát hai `lens_border`; thêm `ego_body` đúng phần thân xe nhìn thấy.
- [ ] Kiểm class từng phương tiện, nhất là xe ba bánh và xe đỗ.
- [ ] Kiểm rider; phân xử tư thế người áo vàng trước khi chọn gộp/tách.
- [ ] Box bám phần nhìn thấy; không suy phần khuất hoặc nắn thẳng vật cong.
- [ ] Kiểm độc lập `truncated` và `occluded` trên từng vật.
- [ ] Kiểm thiếu/trùng bằng một lượt quét toàn ảnh.
- [ ] Mỗi ignore có đúng một reason; không box nào nằm ≥50% trong một polygon ignore.
- [ ] Xác nhận đúng tên task, đúng một ảnh C0 và export CVAT for images 1.1 sau khi Save.

## Bàn giao

Đã khóa nguyên bản `c0-export.zip` bằng `python lab11.py lock calib c0-export.zip`, mã **78D7-86D3**. Sau khóa đã chạy `reference calib` và `compare calib` theo yêu cầu; xem `calibration_summary.md` và ba finding trong `submission/findings.csv`. Các ca thiếu/không chắc vẫn cần sửa hoặc phân xử trong CVAT; không thay nội dung XML đã khóa. B/C chưa được coi là đã review hoặc chốt kết luận chung.
