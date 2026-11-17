# Số đo review (det)

- Ảnh tính: 24 (batch,gold); box máy: 326; đối tượng sau duyệt: 534; ngưỡng accepted: IoU >= 0.9
- Đã **loại 24 đối tượng nằm quanh các bẫy** khỏi bảng này: lỗi nhân tạo không phản ánh chất lượng máy.

## 1. Bốn trạng thái (AI error profile)
| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 247 | 247/326 = 75.8% | 326 box máy |
| edited (sửa) | 43 | 43/326 = 13.2% | 326 box máy |
| deleted (xoá) | 35 | 35/326 = 10.7% | 326 box máy |
| added (vẽ thêm) | 239 | 239/534 = 44.8% | 534 đối tượng sau duyệt |

Trong số deleted + added, **7 trường hợp là đổi lớp** (máy gán sai lớp, box đúng chỗ): đó không phải máy bỏ sót. Số vật máy **sót thật** ≈ added − đổi lớp = 232.

### Theo lớp
| Lớp | accepted | edited | deleted | added |
|---|---|---|---|---|
| person | 140 | 21 | 6 | 111 |
| bicycle | 2 | 1 | 2 | 5 |
| motorbike | 66 | 12 | 4 | 83 |
| car | 26 | 2 | 1 | 28 |
| bus | 1 | 0 | 1 | 3 |
| truck | 2 | 0 | 0 | 4 |
| three_wheeler | 10 | 7 | 21 | 5 |

## 2. So với gold (nhãn gán tay từ đầu)
Ảnh gold: 4; đối tượng gold: 59 (mẫu số của recall)
| | TP | FP | FN | Precision | Recall |
|---|---|---|---|---|---|
| Máy (pre-label) | 35 | 7 | 24 | 83.3% (KTC 95%: 69–92%) | 59.3% (KTC 95%: 47–71%) |

*Lạc quan: ngưỡng được chọn trên chính các ảnh gold này (in-sample), và chỉ có 4 ảnh. Đọc khoảng tin cậy, đừng đọc con số đơn.*


(Ảnh gold không nằm trong nhãn sau duyệt: không tính residual / new error. Đó là chủ ý: duyệt lại ảnh bạn vừa tự gán tay sẽ đo trí nhớ, không đo thiên kiến.)

## 3. Ảnh bẫy
- Bắt được 7/12 = 58.3% (KTC 95%: 32–81%) (ngưỡng đạt: 80%). Chỉ 12 bẫy nên khoảng tin cậy rất rộng: đọc cùng danh sách bẫy lọt, đừng kết luận chỉ từ %.
  - missing: 2/3
  - loose: 1/3
  - extra: 2/3
  - wrong_class: 2/3
  - lọt: img_014.jpg (loose), img_024.jpg (loose), img_034.jpg (missing), img_008.jpg (extra), img_006.jpg (wrong_class)

## Gợi ý đọc số
- `deleted` cao → ngưỡng thấp hoặc ánh xạ nhãn sai; `added` cao → máy bỏ sót (xem lớp nào);
- `accepted` thấp (< 50%) → sửa mỗi box lâu hơn vẽ mới: đổi model/ngưỡng hoặc vẽ tay;
- bắt bẫy < 80% → duyệt lại lô, siết checklist (quét ảnh TRƯỚC khi nhìn nhãn máy).
