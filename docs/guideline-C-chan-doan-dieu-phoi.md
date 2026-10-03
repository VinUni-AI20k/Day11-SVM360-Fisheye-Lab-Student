# Guideline vai C — Diagnostician & Điều phối (Người Chẩn đoán & Quản lý)

> **Lab Day11 · SVM 360 Fisheye · Nhóm 3 người · 240 phút (P0–P6)**
> Bạn giữ repo, theo dõi mốc, chạy báo cáo sau QA, chủ trì phân xử và nộp bài. Bạn phân xử bằng **bằng chứng**, không bằng số đông.

---

## Tổng quan quy trình & Thứ tự bắt buộc

```
A gán nhãn → A tự soát & khóa → B QA độc lập → C mở reference/model
→ A phản hồi & sửa → B kiểm lại ca sửa → C nộp
```

> **Bạn là người giữ nhịp toàn nhóm.** Cuối mỗi pha, bạn chủ động hỏi: đầu ra ở đâu, còn vướng gì, ai nhận bước tiếp theo.

---

## Luật chung cho cả nhóm

- Thứ tự bắt buộc: A gán nhãn → A tự soát và khóa → B QA độc lập → C mở reference/model → A sửa → B kiểm lại.
- Cả nhóm dùng một máy chính, một slice (lấy từ `submission/00_setup/mode.json`), và mỗi file chỉ một người sửa tại một thời điểm.
- Không dùng `docker compose down -v`. Không đưa mật khẩu, token hay `.env` vào repo.
- Cuối mỗi pha, **bạn** dành một phút hỏi: đầu ra ở đâu, còn vướng gì, ai nhận bước tiếp theo.
- Mẫu thông báo bàn giao: Đã làm → file/commit → cần người nhận kiểm gì → vướng mắc.

---

## Đầu ra bạn phải giao

| Đầu ra | Thời điểm | Người kiểm |
|---|---|---|
| Repo Public `KX-DAY11-TenNhom`, `TEAMMATES.md` hoàn chỉnh | P0 | A, B |
| Báo cáo `r3_diag/` (local quality, model compare, zone table, IoU sweep) | P4 | A, B đối chiếu ảnh |
| Dòng finding `r3_diag` đủ `why`, `severity`, `owner`, `action` | P4 | A, B |
| `40_decision_log.csv` (≥4 quyết định, ≥1 escalation) | P4–P6 | A, B |
| `10_error_card.md` mục Phân tích | P6 | A, B |
| `submission/rework/delta.md` | P5 | A, B |
| Các kế hoạch: `45_review_plan.md`, `45_sampling_plan.csv`, `46_gold_set_plan.md`, `50_exit_ticket.md` | P6 | A, B |
| `manifest.json`, commit nộp | P6 | Cả ba duyệt cùng một commit |

---

## P0 (phút 0–40): Thiết lập Repo và Khởi động

### Bước 0.1 — Thiết lập repo nhóm (phút 0–10)

> Phải hoàn thành trong 5 phút đầu tiên.

1. **Chốt ba vai (họ tên, MSSV)** và điền `TEAMMATES.md`:
   ```
   Vai A: [Họ tên] - [MSSV]
   Vai B: [Họ tên] - [MSSV]
   Vai C: [Họ tên] - [MSSV]
   ```

2. **Tạo repo nhóm từ template:**
   - GitHub: Use this template → Create a new repository
   - Đặt **Public** (bắt buộc để chấm bài)
   - Tên: `KX-DAY11-TenNhom` (K là khóa, X là số, TenNhom là tên nhóm)
   - Clone về máy chính bằng GitHub Desktop hoặc:
   ```
   git clone https://github.com/TenTaikhoan/KX-DAY11-TenNhom
   ```

3. **Chạy lệnh khởi tạo trong thư mục repo** (Windows dùng `py` thay `python3`):
   ```
   py lab11.py --help
   py lab11.py doctor
   py lab11.py mode --members an,binh,chi --self an
   py lab11.py status
   ```
   - Thay tên thật vào `--members`; `--self` là tên định danh của người vai A
   - Đọc kết quả `doctor`: phải không còn dòng `✗`

4. **Ghi slice từ `submission/00_setup/mode.json`** vào `TEAMMATES.md`:
   ```
   Slice nhóm: [SLICE_NHOM]
   ```
   Mọi chỗ `SLICE_NHOM` trong bài dùng giá trị này.

5. **Điền `submission/00_setup/sensor_context.md`:**
   - Quan sát từ ảnh thực tế trong `assets/images/`
   - Ghi rõ: chỉ một camera, không tự khẳng định thông số calibration
   - A/B kiểm lại theo ảnh

