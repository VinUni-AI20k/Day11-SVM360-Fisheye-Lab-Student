# Guideline vai B — QA Reviewer (Người Soát Nhãn Độc Lập)

> **Lab Day11 · SVM 360 Fisheye · Nhóm 3 người · 240 phút (P0–P6)**
> Bạn soát nhãn của A bằng ảnh và guideline, **khi chưa thấy reference hay model**. Mỗi nhận xét của bạn phải truy được về ảnh, đối tượng và rule cụ thể.

---

## Tổng quan quy trình & Thứ tự bắt buộc

```
A gán nhãn → A tự soát & khóa → B QA độc lập → C mở reference/model
→ A phản hồi & sửa → B kiểm lại ca sửa → C nộp
```

> **Bạn là người giữ tính khách quan của vòng QA.** Xem reference trước khi chốt QA sẽ làm mất ý nghĩa của toàn bộ bước này.

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
| `submission/r2_qa/qa_review.md` không còn TODO | Cuối P3 | C, A | C kiểm đủ frame, object, rule |
| Ít nhất 3 dòng finding `round=r2_qa` trong `findings.csv` | Cuối P3 | C | C |
| Ảnh bằng chứng trong `submission/screenshots/` | Cuối P3 | C | C |
| Kết quả kiểm lại ca sửa (review hoặc note) | Cuối P5 | C | A xác nhận |
| Xác nhận trong `TEAMMATES.md` có đường dẫn bằng chứng | P6 | C | C |

---

## P0 (phút 0–40): Đọc luật, soát parking của A

### Bước 0.1 — Đọc tài liệu luật

Đây là thời gian quan trọng để bạn hiểu rule trước khi soát nhãn. Đọc các tài liệu sau:

1. **`docs/02-rules-vi.md`** — Luật gán nhãn (R01–R11):

   | Rule | Nội dung |
   |---|---|
   | **R01** | Vật cao ≥40 px trong vùng hợp lệ → bắt buộc có box |
   | **R02** | Box bám phần nhìn thấy trên ảnh fisheye gốc; không nắn thẳng |
   | **R03** | Rider: người lái + xe hai bánh = 1 `Bike`; người dắt = `Pedestrian` + `Bike` tách |
   | **R04** | Van chở người → `Car`; minibus → `Bus`; xe tải nhỏ → `Truck`; xe ba bánh → `ThreeWheeler` |
   | **R05** | `truncated` = bị vòng kính/biên cắt; `occluded` = bị vật khác che. Độc lập nhau |
   | **R06** | `reason` hợp lệ: `ego_body`, `lens_border`, `crowd_or_group`, `unreadable`, `privacy_or_policy` |
   | **R07** | `ego_body` chỉ có khi thấy thân xe; frame không thấy thân xe thì không vẽ |
   | **R08** | `lens_border` đã import sẵn, chỉ soát lại |
   | **R09** | Không box nào nằm ≥50% trong `ignore_region` |
   | **R10** | P0 = sai phạm vi (ego/lens sai → cần sửa ngay) |
   | **R11** | `occluded` và `ignore_region.reason` không so với reference khi chấm khác biệt |

2. **`docs/04-selfqc-checklist-vi.md`** — 9 mục tự soát (để biết A đã kiểm gì)
3. **`docs/06-misconceptions-vi.md`** — 6 ngộ nhận thường gặp

### Bước 0.2 — Soát vai trò vạch trong bài parking của A

Nhìn ảnh `assets/parking/parking-lot-core.jpg` và đọc `submission/parking/observations.md` của A:

Câu hỏi cần trả lời khi soát:
- Vạch A vẽ có thực sự **chia ô đỗ** không, hay chỉ là vạch lối xe chạy?
- Một đoạn sơn/biên A **không** vẽ — lý do A ghi có thuyết phục không?
- Polygon `free_space` có bị xuyên qua xe/curb/cây không?
- `observations.md` có còn TODO không?

