# Báo cáo QC tự động (nháp)

> **Gợi ý** do script tính; bạn xác nhận. Script chỉ đếm theo phiếu kiểm bằng mắt người.

## Mẫu
- 6 ảnh (1 ca khó + 5 ngẫu nhiên), 89 đối tượng.
- taxonomy v1.0

## Số đo
| Chỉ số | Giá trị | Mẫu số |
|---|---|---|
| Lỗi **theo đối tượng** | 3/89 = 3.4% | 89 đối tượng trong mẫu |
| **Ảnh có lỗi** (dùng cho ngưỡng) | 3/6 = 50.0% | 6 ảnh trong mẫu |
| Máy: accepted / edited / deleted | 247/326 = 75.8% / 43/326 = 13.2% / 35/326 = 10.7% | 326 box máy |
| Máy: added | 239/534 = 44.8% | 534 đối tượng sau duyệt |
| Bắt lỗi bẫy | 7/12 = 58.3% | 12 bẫy |

## Top 3 loại lỗi
| Loại lỗi | Số lỗi | Ảnh ví dụ |
|---|---|---|
| sai_vi_tri_kich_thuoc | 3 | img_006.jpg, img_013.jpg, img_031.jpg |

**Lỗi hệ thống**: 'sai_vi_tri_kich_thuoc' lặp lại -> sửa quy tắc / recipe, không chỉ sửa từng ảnh.

## Giới hạn thống kê
- 3 ảnh lỗi trong 6 ảnh mẫu: cận trên 95% (Wilson) của tỉ lệ ảnh lỗi thật là **81%**. Ngưỡng 2% không thể được **chứng minh** bằng mẫu nhỏ như vậy: hãy nói rõ giới hạn này trong báo cáo.

## Gợi ý quyết định
**REWORK**

- ảnh có lỗi 50.0% > 2%
- lỗi hệ thống: 'sai_vi_tri_kich_thuoc' chiếm 3/3 lỗi
- bắt bẫy 58% < 80% -> lô duyệt chưa đáng tin

Cây quyết định: Hold (guideline chưa rõ / dữ liệu hỏng) -> Rework (> ngưỡng, lỗi lặp, bắt bẫy / nhất quán thấp) -> Release.
