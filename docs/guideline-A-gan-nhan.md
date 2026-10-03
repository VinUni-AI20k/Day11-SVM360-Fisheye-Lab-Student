# Guideline vai A — Annotator (Người Gán Nhãn)

> **Lab Day11 · SVM 360 Fisheye · Nhóm 3 người · 240 phút (P0–P6)**
> Bạn là người **duy nhất vẽ nhãn** trong CVAT. Chất lượng nhãn, tính toàn vẹn của bản khóa và độ trung thực của self-QC nằm ở bạn.

---

## Tổng quan quy trình & Thứ tự bắt buộc

```
A gán nhãn → A tự soát & khóa → B QA độc lập → C mở reference/model
→ A phản hồi & sửa → B kiểm lại ca sửa → C nộp
```

> **Không được đảo thứ tự này.** Mở reference trước khi khóa làm mất giá trị của toàn bộ vòng QA.

---

## Luật chung cho cả nhóm

- Thứ tự bắt buộc: A gán nhãn → A tự soát và khóa → B QA độc lập → C mở reference/model → A sửa → B kiểm lại.
- Cả nhóm dùng một máy chính, một slice (lấy từ `submission/00_setup/mode.json`), và mỗi file chỉ một người sửa tại một thời điểm.
- Không dùng `docker compose down -v`. Không đưa mật khẩu, token hay `.env` vào repo.
- Cuối mỗi pha, C hỏi: đầu ra ở đâu, còn vướng gì, ai nhận bước tiếp theo.

---

## Đầu ra bạn phải giao

| Đầu ra | Thời điểm | Người nhận | Người kiểm |
|---|---|---|---|
| XML parking + `parking/observations.md` | Cuối P0 | C | B soát vai trò vạch |
| Bản C0 đã khóa (`lock calib`) + `p1_calib/lock.txt` | Cuối P1 | B, C | C mở reference sau lock |
| XML slice đã khóa + mã khóa (`r1_craft`) + `selfqc.md` | Cuối P2 | B, C | B soát nhãn, C kiểm đúng phiên bản |
| Dòng finding `r1_craft` trong `findings.csv` (≥3 dòng) | Cuối P2 | C | C |
| Nhãn v2 + `lock2` (sau rework) | Cuối P5 | B, C | B kiểm lại ca sửa |
| Xác nhận trong `TEAMMATES.md` có đường dẫn bằng chứng | P6 | C | C |

---

## P0 (phút 0–40): Thiết lập & Parking

### Bước 0.1 — Thiết lập môi trường (phút 0–20)

1. Mở Docker Desktop, chờ Engine khởi động.
2. Vào thư mục CVAT đã cài từ Day 2, chạy:
   ```
   docker compose start
   ```
   Nếu báo chưa có container: `docker compose up -d`
3. Mở trình duyệt vào `http://localhost:8080`, đăng nhập tài khoản Day 2.
4. Trong thư mục repo, kiểm môi trường (Windows dùng `py` thay `python3`):
   ```
   py lab11.py --help
   py lab11.py doctor
   ```
   Đọc `submission/00_setup/doctor.txt`: Python ≥3.9, CVAT kết nối, Git không track `.env`, repo Public.
   Nếu có dòng `✗` → sửa trước khi tiếp tục.
5. C chạy lệnh mode, A xác nhận slice:
   ```
   py lab11.py mode --members an,binh,chi --self an
   py lab11.py status
   ```
   Trên Windows thay `python3` bằng `py`. Ghi nhớ **SLICE_NHOM** từ `submission/00_setup/mode.json`.
6. Điền `submission/00_setup/sensor_context.md` bằng quan sát từ ảnh thực tế, không đoán thông số rig.

**Điểm dừng 0.1:** `py lab11.py doctor` không còn dòng `✗`; `mode.json` có slice; `sensor_context.md` không còn `TODO`.

---

### Bước 0.2 — Vạch ô đỗ và free-space (phút 20–35)

> Hãy tự nhìn ảnh và chọn vạch trước khi xem bất kỳ gợi ý nào.

**Ảnh cần xử lý:** `assets/parking/parking-lot-core.jpg`  
**File labels:** `assets/parking/labels.json`

1. Chạy:
   ```
   py lab11.py parking
   ```
   Lệnh in đường dẫn chính xác tới ảnh và labels.

