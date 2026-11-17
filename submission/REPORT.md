# Báo cáo Day 08 — 2A202603292 - Lê Minh Thắng

## 1. Model và recipe (Part 2–3)

| Lớp | Model + cỡ | Ngưỡng | Căn cứ |
|---|---|---|---|
| person / bicycle / motorbike / bus / truck | YOLO26n | 0.3 | Đã thử ngưỡng 0.25 và 0.5 ở r01, r02. threshold_sweep không tìm được ngưỡng đạt recall mục tiêu cho person, motorbike; các lớp còn lại có dưới 5 vật gold nên giữ mặc định. |
| car | YOLO26n | 0.45 | threshold_sweep gợi ý recall 100%, precision 67% trên 8 vật gold. |
| three_wheeler | YOLOE-26s | 0.15 | r03 dùng các prompt tuk-tuk, cyclo, cargo tricycle. threshold_sweep gợi ý recall 100%, precision 78% trên 7 vật gold. |

Nguồn: submission/model_log.csv và submission/qc/threshold_sweep.md.

Lệnh gộp đã dùng:

```powershell
python scripts/merge_prelabels.py work/prelabel/yoloe_all/pre.coco.json work/prelabel/y26_all/pre.coco.json --thr "car=0.45,three_wheeler=0.15" --conf 0.3 --iou 0.6 --split "batch,gold" --out work/prelabel/merged_review/pre.coco.json
```

- Phiên bản SAM: SAM 2.1 base, trọng số sam2.1_b.pt. Model đã chạy được bằng CPU trên máy RAM 32 GB; lượt thử r04 tạo 8 mask trên 2 ảnh, khoảng 2,98 giây/ảnh.
- Đổi ngưỡng không giải quyết được việc model không có lớp phù hợp hoặc gán sai lớp. Vì vậy tôi dùng YOLOE để tìm three_wheeler, sau đó kiểm tra và sửa trong CVAT.
- Giới hạn: ngưỡng được chọn và đánh giá trên cùng 4 ảnh gold nên số đo có thể lạc quan. Một số lớp có rất ít hoặc không có đối tượng gold; ngưỡng chỉ là ước lượng ban đầu.

## 2. AI error profile

Nguồn: submission/qc/review_stats.md. Script đã loại 24 đối tượng quanh vùng bẫy khỏi bảng profile để tránh tính lỗi nhân tạo thành lỗi tự nhiên của máy.

| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted | 247 | 247/326 = 75,8% | 326 box máy |
| edited | 43 | 43/326 = 13,2% | 326 box máy |
| deleted | 35 | 35/326 = 10,7% | 326 box máy |
| added | 239 | 239/534 = 44,8% | 534 đối tượng sau duyệt |

- Trong deleted và added có 7 trường hợp đổi lớp. Số vật máy sót thật theo cách tính của script khoảng 239 − 7 = 232.
- three_wheeler có 21 box bị xoá, 7 box sửa và 10 box giữ nguyên; person và motorbike cần thêm lần lượt 111 và 83 đối tượng. Các số này phản ánh những kiểu lỗi khác nhau, không đủ để xếp hạng chất lượng chỉ bằng số lượng tuyệt đối.
- Lớp tôi đánh giá cần chú ý nhất và ảnh minh hoạ: [đó là ignore_region cần chú ý xem có đủ 5 vật và mỗi vật nhỏ hơn 20px hay ko].
- So với 4 ảnh gold, máy có TP = 35, FP = 7, FN = 24:
  - Precision: 35/42 = 83,3%.
  - Recall: 35/59 = 59,3%, mẫu số là 59 đối tượng gold.
- Các số đo trên gold có giới hạn vì mẫu nhỏ và ngưỡng được chọn trên chính tập này.

## 3. Thiên kiến tự động hóa và ảnh bẫy

Nguồn: submission/qc/review_stats.md.

- Bắt được 7/12 bẫy = 58,3%, dưới ngưỡng 80%.
- Theo loại: missing 2/3; loose 1/3; extra 2/3; wrong_class 2/3.
- Bẫy lọt:
  - img_014.jpg và img_024.jpg: loose.
  - img_034.jpg: missing.
  - img_008.jpg: extra.
  - img_006.jpg: wrong_class.