Ghi nhận xét vào ghi chú cá nhân (không sửa file của A, không ghi vào findings.csv lúc này).

---

## P1 (phút 40–70): C0 — Soát cách hiểu luật

### Bước 1.1 — Xem ảnh C0 và nhận xét

Mở task C0 của A trong CVAT (hoặc xem `p1_calib/annotations.xml`):

Kiểm từng điểm sau:
- Vật ≥40 px: có được box đầy đủ không? (R01)
- Box có bám đúng phần nhìn thấy trên ảnh fisheye gốc không? (R02)
- Rider/xe hai bánh: xử lý đúng R03 chưa?
- `truncated` và `occluded`: dùng đúng chưa? (R05)
- `ignore_region`: đủ `reason` hợp lệ chưa? (R06)

Đây là cơ hội để nhóm **thống nhất cách hiểu luật** trước khi vào slice chính.

### Bước 1.2 — Nêu nhận xét cách hiểu luật

Đưa nhận xét **theo ảnh, đối tượng và rule** để nhóm thống nhất. Ví dụ:
> "Frame adasind_XXXXXX, box L3 (Car): box hơi rộng hơn phần nhìn thấy trên rìa — theo R02 box bám phần nhìn thấy trên ảnh gốc."

Ghi chú riêng nhưng chưa điền vào `findings.csv` ở bước này.

---

## P2 (phút 70–125): Chuẩn bị QA

### Bước 2.1 — Chuẩn bị thứ tự và checklist QA

Trong khi A đang gán nhãn slice, bạn chuẩn bị:

1. **Lập thứ tự soát QA:**
   - Frame 1 → Frame 2 → Frame 3 (theo thứ tự slice)
   - Ưu tiên: phạm vi (P0) → class & geometry (P1) → attribute (P2)

2. **Lập checklist riêng theo rule:**
   - R01: Phạm vi — có vật nào ≥40 px chưa có box không?
   - R02: Geometry — box có bám đúng phần nhìn thấy không?
   - R03: Rider — xử lý đúng chưa?
   - R04: Class đặc biệt — ThreeWheeler/Bus/Truck đúng chưa?
   - R05: `truncated`/`occluded` — dùng đúng chưa?
   - R07: `ego_body` — có khi thấy thân xe, không có khi không thấy?
   - R08: `lens_border` — 2 polygon mỗi frame, soát đúng vòng kính?
   - R09: Không box nào trong ignore region?

3. **Mã rule để điền `findings.csv`:** R01, R02, R03, R04, R05, R06, R07, R08, R09

### Bước 2.2 — Chưa làm gì với slice chính

> **Tuyệt đối chưa:**
> - Xem reference, model hay compare của slice chính
> - Sửa nhãn của A
> - Đoán nguyên nhân khi chưa đủ bằng chứng

---

## P3 (phút 140–165): QA Độc Lập

> Đây là pha quan trọng nhất của vai B. Hãy tập trung và soát độc lập.

### Bước 3.1 — Nhận bàn giao từ A

Nhận từ A:
- File `submission/r1_craft/annotations.xml` (bản đã khóa)
- File `submission/r1_craft/lock.txt`
- Giá trị SLICE_NHOM
- Mã khóa `XXXX-XXXX`

Xác nhận:
- Đọc slice trong `lock.txt`, kiểm khớp với bảng phân vai trong `TEAMMATES.md`
- Ghi lại thời điểm nhận bàn giao

### Bước 3.2 — Chạy lệnh QA

```
py lab11.py qa --slice SLICE_NHOM --file "submission/r1_craft/annotations.xml" --code MA_KHOA_A_BAN_GIAO
```

- Nếu mã khóa không khớp: **dừng lại** và yêu cầu A kiểm lại bản khóa. Không đoán mã.
- Lệnh tạo `submission/r2_qa/qa_overlay.html` và mẫu `qa_review.md`

### Bước 3.3 — Soát từng frame