**Điểm dừng 0.1:** `doctor` không còn `✗`; `mode.json` có slice; `sensor_context.md` không còn `TODO`; `TEAMMATES.md` có đủ ba vai và slice.

---

### Bước 0.2 — Phác kế hoạch bốn camera (phút 35–40)

1. **Phác `submission/45_sampling_plan.csv`** (8 tổ hợp):
   ```
   camera,scenario,frames,risk,rationale
   front,normal,25,,
   front,hard,25,,
   rear,normal,25,,
   rear,hard,25,,
   left,normal,25,,
   left,hard,25,,
   right,normal,25,,
   right,hard,25,,
   ```
   Tổng = **200 frame**. Điền risk và rationale sau khi thấy lỗi từ P4.

2. **Ghi ý đầu cho `submission/46_gold_set_plan.md`**

---

## P1 (phút 40–70): C0 — Reference và Compare

### Bước 1.1 — Chờ A lock C0

**Chưa làm gì với reference cho đến khi A lock xong.**

Dấu hiệu A đã lock: A thông báo "đã chạy `lock calib`" và `p1_calib/lock.txt` có mặt.

### Bước 1.2 — Chạy reference và compare

```
py lab11.py reference calib
py lab11.py compare calib
```

### Bước 1.3 — Đọc báo cáo và ghi findings

Mở `submission/p1_calib/compare.md` và `compare.html`:
- Ghi bất đồng theo ảnh và rule
- Viết kết luận chung

Ghi **3 finding đầu** vào `findings.csv`:
- Dẫn đúng frame và đối tượng cụ thể
- Round: để trống hoặc dùng round phù hợp

> **Lưu ý:** Reference C0 là teaching reference đã sửa tay, có thể sai. Nếu thấy ca đáng ngờ, ghi frame, vật và rule — không biến reference thành chân lý.

**Điểm dừng P1:** `p1_calib/compare.md`, `compare.html`, `reference.txt` có mặt. Ba dòng đầu `findings.csv` nêu đúng frame/object.

---

## P2 (phút 70–125): Theo dõi mốc

### Bước 2.1 — Kiểm và theo dõi mốc của A

Trong khi A gán nhãn slice chính:
- Phác kế hoạch phủ bốn camera
- Theo dõi mốc thời gian (A có đang đúng tiến độ không?)
- **Chưa mở reference/model của slice chính**

### Bước 2.2 — Xác nhận bàn giao từ A (trước khi A nghỉ)

Trước giờ nghỉ, kiểm:
- Nhận đúng `submission/r1_craft/annotations.xml` (bản đã khóa)
- Nhận đúng `submission/r1_craft/lock.txt`
- Ghi nhớ SLICE_NHOM và mã khóa `XXXX-XXXX`

Ghi mốc bàn giao vào `TEAMMATES.md`.

---

## Nghỉ (phút 125–140)

Commit hồ sơ hiện tại sau khi nhận bàn giao từ A.

---

## P3 (phút 140–165): Kiểm B

### Bước 3.1 — Kiểm nhận xét của B có đủ bằng chứng

Trong khi B đang QA, không dùng báo cáo đối chiếu để gợi ý đáp án cho B.

Sau khi B QA xong, kiểm từng nhận xét trong `qa_review.md`:
- ✅ Có frame cụ thể không?
- ✅ Có object_ref (mã L) không?
- ✅ Có rule_id không?
- ✅ Có mô tả điều nhìn thấy không?
- ✅ Không chứa suy nguyên nhân khi chưa có bằng chứng không?

Kiểm `findings.csv`: dòng `r2_qa` có `rule_id` và `why` để trống.

### Bước 3.2 — Lưu mốc bàn giao B

Sau khi B thông báo "QA đã chốt":
- Lưu mốc bàn giao và link bằng chứng trong `TEAMMATES.md`
- Commit hồ sơ
- Chỉ sau mốc này A mới được phản hồi

---

## P4 (phút 165–200): Chẩn đoán và Phân xử

> Đây là pha phức tạp nhất của vai C. Hãy thực hiện đúng thứ tự.

### Bước 4.1 — Xác nhận và chạy báo cáo

1. Kiểm `mode.json` vẫn là slice chung (không ai đổi)
2. Đọc review của B trong `qa_review.md`
3. Chạy theo thứ tự:

```
py lab11.py reference r1_craft
py lab11.py compare r1_craft
py lab11.py local-quality
py lab11.py model
py lab11.py iou-sweep --iou 0.3,0.5,0.7
```