- Nguyên nhân trong cách duyệt của tôi: [ làm chưa kỹ].
- Accepted 75,8% không chứng minh lượt duyệt đáng tin khi vẫn lọt 5/12 bẫy. Accepted đo mức nhãn được giữ lại; khả năng phát hiện lỗi phải xem thêm kết quả bẫy và QC.

## 4. Máy có đáng dùng không?

Nguồn: submission/time_log.csv.

- Gán tay gold: 35 phút/4 ảnh = 8,75 phút/ảnh.
- Review batch: 300 phút/24 ảnh = 12,5 phút/ảnh.
- Trong số liệu đã ghi, thời gian review mỗi ảnh cao hơn thời gian gán gold. Tuy nhiên hai tập khác số lượng vật và độ khó; tôi cũng đã quen quy tắc sau khi làm gold, nên đây không phải phép so sánh tốc độ có kiểm soát.
- Kết luận sử dụng của tôi: Máy đáng dùng đặc biệt các hình ảnh rõ nét và các trường hợp phổ thông nhưng các trường hợp khó. Ví dụ như ảnh nhòe hay vật khó kiểu như xe chở hàng... thường bị nhầm

Ba quy tắc giảm thiên kiến tôi sẽ áp dụng:
1. [TỰ ĐIỀN một hành động cụ thể trước khi nhìn nhãn máy].
2. [TỰ ĐIỀN cách kiểm riêng vật bị sót và box thừa/sai lớp].
3. [TỰ ĐIỀN cách kiểm biên box và tổ chức nghỉ/kiểm tra lại].

## 5. Mask SAM cho 3 vật (Part 5)

| Ảnh | SAM (phiên bản) | Số click | Mask gồm người lái / hàng hoá? | Lỗi quan sát | File trong masks/ |
|---|---|---|---|---|---|
| img_013 | SAM 2.1 base | 3: 2 dương, 1 âm | Còn lấy lẫn một phần người lái | Chưa tách sạch người khỏi mask xe; đã ghi CAL-01 | tuktuk_img013.jpg |
| img_004 — xe xanh lá | SAM 2.1 base | 2 dương | Không phát hiện lấy lẫn người/hàng trong lần kiểm bằng mắt | Chưa phát hiện lỗi rõ; chưa đo độ chính xác mask | tuktuk_green_img004.jpg |
| img_004 — xe xanh dương | SAM 2.1 base | 2 dương | Không lấy người đứng bên cạnh | Chưa phát hiện lỗi rõ; chưa đo độ chính xác mask | tuktuk_blue_img004.jpg |

- Đối chiếu mục 4.2: cần kiểm biên phần nhìn thấy, không lấy bóng đổ, kiểm khoảng trống lớn và phần người bị lấy lẫn. Không coi việc script tạo được mask là bằng chứng mask hoàn toàn đúng.
- Quyết định của tôi ở CAL-01: mask xe chỉ gồm phần xe nhìn thấy; loại phần người lái khỏi mask xe.
- Không thử Interactor SAM trong CVAT; sử dụng script SAM offline.

## 6. Calibration (Part 6)

- Đã ghi 3 quyết định trong submission/qc/calibration_decisions.csv:
  - CAL-01, img_013, loi_may: mask SAM lấy lẫn người lái; loại phần người khỏi mask xe.
  - CAL-02, img_024, cau_hoi_mo: vật rõ và đủ điều kiện thì gán riêng; vùng nhòe không tách được cá thể thì dùng ignore_region.
  - CAL-03, img_021: làm rõ cách gán xe che nhau, mỗi xe một box phần nhìn thấy; giữ nguyên ngưỡng che khuất và kích thước của v1.0.
- Các nội dung đã được ghi trong submission/guidelines/guideline_v1.1_calibrated.md, kèm ảnh ví dụ và lịch sử thay đổi.
- CAL-03 là làm rõ quy tắc hiện có, không phải thay đổi ngưỡng.
- Nguyên nhân gồm lỗi mask của máy, câu hỏi về phạm vi ignore_region và khó khăn khi áp dụng quy tắc che khuất.
- Quyết định quan trọng nhất theo tôi: [TỰ CHỌN CAL-01/CAL-02/CAL-03 và nêu lý do ngắn].