2. Tạo task trong CVAT:
   - Tasks → `+` → Create a new task
   - Tên task: `Day11 · parking_line · public-sample` *(chép chính xác)*
   - Labels → Raw → Xóa nội dung có sẵn → Dán toàn bộ `assets/parking/labels.json` → Save
   - Select files → My computer → Chọn **chỉ** `parking-lot-core.jpg`
   - Submit & Open → Job #…

3. Vẽ nhãn:

   **polyline `parking_line` — vẽ ≥2 vạch:**
   - Draw new polyline → `parking_line` → Shape
   - Bấm từng điểm dọc theo phần sơn chia **ô đỗ** (phân chia từng ô riêng biệt)
   - Nhấn `N` hoặc Done để kết thúc mỗi polyline
   - Đường **dừng** tại chỗ sơn bị che hoặc kết thúc thực sự; không nối qua phần không nhìn thấy
   - ❌ Không vẽ: vạch lối xe chạy, mũi tên, vạch qua đường, biển báo

   **polygon `free_space` — vẽ ≥1 vùng:**
   - Draw new polygon → `free_space` → Shape
   - Bấm ≥3 điểm bao phần mặt đường trống **nhìn thấy được** của lối xe chạy
   - Kết thúc bằng `N`/Done
   - ❌ Không xuyên qua: xe đang đỗ, curb, cây, cột, vùng bị che

4. Export và nộp:
   ```
   Ctrl+S
   ```
   Menu → Export job dataset → CVAT for images 1.1 → Tắt Save images → Tải ZIP
   ```
   py lab11.py parking --file "/duong-dan/parking-export.zip"
   ```
   Lệnh lưu `submission/parking/annotations.xml`.

5. Điền `submission/parking/observations.md`:
   - Hai vạch đã chọn ở đâu (mô tả vị trí trong ảnh)
   - Một dấu sơn/biên đã **không** vẽ và lý do
   - Vùng trống dừng ở đâu và vì sao
   - Ca còn nghi ngờ (nếu có)

**Điểm dừng 0.2:** Lệnh parking nhận đúng 1 ảnh core, ≥2 polyline `parking_line`, ≥1 polygon `free_space`; `observations.md` không còn `TODO`.

---

### Bước 0.3 — Phác kế hoạch bốn camera (phút 35–40)

- Phác `submission/45_sampling_plan.csv`: 8 tổ hợp `front/rear/left/right × normal/hard`, tổng **200 frame**
- Ghi ý đầu cho `submission/46_gold_set_plan.md`
- Sau P4 quay lại bổ sung lý do dựa trên lỗi thực tế đã thấy

---

## P1 (phút 40–70): Hiệu chuẩn C0

### Bước 1.1 — Tạo task C0

1. Chạy:
   ```
   py lab11.py cvat C0
   ```
   Lệnh in ra: tên task (có `raw_fisheye`), đường dẫn `assets/labels.json`, danh sách ảnh cần nạp, XML prefill.

2. Tạo task trong CVAT:
   - Tên task: chép **đúng** tên lệnh in ra
   - Labels → Raw → Dán `assets/labels.json` → Save
   - Constructor kiểm: phải có **6 class động** và `ignore_region` có attribute `reason`
   - Select files → chọn **đúng ảnh** lệnh liệt kê
   - Submit & Open → Job #…
   - Trang task: Actions → Upload annotations → CVAT 1.1 → Chọn XML prefill → Xác nhận
   - Mở job kiểm: box và `lens_border` đã hiện

### Bước 1.2 — Soát và chỉnh nhãn C0

> Chưa mở teaching reference trong pha này.

Áp dụng luật (`docs/02-rules-vi.md`):

| Rule | Quy tắc |
|---|---|
| **R01** | Vật cao ≥40 px trong vùng hợp lệ → bắt buộc có box |
| **R02** | Box bám phần nhìn thấy trên ảnh fisheye gốc; không nắn thẳng vật cong ở rìa |
| **R03** | Rider: người lái + xe hai bánh = 1 box `Bike`; người dắt = `Pedestrian` + `Bike` tách riêng |
| **R04** | Van chở người → `Car`; minibus → `Bus`; xe tải nhỏ/máy kéo → `Truck`; xe ba bánh → `ThreeWheeler` |
| **R05** | `truncated` = bị vòng kính/biên khung cắt; `occluded` = bị vật khác che. Độc lập nhau |
| **R07** | `ego_body`: tự vẽ khi thấy thân xe ego; không vẽ nếu không thấy |
| **R08** | `lens_border`: soát lại 2 polygon đã import; sửa nếu lệch vòng kính thật |
| **R09** | Không box nào nằm ≥50% trong `ignore_region` |