### Bước 4.2 — Đọc các báo cáo

| File báo cáo | Nội dung |
|---|---|
| `r1_craft/compare.md` + `compare.html` | Khác biệt nhãn A (L) với teaching reference (R) |
| `r3_diag/local_quality.md` + JSON | Phép tính offline từ rectangle theo rule lab |
| `local_quality_conflicts.csv` | Từng conflict theo frame/object |
| `local_quality_confusion.csv` | Ma trận nhầm class |
| `model_compare.md` + `.html` | Thêm model đóng băng (M) |
| `iou_sweep.md` | Kết quả ghép nhạy với ngưỡng IoU |
| `r3_diag/zone_table.md` | Bảng số do `model` tự ghi (chỉ viết mục Nhận xét) |

> Số local-quality và giao diện CVAT có thể khác do cách ghép/filter/ignore; đọc `docs/05-taxonomy-vi.md`.

### Bước 4.3 — Chủ trì phân xử từng ca

Với mỗi ca bất đồng hoặc conflict:

**Quy trình phân xử:**
1. **B nêu hiện tượng và rule** (từ `qa_review.md`)
2. **A giải thích lựa chọn ban đầu** (bằng ảnh và rule)
3. **C mở ảnh/overlay đối chiếu** → `compare.html`, `qa_overlay.html`
4. **Nhóm quyết định** một trong ba: `rework`, `keep_with_reason`, `escalate`
5. **C ghi lý do vào `findings.csv`** (dòng `r3_diag`) và `40_decision_log.csv`

### Bước 4.4 — Điền `findings.csv` cho dòng `r3_diag`

| Cột | Các giá trị hợp lệ |
|---|---|
| `round` | `r3_diag` |
| `why` | `E0_reference_defect` / `E1_annotator_error` / `E2_guideline_gap` / `E3_data_defect` / `E4_model_domain` / `E5_unresolved` |
| `severity` | `P0` (sai phạm vi) / `P1` (thiếu/thừa/sai) / `P2` (attribute) / `P3` (trình bày) |
| `owner` | `annotator` / `guideline` / `data_ops` / `ai_team` / `qa` |
| `action` | `rework` / `keep_with_reason` / `escalate` |

**Hướng dẫn chọn `why`:**

| Mã | Khi nào dùng |
|---|---|
| `E0_reference_defect` | Có bằng chứng reference sai (không chỉ "cảm giác") |
| `E1_annotator_error` | A rõ ràng vi phạm rule đã có |
| `E2_guideline_gap` | Rule chưa bao phủ ca này |
| `E3_data_defect` | Ảnh quá tối, mờ, bị che → không thể phân biệt được |
| `E4_model_domain` | Cần nhiều ca hoặc phép so sánh hỗ trợ (không chỉ 1 box lệch) |
| `E5_unresolved` | Chưa đủ bằng chứng → ghi rõ cần kiểm gì tiếp |

> **Chưa đủ căn cứ → dùng `E5_unresolved`** và nêu phép kiểm tiếp theo. Không bịa finding.

### Bước 4.5 — Viết mục Nhận xét trong `zone_table.md`

Bảng số do `model` tạo tự động — **không sửa bảng**.  
Bạn chỉ viết phần **Nhận xét**:
- Zone nào nhiều vấn đề nhất (center/mid/edge)
- Giả thuyết về nguyên nhân
- Giới hạn của slice 3 frame

### Bước 4.6 — Kiểm và sửa enum

```
py lab11.py triage
```

Sửa các dòng được báo lỗi trong `findings.csv`, chạy lại đến khi hết lỗi.

**Điểm dừng P4:** `triage` không báo lỗi; các báo cáo P4 có mặt; `zone_table.md` hết TODO; mỗi khác biệt đáng chú ý có lý do + bằng chứng.

---

## P5 (phút 200–215): Rework

### Bước 5.1 — Giao việc sửa cho A

Lọc `findings.csv` theo `action=rework` và `severity=P0/P1`:
- Ghi danh sách ca cần sửa
- Giao A sửa đúng các ca đó; B chuẩn bị kiểm lại

### Bước 5.2 — Chạy báo cáo rework

Sau khi A lock bản v2:
```
py lab11.py rework
```

### Bước 5.3 — Đọc `delta.md`

Mở `submission/rework/delta.md`:
- Matched, missing, spurious **trước và sau** theo zone
- Nếu số không cải thiện: giữ nguyên và giải thích nguyên nhân bằng ảnh/luật
- **Không sửa số trong báo cáo bằng tay**