Mở `submission/r2_qa/qa_overlay.html` trong trình duyệt. Đi từng frame:

- Mã `L` trên box là nhãn của A → dùng làm `object_ref`
- Kiểm theo checklist 9 mục đã chuẩn bị ở P2

**Với mỗi nhận xét, ghi vào `qa_review.md`:**

```
**Frame:** adasind_XXXXXX
**Object ref:** L5
**Rule:** R02
**Quan sát:** Box rộng hơn phần nhìn thấy ở rìa phải của ảnh fisheye
**Cần kiểm lại:** Theo R02, box phải bám phần nhìn thấy trên ảnh gốc
```

**Tiêu chuẩn mỗi nhận xét:**
- ✅ Truy được về frame cụ thể
- ✅ Truy được về object_ref cụ thể (mã L trên overlay)
- ✅ Có rule_id cụ thể (R01–R11)
- ✅ Mô tả điều nhìn thấy trên ảnh
- ✅ Nêu điều cần kiểm lại
- ❌ Không suy nguyên nhân khi chưa có đủ bằng chứng
- ❌ Không điền cột `why` (đó là việc của C ở vai Diagnostician)

### Bước 3.4 — Điền header `qa_review.md`

Đầu file ghi:
```
Reviewer: [Tên B]
Chủ nhãn: [Tên A]
Slice: SLICE_NHOM
Mã khóa đã kiểm: XXXX-XXXX
Số nhận xét: N
Ca chưa rõ: (nếu có)
```

### Bước 3.5 — Lưu ảnh bằng chứng

Với mỗi nhận xét quan trọng:
- Chụp màn hình frame đó trong `qa_overlay.html`
- Lưu vào `submission/screenshots/` với tên mô tả
- Ghi đường dẫn ảnh vào nhận xét tương ứng trong `qa_review.md`

### Bước 3.6 — Điền `findings.csv` (ít nhất 3 dòng)

Thêm ≥3 dòng với `round=r2_qa`:

| Cột | Giá trị |
|---|---|
| `round` | `r2_qa` |
| `cell` | `L_only` |
| `rule_id` | R01/R02/... (phải có giá trị, không để trống) |
| `what` | Mô tả hiện tượng quan sát trên ảnh |
| `why` | **Để trống** (B không điền) |
| `severity` | Để trống (C điền) |
| `owner` | Để trống (C điền) |
| `action` | Để trống (C điền) |

Giữ nguyên header và các dòng craft của A.

### Bước 3.7 — Thông báo "QA đã chốt"

Thông báo cho nhóm, nêu rõ:
- File đã kiểm (`submission/r1_craft/annotations.xml`)
- Mã khóa đã kiểm (`XXXX-XXXX`)
- Số nhận xét
- Ca chưa rõ (nếu có)

> **Sau mốc này A mới được phản hồi.** C lưu mốc bàn giao và commit hồ sơ.

---

## P4 (phút 165–200): Phân xử

1. **Trình bày nhận xét đã chốt**: nêu hiện tượng và rule theo từng ca
2. **Nghe A giải thích** lựa chọn ban đầu
3. C mở ảnh và overlay đối chiếu; cùng nhóm đối chiếu
4. **Không sửa lại nhận xét ban đầu** để che việc từng bất đồng

---

## P5 (phút 200–215): Kiểm lại ca sửa

1. Giữ danh sách các ca đã yêu cầu sửa (từ `findings.csv` với `action=rework`)
2. Sau khi A lock bản v2, kiểm lại **đúng các ca A đã sửa**:
   - So với ảnh và rule
   - Kiểm ca sửa có đúng như đã quyết định không
3. Ghi kết quả kiểm lại vào `qa_review.md` hoặc ghi chú riêng:
   - Ca sửa đúng: xác nhận
   - Ca sửa chưa đủ: nêu cụ thể

---

## P6 (phút 215–240): Xác nhận

Trong `TEAMMATES.md`, xác nhận bằng tên thật:
- Đã QA độc lập **trước khi xem reference**
- Đã kiểm lại ca sửa
- Kèm tên file/đường dẫn bằng chứng

