# Label contract — Phố châu Á — BỔ SUNG v1.1 — 2A202603292 - Lê Minh Thắng

> Phần bổ sung cho v1.0. Các quy tắc không đề cập ở đây giữ nguyên.
> Các ca tương ứng được ghi trong submission/qc/calibration_decisions.csv.

## 1. Câu hỏi mở đã chốt (từ mục 6 của v1.0)

| # | Câu hỏi | Quyết định | Ảnh ví dụ | case_id |
|---|---|---|---|---|
| 2 | Cụm xe/người đông, nhòe, kể cả gần camera: gán từng vật hay ignore_region? | Vật phân biệt rõ và đủ điều kiện thì gán riêng. Chỉ khoanh ignore_region cho phần nhòe, không thể tách từng cá thể; không bao vật rõ chỉ vì khó vẽ. | img_024 | CAL-02 |

Các câu hỏi mở 1, 3, 4, 5, 6 và 7 của v1.0 chưa chốt trong lần này vì chưa có đủ bằng chứng để quyết định. Tiếp tục áp dụng các quy tắc hiện có; ca chưa chắc được đánh dấu can_review và ghi lại.

## 2. Ca khó mới và làm rõ quy tắc

| Ảnh | Tình huống | Máy làm gì (model nào) | Quyết định của bạn | Bổ sung hoặc làm rõ mục nào của v1.0 |
|---|---|---|---|---|
| img_013 — CAL-01 | Người lái nằm sát và che một phần xe tuk-tuk. | Mask SAM 2.1 base lấy lẫn một phần người lái vào xe. | Loại phần người lái khỏi mask xe. | Bổ sung mục 4.2: mask xe chỉ gồm phần xe nhìn thấy; người lái là đối tượng riêng. Không đoán phần xe bị người che. |
| img_021 — CAL-03 | Nhiều xe che nhau, khó xác định phạm vi từng box. | Không xác định lỗi máy cụ thể; đây là ca khó khi review. | Mỗi xe một box ôm phần nhìn thấy; bỏ qua xe bị che hơn 80% hoặc box cao dưới 20 px. | Làm rõ mục 4.1.2 và 4.1.6: không đoán phần khuất, không gộp nhiều xe vào một box. Không thay đổi các ngưỡng của v1.0. |

Ảnh mask minh hoạ CAL-01: ../masks/tuktuk_img013.jpg.
Ảnh gốc minh hoạ các ca: ../../data/images/img_013.jpg, ../../data/images/img_024.jpg và ../../data/images/img_021.jpg.

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Lý do (ca nào, case_id) |
|---|---|---|
| v1.0 | Bản giảng viên. | — |
| v1.1 | Làm rõ ignore_region cho cụm đông bị nhòe; tách người lái khỏi mask xe; làm rõ box cho xe bị che nhau. | img_024 — CAL-02; img_013 — CAL-01; img_021 — CAL-03. |