**Điểm dừng P5:** `rework/annotations-v2.xml`, `lock2.txt`, `delta.md` có mặt; `delta.md` nêu thay đổi thật hoặc giới hạn.

---

## P6 (phút 215–240): Hoàn thiện và Nộp

### Bước 6.1 — Chạy error card

```
py lab11.py card
```

Lệnh tính bảng zone × block và top lỗi vào `submission/10_error_card.md`.  
Bạn chỉ viết mục **Phân tích của bạn**: nguyên nhân, cách sửa, bằng chứng.

### Bước 6.2 — Hoàn thiện các file còn lại

**`20_guideline_patch.md`** — Đề xuất sửa luật:
- Không sửa trực tiếp `docs/02-rules-vi.md`
- Đề xuất rule mới và bump `rules_version` (vd `v1.0.0` → `v1.1.0`)
- Kèm ví dụ cụ thể từ bài làm

**`30_escalation_ticket.md`** — Với ca `action=escalate`:
- Frame và ảnh chụp trong `submission/screenshots/`
- Expected impact (tác động ước tính)
- Owner (ai cần xử lý)
- Recommendation (đề xuất hành động)
- Cùng ca phải có `status=escalated` trong `40_decision_log.csv`

**`40_decision_log.csv`** — Ít nhất 4 quyết định:
- Ít nhất 1 dòng `status=escalated`
- Mỗi quyết định truy được với bằng chứng

**`45_review_plan.md`**:
- Hai lát cắt ADASIND cần review trước
- Cách soát độ phủ cho kế hoạch giả lập

**`45_sampling_plan.csv`** — Hoàn thiện 8 dòng:
- `front/rear/left/right × normal/hard`
- Số frame là **số nguyên dương**, tổng đúng **200**
- Mỗi dòng có `risk` và `rationale`

**`46_gold_set_plan.md`** — Theo từng camera:
- Ca hard case và lý do dễ sai
- Calibration/annotation space cần giữ
- Người review xác nhận trước khi gọi là gold
- Cách xử lý bất đồng giữa reviewer
- Khi nào cần refresh gold set
- Một ca seam/cross-camera cần policy

**`50_exit_ticket.md`** — Ba câu trả lời:
- Seam và tracking
- Tracking identity trong một camera
- Tự nhìn lại (dẫn đúng một ca trong bài)
- Ghi đóng góp A/B/C

### Bước 6.3 — Ảnh bằng chứng

Giữ ít nhất **2 ảnh minh chứng** trong `submission/screenshots/`  
Tránh đưa dữ liệu cá nhân hoặc bí mật vào repo.

### Bước 6.4 — Kiểm và push

```
py lab11.py triage
py lab11.py status
py lab11.py check
```

Sửa các mục `check` báo lỗi.  
Dùng GitHub Desktop xem thay đổi, commit, push origin.

Mở lại repo trên GitHub, kiểm:
- ✅ Repo ở chế độ **Public**
- ✅ Commit mới nhất xuất hiện
- ✅ `manifest.json` có `failed_gates` rỗng

### Bước 6.5 — Nộp bài

Gửi **1 link repo + 1 mã commit chốt** qua kênh lớp.

> Nếu sửa sau khi chốt: kiểm lại và cập nhật commit nộp; thông báo Lab Coach.

---

## Ngưỡng cần đạt trước khi nộp

| Tiêu chí | Ngưỡng tối thiểu |
|---|---|
| `findings.csv` tổng dòng | Ít nhất **12 dòng** |
| Giá trị `cell` khác `na` | Ít nhất **4 giá trị** |
| Dòng có M trong `cell` | Ít nhất **8 dòng** (`LRM`, `LM_noR`, `RM_noL`, `M_only`) |
| Dòng `r1_craft` | Ít nhất **3 dòng** |
| Dòng `r2_qa` | Ít nhất **3 dòng** (có `rule_id`, `why` trống) |
| Dòng `r3_diag` | Ít nhất **3 dòng** (đủ `why`, `severity`, `owner`, `action`) |
| Bằng chứng phủ zone | Phủ cả 3 zone: `center`, `mid`, `edge` |
| Decision log | Ít nhất 4 mục, 1 escalation |
| `45_sampling_plan.csv` | Đúng 8 dòng, tổng 200, số nguyên dương |
| `check` | Exit code 0 |
| `manifest.json` | `failed_gates` rỗng |
| File bắt buộc | Không còn `TODO` |

---

## Bảng mã `why` cho `r3_diag`

