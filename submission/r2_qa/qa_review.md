# QA review · B4-center

Mã khóa: 6997-BF13

| frame | object_ref | rule_id | nhận xét |
|---|---|---|---|
| adasind_295948.jpg | L1 | R01 | Box Car tại (451.92, 934.43)-(467.43, 961.42) có chiều cao 26.99 px dưới ngưỡng H=40 px; vi phạm R01, kiến nghị xóa box này. |
| adasind_295948.jpg | L3 | R02;R05 | Box Bike khổng lồ ở mép phải (570, 605)-(1080, 1720) bị cắt ngang bởi mép vòng kính; cần kiểm tra thuộc tính truncated=true và edge_zone=true theo R05. |
| adasind_270517.jpg | L2 | R01;R02 | Pedestrian tại (332, 762)-(404, 904) cao 142 px; box ôm khít đối tượng nhìn thấy, class Pedestrian chính xác theo R01 và R02. |
| adasind_271039.jpg | L2 | R01;R02;R05 | Car tại (27, 816)-(96, 898) nằm ở dải rìa phía trước; phân loại đúng nhưng cần kiểm tra cẩn thận ranh giới occluded với lề đường. |

Ghi finding r2_qa: cell=L_only, rule_id có giá trị, why để trống.