### Bước 1.3 — Export và khóa C0

```
Ctrl+S → Menu → Export → CVAT for images 1.1 → Tắt Save images → Tải ZIP

py lab11.py lock calib "/duong-dan/c0-export.zip"
```

Chỉ sau khi khóa C0 mới để C chạy `reference calib` và `compare calib`.

**Điểm dừng P1:** `p1_calib/annotations.xml`, `lock.txt`, `reference.txt`, `compare.md`, `compare.html` có mặt. Ba dòng đầu trong `findings.csv` nêu đúng frame/object.

---

## P2 (phút 70–125): Slice chính — Gán nhãn, Tự soát, Khóa

### Bước 2.1 — Tạo task slice

1. Chạy:
   ```
   py lab11.py cvat SLICE_NHOM
   ```
   Lệnh in ra **đúng 3 ảnh** và file `assets/prefill/SLICE_NHOM.xml`.

2. Tạo task mới:
   - Tên task **phải chứa** `raw_fisheye`
   - Labels → Raw → Dán `assets/labels.json` → Save
   - Nạp **đúng 3 ảnh** lệnh liệt kê
   - Actions → Upload annotations → CVAT 1.1 → Nạp XML prefill
   - Kiểm: frame 1 có nửa box prefill và `lens_border`

### Bước 2.2 — Gán nhãn từng frame

Phím tắt: `F` (frame tiếp), `D` (frame trước)

Với mỗi frame, soát từng box prefill và quyết định giữ/sửa/xóa:

| Việc cần làm | Cách làm trong CVAT |
|---|---|
| Bỏ nhãn sai phạm vi | Chọn box → Del |
| Thêm vật thiếu (≥40 px) | Draw new rectangle → Label → Shape → kéo góc |
| Đặt `truncated` | Chọn box → sidebar → tick truncated |
| Đặt `occluded` | Chọn box → sidebar → tick occluded |
| Vẽ `ignore_region` | Draw new polygon → ignore_region → chọn `reason` |
| Soát `lens_border` | Xem 2 polygon import, sửa nếu lệch |
| Vẽ `ego_body` | Khi nhìn thấy thân xe ego trong frame đó |

**Lưu ý đặc biệt:**
- Frame `adasind_006840.jpg` và `adasind_271039.jpg`: **không** có thân xe ego → không vẽ polygon `ego_body`
- Vật cong ở rìa fisheye: box bám theo viền nhìn thấy thực tế, không nắn thẳng

### Bước 2.3 — K12 (tùy chọn nếu đủ thời gian)

Với 4 đối tượng lệnh `cvat` gợi ý:
1. Vẽ polygon theo viền nhìn thấy cùng class với box
2. `Ctrl+click` chọn cả box và polygon
3. Nhấn `G` → cùng `group_id`

Nếu trễ thời gian: chạy `py lab11.py degrade k12` và bỏ qua.

### Bước 2.4 — Export nháp và Self-QC 9 mục

```
Ctrl+S → Export → CVAT for images 1.1 → Tắt Save images → Tải ZIP nháp

py lab11.py draft "/duong-dan/r1-draft.zip"
py lab11.py fill
py lab11.py selfqc r1_craft
```

Mở `submission/r1_craft/selfqc.md`. Kiểm **đủ 9 mục theo đúng thứ tự** này:

| # | Mục kiểm | Điều cần kiểm trên ảnh |
|---|---|---|
| 1 | **Phạm vi** | Mọi vật ≥40 px trong vùng hợp lệ đều có box; vật <40 px không box |
| 2 | **lens_border & ego_body** | `lens_border` phủ đúng vành đen; `ego_body` có khi thấy thân xe; không thêm ego vào frame ngoại lệ |
| 3 | **Class** | Đúng 6 class; ThreeWheeler không bị gọi Bus/Truck; van chở người là Car |
| 4 | **Rider** | Người lái + xe hai bánh = 1 `Bike`; người dắt xe = `Pedestrian` + `Bike` tách |
| 5 | **Geometry** | Box bám phần nhìn thấy trên ảnh gốc; không một box phủ cả dãy xe |
| 6 | **Attribute** | `truncated` ≠ `occluded`; dùng đúng từng loại, không lẫn lộn |
| 7 | **Thiếu/trùng** | Không hai box cho một vật; không bỏ sót vật ≥H ở rìa |
| 8 | **ignore_region** | Mỗi polygon có đúng 1 `reason`; không box nào nằm trong ignore |
| 9 | **Tên task & định dạng** | Tên có `raw_fisheye`; export đúng CVAT for images 1.1 |