Soát bằng chứng và checklist của hồ sơ trước khi C push.

---

## Luật gán nhãn cần nắm (để soát A)

### 6 Class

| Class | Gồm |
|---|---|
| `Car` | Ô tô con, van chở người, xe bán tải nhẹ |
| `Bus` | Xe buýt, minibus |
| `Truck` | Xe tải, xe bán tải nhỏ, máy kéo |
| `ThreeWheeler` | Auto-rickshaw, e-rickshaw, xích lô |
| `Bike` | Xe hai bánh (có/không người lái) |
| `Pedestrian` | Người đi bộ, người dắt xe |

### Rider (R03) — Lỗi hay gặp nhất

| Tình huống | Nhãn đúng |
|---|---|
| Người ngồi trên xe hai bánh | 1 box `Bike` duy nhất (không box người riêng) |
| Người dắt xe (không ngồi lên) | `Pedestrian` + `Bike` tách riêng |
| Người ngồi trong ô tô/xe buýt | Không box người (chỉ box phương tiện) |

### `ignore_region` (R06–R09)

| `reason` | Khi nào có |
|---|---|
| `ego_body` | Thân xe ego nhìn thấy (không có ở frame 006840 và 271039) |
| `lens_border` | Vành đen ngoài vòng kính (đã import sẵn) |
| `crowd_or_group` | Cụm vật không tách được |
| `unreadable` | Vật không đọc được |
| `privacy_or_policy` | Dữ liệu riêng tư |

---

## Không được làm

| Hành động cấm | Lý do |
|---|---|
| Xem reference, model hay compare của slice chính trước khi chốt QA | Làm mất tính độc lập của QA |
| Thay mã khóa để hợp thức hóa file đã bị sửa | Vi phạm tính toàn vẹn |
| Sửa XML của A | Không phải vai của B |
| Chạy `mode` / `cvat` để đổi slice | Slice là cố định của nhóm |
| Điền cột `why` trong findings | Nguyên nhân do C ghi ở dòng `r3_diag` |
| Sao chép ghi chú cold review từ ảnh demo | Phải soát ảnh thật |

---

## Checklist trước khi chốt QA (cuối P3)

- [ ] Đã xem đủ mọi frame của slice; `qa_review.md` không còn TODO
- [ ] Mỗi nhận xét truy được về frame, object_ref, rule_id; ảnh bằng chứng đã lưu
- [ ] Có ít nhất 3 dòng `r2_qa` với `cell=L_only`, cột `why` để trống
- [ ] XML vẫn là bản A đã khóa, chưa bị chỉnh

## Checklist trước khi push lần cuối (P6)

- [ ] `TEAMMATES.md` có xác nhận với đường dẫn bằng chứng
- [ ] Kết quả kiểm lại ca sửa đã ghi vào review/note
- [ ] Không có mật khẩu, token hay `.env` trong repo
- [ ] Cùng C và A duyệt commit nộp

---

## Xử lý tình huống đặc biệt

| Tình huống | Xử lý |
|---|---|
| Mã khóa không khớp | Dừng, yêu cầu A kiểm lại và cung cấp đúng export đã khóa |
| Chờ quá 5 phút mà A chưa bàn giao | Chuyển sang cold review chính bản khóa của mình (solo mode) |
| Không chắc là lỗi hay không | Ghi nhận xét với `rule_id`, mô tả hiện tượng, đặt câu hỏi trong `why_to_check` |
| Ca có thể là reference sai | Ghi nhận xét theo ảnh, để C và A phân xử ở P4 với ảnh đối chiếu |

---

*Tài liệu tham khảo: `docs/02-rules-vi.md` · `docs/03-roles-rotation-vi.md` · `docs/04-selfqc-checklist-vi.md` · `docs/06-misconceptions-vi.md` · `GUIDE.md` · `RUBRIC.md`*