| Mã | Nghĩa | Điều kiện dùng |
|---|---|---|
| `E0_reference_defect` | Reference sai | Phải có bằng chứng cụ thể |
| `E1_annotator_error` | Lỗi annotator | A rõ ràng vi phạm rule đã có |
| `E2_guideline_gap` | Rule thiếu | Ca này rule chưa bao phủ |
| `E3_data_defect` | Lỗi dữ liệu | Ảnh quá tối/mờ/hỏng |
| `E4_model_domain` | Domain shift model | Cần nhiều ca hoặc phép so sánh, không chỉ 1 box |
| `E5_unresolved` | Chưa rõ | Phải ghi rõ cần kiểm gì tiếp theo |

---

## Không được làm

| Hành động cấm | Lý do |
|---|---|
| Mở reference, model hay compare của slice chính trước khi B chốt QA | Phá vỡ vòng QA độc lập |
| Sửa nhận xét ban đầu của B | Phải giữ nguyên để so sánh |
| Sửa số trong `delta.md` bằng tay | Số tự động từ lệnh, không chỉnh tay |
| Sửa thẳng `docs/02-rules-vi.md` | Đề xuất ở `20_guideline_patch.md` |
| Biểu quyết theo số đông thay cho bằng chứng | Phân xử phải dựa trên ảnh và rule |
| Bịa finding cho đủ số | Mỗi finding phải có bằng chứng thật |
| Đổi slice (chạy lại `mode --self` với tên mình) | Slice là cố định |
| Tự ghi thay xác nhận của A/B | A và B phải tự ký xác nhận phần mình |
| Dùng `E4_model_domain` chỉ từ 1 box lệch | Cần nhiều ca hoặc phép so sánh hỗ trợ |
| Xóa nội dung hay tạo file rỗng để vượt cổng `check` | Vi phạm tính toàn vẹn |

---

## Checklist trước khi push (P6)

- [ ] A và B đã xác nhận phần mình trong `TEAMMATES.md` bằng tên và đường dẫn bằng chứng
- [ ] Cả ba duyệt cùng một commit
- [ ] `check` exit 0; `manifest.json` có `failed_gates` rỗng
- [ ] Repo Public, ảnh và bằng chứng mở được
- [ ] Không có mật khẩu, token hay `.env` trong repo
- [ ] `findings.csv` đủ 12 dòng, phủ cả 3 zone
- [ ] `45_sampling_plan.csv` đúng 8 dòng, tổng 200
- [ ] Decision log ≥4 mục, ≥1 escalation
- [ ] Không còn `TODO` trong các file bắt buộc

---

## Lịch trình theo dõi mốc (C chủ động nhắc)

| Mốc thời gian | Việc C cần làm |
|---|---|
| Phút 5 | Chốt 3 vai và `TEAMMATES.md` |
| Phút 20 | Xác nhận `doctor` sạch, slice đã ghi |
| Phút 35 | Kiểm A đã xong parking, phác sampling |
| Phút 70 | Xác nhận A đã lock C0, C chạy reference calib |
| Phút 125 | Nhận bàn giao từ A, commit hồ sơ, cho nghỉ |
| Phút 140 | Nhắc B bắt đầu QA |
| Phút 165 | Nhận "QA chốt" từ B, lưu mốc, commit |
| Phút 165 | Chạy reference, compare, local-quality, model |
| Phút 200 | Giao A sửa, B chuẩn bị kiểm lại |
| Phút 215 | Chạy rework, đọc delta.md |
| Phút 215–240 | Hoàn thiện, check, push, nộp |

---

## Xử lý tình huống đặc biệt

| Tình huống | Xử lý |
|---|---|
| A trễ hơn 5 phút | Hỏi lý do, điều chỉnh kế hoạch; nhắc dùng `degrade` nếu cần |
| B chưa nhận được file sau 5 phút | B chuyển sang cold review; `mode.json` ghi lại, không tính lỗi |
| `check` báo lỗi | Đọc từng lỗi, chạy `status` để tìm bước kế; sửa trên ảnh/CVAT khi cần |
| Conflict không giải được | Dùng `E5_unresolved`, ghi phép kiểm tiếp theo; `action=escalate` |
| Số delta không cải thiện | Giữ nguyên và giải thích nguyên nhân; không sửa số báo cáo |
| Repo không còn Public | Vào GitHub Settings → đặt lại Public trước khi nộp |

---

*Tài liệu tham khảo: `docs/02-rules-vi.md` · `docs/03-roles-rotation-vi.md` · `docs/05-taxonomy-vi.md` · `docs/07-escalation-vi.md` · `docs/10-svm360-reading-vi.md` · `GUIDE.md` · `RUBRIC.md`*