> Chỉ đổi `- [ ]` thành `- [x]` **sau khi đã kiểm thật sự trên ảnh**. Không tick hàng loạt.

### Bước 2.5 — Sửa lỗi và khóa bản cuối

1. Quay lại CVAT sửa những gì self-QC phát hiện
2. `Ctrl+S`, export **bản cuối** thành file ZIP **khác bản nháp**
3. Khóa:
   ```
   py lab11.py lock r1_craft "/duong-dan/r1-final.zip"
   ```
4. Lệnh in ra mã `XXXX-XXXX` — **lưu mã lại ngay, gửi ngay cho B và C**
5. Điền ≥3 dòng vào `findings.csv` (round=`r1_craft`, có `what`, `rule_id`)

**Điểm dừng P2:** `r1_craft/annotations.xml`, `lock.txt`, `selfqc.md` có 9 ô đã soát; mã khóa khớp bản cuối.

---

## Nghỉ (phút 125–140)

Bàn giao cho B và C **trước khi nghỉ**:
- File `submission/r1_craft/annotations.xml` (bản đã khóa)
- File `submission/r1_craft/lock.txt`
- Giá trị SLICE_NHOM
- Mã khóa `XXXX-XXXX`

B và C xác nhận nhận đúng bản; C ghi mốc commit.

---

## P3 (phút 140–165): Chờ B QA

- Chuẩn bị ghi chú bàn giao
- **Không dẫn dắt B, không tự điền nhận xét của B**
- Không sửa bất cứ thứ gì trong `submission/r1_craft/` trong pha này
- Nếu B hỏi lý do chọn nhãn, không trả lời trước khi B chốt QA

---

## P4 (phút 165–200): Phản hồi nhận xét và Phân xử

1. Sau khi B thông báo "QA đã chốt": nhận danh sách nhận xét từ B
2. Với **mỗi nhận xét của B**, phản hồi dựa trên ảnh và rule:
   - Giải thích lựa chọn ban đầu dựa trên ảnh thực và rule cụ thể
   - Nếu đồng ý là lỗi → chuẩn bị sửa ở P5
   - Nếu không đồng ý → nêu rõ frame, object, rule tương ứng
3. C mở ảnh overlay đối chiếu; nhóm quyết định: `rework`, `keep_with_reason`, hay `escalate`
4. C ghi quyết định vào `findings.csv` và `40_decision_log.csv`

---

## P5 (phút 200–215): Rework

1. Từ `findings.csv`, lọc các ca `action=rework` và `severity=P0/P1`
2. Chỉ sửa **đúng các ca đã thống nhất** trong CVAT
3. Xuất và khóa bản v2:
   ```
   Ctrl+S → Export bản sau sửa → ZIP mới

   py lab11.py lock rework "/duong-dan/rework-export.zip"
   ```
4. Gửi bản v2 cho B kiểm lại; C chạy `py lab11.py rework`
5. Giữ nguyên bản khóa P2 (`r1_craft`) để so sánh — **không xóa**

---

## P6 (phút 215–240): Xác nhận và nộp

Trong `TEAMMATES.md`, xác nhận bằng tên thật:
- Nhãn và bản v2 là export từ CVAT (kèm đường dẫn file bằng chứng)
- Giải thích các lần sửa có căn cứ (frame, rule, bằng chứng)
- Xác nhận đã tự soát trước khi xem reference

Hỗ trợ C kiểm `py lab11.py check` và cùng duyệt commit nộp cuối.

---

## Bảng 6 Class và cách ánh xạ đặc biệt

| Class | Gồm những gì |
|---|---|
| `Car` | Ô tô con, xe bán tải nhẹ chở người, van chở người |
| `Bus` | Xe buýt, minibus |
| `Truck` | Xe tải, pickup/xe tải nhỏ, máy kéo |
| `ThreeWheeler` | Auto-rickshaw, e-rickshaw, xích lô, xe ba bánh chở người/hàng |
| `Bike` | Xe hai bánh (có hoặc không người lái) |
| `Pedestrian` | Người đi bộ, người dắt xe (chưa ngồi lên xe) |