## 7. QC và quyết định (Part 6)

- Mẫu: 6 ảnh, gồm 1 ca khó và 5 ảnh ngẫu nhiên, seed 42; tổng 89 đối tượng. Người kiểm: Lê Minh Thắng. Hạn chế: tôi vừa là người duyệt vừa là người kiểm.
- Lỗi theo đối tượng: 3/89 = 3,4%.
- Lỗi theo ảnh: 3/6 = 50%, vượt ngưỡng Release ≤ 2%.
- Nguồn: submission/qc/qc_report_auto.md và submission/qc/qc_review.csv.

### Các lỗi phát hiện

| Ảnh | Object ID | Loại lỗi | Mô tả |
|---|---|---|---|
| img_006.jpg | 70 | sai_vi_tri_kich_thuoc | ignore_region sai vị trí, cần chuyển sang vùng cần bỏ qua. |
| img_013.jpg | 112 | sai_vi_tri_kich_thuoc | Box person chưa bao hết phần chân nhìn thấy. |
| img_031.jpg | 471 | sai_vi_tri_kich_thuoc | Box bicycle bao cả gánh hoa quả; cần kiểm lại phạm vi xe theo guideline, phân biệt giỏ cố định và hàng nhô ra. |

- Cả 3/3 lỗi ghi nhận thuộc vị trí/kích thước, cần siết bước kiểm phạm vi box.
- Cận trên Wilson 95% của tỉ lệ ảnh lỗi thật khoảng 81%. Không thể chứng minh đạt ngưỡng 2% từ mẫu nhỏ này.
- Script kiểm cấu trúc nhãn và tính số đo theo phiếu QC; không tự xác nhận box ôm sát vật, lớp đúng thực tế hoặc tìm mọi vật mà cả người và máy cùng bỏ sót.

**Quyết định: Rework**, ngày 2026-11-17.

Tôi quyết định Rework vì ảnh lỗi 3/6 = 50%, lỗi vị trí/kích thước lặp lại và bắt bẫy 7/12 = 58,3%, dưới ngưỡng 80%.

Việc cần làm tiếp: sửa lỗi trong CVAT, duyệt lại 24 ảnh theo checklist, cập nhật nhãn và chạy lại build_release, lấy mẫu QC và qc_report. Giữ kết quả lượt đầu; lượt sửa sau khi biết đáp án bẫy phải được ghi rõ, không coi là phép đo độc lập.

## 8. Ghi chú cá nhân

- Dùng ChatGPT để giải thích thuật ngữ, hướng dẫn cài đặt, lệnh chạy, thao tác CVAT và hỗ trợ định dạng nhật ký/báo cáo từ thông tin tôi cung cấp. Tôi tự gán, review và kiểm ảnh; đối chiếu bằng guideline và các script. Cần đọc lại nội dung được hỗ trợ trước khi nộp.
- Gold có bị nhiễm không: trước khi hoàn tất gold, tôi đã hỏi AI về guideline, công cụ, kích thước và gửi ảnh xin kiểm tra cách làm. AI đã có phản hồi trên một số ảnh; tôi công khai việc này và không khẳng định gold hoàn toàn độc lập với hỗ trợ AI.
- Máy: Windows, RAM 32 GB, Python 3.12.9; chạy model bằng CPU. CVAT local Docker được nâng từ 2.74.1 lên 2.81.0.
- Vướng mắc: Ubuntu dùng Python 3.14 nên chuyển sang môi trường Python 3.12 trên Windows. Docker gặp lỗi DNS khi tải image; đổi sang mạng điện thoại rồi tải thành công.
- Không dùng data/cache/ thay cho chạy model; đã tự chạy YOLO26, YOLOE và SAM.
- Chưa xác định bug script. Đã gặp lỗi tên file gold_manual..json có hai dấu chấm và sửa thành gold_manual.json.
- Điều tôi sẽ làm khác lần sau: [TỰ ĐIỀN thay đổi thực tế bạn muốn áp dụng].