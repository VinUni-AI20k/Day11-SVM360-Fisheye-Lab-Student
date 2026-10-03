# Khung Phân loại & Chẩn đoán Lỗi SVM-360 Fisheye (Error Taxonomy)

**Vai trò C (Diagnostician & Coordinator):** Đỗ Trung Kiên (MSSV: 2A202602283)  
**Slice phân bổ:** B4-dense  
**Mục tiêu:** Thiết lập hệ thống phân loại nguyên nhân gốc rễ (Root Cause Analysis) cho các sai số phát hiện tại P3–P4.

---

## 1. Trục phân loại không gian (Radius Zone Binning)

Tỷ số bán kính quang học được chuẩn hóa theo tâm thấu kính fisheye:  
\frac{r}{R} = \frac{\text{Khoảng cách từ tâm BBox tới tâm vòng kính}}{\text{Bán kính vòng kính của frame}}

- **Zone center (/R < 0.35$):** Vùng trung tâm quang học, góc nhìn trực diện, độ méo tối thiểu (< 5%), đặc trưng thị giác rõ ràng nhất.
- **Zone mid (.35 \le r/R < 0.6$):** Vùng chuyển tiếp, bắt đầu xuất hiện độ cong quang học nhẹ, các đối tượng có xu hướng dạt sang hai bên.
- **Zone edge (/R \ge 0.6$):** Vùng rìa ngoại vi (Distortion Edge), góc tới $\ge 70^\circ$, đối tượng bị nén dẹt và kéo cong hình học cực mạnh. Đây là điểm mù mô hình và là nơi phát sinh phần lớn False Negative / Loose Box.

---

## 2. Ma trận Đối chiếu Ba Nguồn (Cell 3-Way Overlap)

| Ký hiệu Cell | Nguồn phát hiện | Ý nghĩa chẩn đoán kỹ thuật |
| :--- | :--- | :--- |
| LRM | Learner + Reference + Model | Cả 3 nguồn đồng thuận; trường hợp chuẩn xác cao. |
| LR_noM | Learner + Reference (Model sót) | Con người nhìn thấy nhưng AI bỏ sót do miền dữ liệu lạ (Domain Gap). |
| LM_noR | Learner + Model (Reference sót) | **Nghi ngờ lỗi Reference (E0)**: Nhãn chuẩn bị thiếu sót. |
| L_only | Chỉ Learner gán | Nguy cơ ảo giác / gán nhãn dư thừa (Spurious/FP do Annotator). |
| RM_noL | Reference + Model (Learner sót) | Annotator bỏ sót vật thể hiển nhiên (Annotator FN). |
| R_only | Chỉ Reference có | Learner và Model đều không nhận diện được (Vật thể quá mờ/nhỏ/tranh cãi). |
| M_only | Chỉ Model phát hiện | Model bị ảo giác (False Positive trên nền đường/bóng râm). |

---

## 3. Hệ thống Mã Nguyên nhân Gốc rễ (why)

1. **E0_reference_defect:** Lỗi thuộc về nhãn chuẩn (Teaching Reference bị thiếu box, nhầm class, hoặc box lệch quá 20%).
2. **E1_annotator_error:** Sai sót từ người gán nhãn (không tuân thủ quy tắc R01–R09, sót vật thể $\ge 40, nhầm Rider/Bike).
3. **E2_guideline_gap:** Khoảng trống quy chuẩn (tài liệu hướng dẫn chưa quy định rõ trường hợp vật thể bị che khuất $\ge 85\%$ hoặc vật thể cắt đôi tại Seam).
4. **E3_data_defect:** Khiếm khuyết dữ liệu gốc (ảnh rung nhòe motion blur nặng, chói lóa flare, vùng che mờ khuôn mặt/biển số đè lên vật thể).
5. **E4_model_domain:** Lệch miền mô hình (YOLO26m huấn luyện trên camera phẳng, gãy hoàn toàn ở zone edge thấu kính fisheye).
6. **E5_unresolved:** Ca bất đồng chưa đủ bằng chứng kết luận (cần thêm frame đối chiếu hoặc xin ý kiến Tech Lead).

---

## 4. Bốn Nhóm Lỗi Đặc Thù Cần Săn Tìm (P4 Focus)

- [ ] **Fisheye Distortion Edge ($\ge 70^\circ$):** Bounding box hình chữ nhật bị dư thừa diện tích nền do đối tượng bị bẻ cong theo hình cung tròn thấu kính.
- [ ] **SVM Stitching Seam Cross-Camera:** Vật thể nằm tại ranh giới ghép camera (trước - hông), thân xe bị xé đôi dẫn đến đúp nhãn hoặc lệch định danh.
- [ ] **Slot Marking Occlusion (Free-space & Parking lines):** Vạch kẻ đỗ xe bị bánh xe hoặc bóng râm che khuất, polyline bị ngắt quãng hoặc vẽ xuyên vật thể.
- [ ] **Perspective Skew:** Góc nhìn nghiêng kết hợp méo fisheye khiến vạch đỗ xe song song bị biến dạng hội tụ mạnh mẽ.