## Xử lý `ignore_region` — Bảng `reason` hợp lệ

| `reason` | Khi nào dùng |
|---|---|
| `ego_body` | Thân xe/gương/tay lái của xe gắn camera (chỉ khi nhìn thấy thực sự) |
| `lens_border` | Vành đen ngoài vòng kính (đã import sẵn, chỉ soát lại, không tự vẽ mới) |
| `crowd_or_group` | Cụm vật không tách được từng cái |
| `unreadable` | Vật không đọc được vì mờ/che gần hết |
| `privacy_or_policy` | Dữ liệu riêng tư hoặc cần xử lý theo policy |

## Mức ưu tiên soát (P0–P3)

| Mức | Loại lỗi | Hành động trong bài lab |
|---|---|---|
| **P0** | Sai phạm vi: ego_body sai, lens_border sai, ignore che nhầm | Sửa bắt buộc |
| **P1** | Thiếu/thừa/sai class hoặc hình học | Sửa bắt buộc |
| **P2** | Attribute, khác biệt nhỏ | Soát lại, giải thích nếu giữ |
| **P3** | Lỗi trình bày, ghi chú | Sửa trước khi nộp |

---

## Không được làm

| Hành động cấm | Lý do |
|---|---|
| Sửa tay `submission/r1_craft/annotations.xml` sau khi khóa | Phá vỡ tính toàn vẹn của mã khóa |
| Khóa prefill chưa sửa, hoặc dùng bản nháp làm bản khóa | Bản nháp ≠ bản cuối đã kiểm |
| Tick cả 9 ô self-QC khi chưa kiểm ảnh | Gian lận self-QC |
| Mở reference, model hay compare của slice chính trước P4 | Phá vỡ vòng QA độc lập |
| Đổi slice chung (chạy lại `mode --self`) | Cả nhóm dùng chung một slice |
| Bỏ dòng K12 hoặc xóa finding không đủ | Thiếu bằng chứng |

> **Nếu xuất nhầm:** ghi lý do vào `submission/40_decision_log.csv`, dùng `--relock` (xem `docs/01-guide-cvat-vi.md`), gửi lại file và mã mới cho B.

---

## Checklist trước khi bàn giao cho B (cuối P2)

- [ ] File khóa là export **sau sửa**, đã `Ctrl+S` trong CVAT
- [ ] `selfqc.md` có đủ 9 mục được soát thật (không tick bừa)
- [ ] Đã ghi mã khóa và gửi B đúng file + slice + mã
- [ ] B và C **xác nhận nhận** đúng bản; C ghi mốc commit
- [ ] Ít nhất 3 dòng `r1_craft` trong `findings.csv`

## Checklist trước khi push lần cuối (P6)

- [ ] `TEAMMATES.md` có xác nhận với đường dẫn bằng chứng
- [ ] `rework/annotations-v2.xml` và `lock2.txt` có mặt
- [ ] Không có mật khẩu, token hay `.env` trong repo
- [ ] Cùng C duyệt: `py lab11.py check` exit 0
- [ ] Cùng cả nhóm duyệt commit nộp

---

## Xử lý lỗi thường gặp

| Tín hiệu | Xử lý |
|---|---|
| CVAT không mở localhost:8080 | Kiểm Docker Desktop; `docker compose start` trong thư mục CVAT Day 2 |
| Import prefill không hiện box | Kiểm đúng task/slice, đúng `assets/prefill/SLICE_NHOM.xml`, format CVAT 1.1 |
| `draft`/`lock` báo sai frame | Export đúng job, đúng 3 ảnh lệnh `cvat` in ra |
| `lock` báo sai ảnh | Tạo/export lại task chỉ chứa đúng 3 ảnh |
| File đã đổi sau khóa, mã không khớp | Ghi lý do vào decision log, dùng `--relock` |
| Số local quality thấp | Mở `local_quality_conflicts.csv`, đối chiếu từng case với rule/reference; không sửa số báo cáo bằng tay |
| Trễ mốc 5 phút | Báo Lab Coach; `py lab11.py degrade <tên-bước>` theo thứ tự cắt giảm |

---

*Tài liệu tham khảo: `docs/02-rules-vi.md` · `docs/04-selfqc-checklist-vi.md` · `docs/11-parking-lines-vi.md` · `GUIDE.md` · `RUBRIC.md`*
