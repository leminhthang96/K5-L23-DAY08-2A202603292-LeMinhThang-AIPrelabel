# Thông tin học viên

- Họ và tên: Lê Minh Thắng
- MSHV: 2A202603292
- Lớp (L2 / L3 / khác): L2B
- Tài khoản GitHub: leminhthang96
- Repo bài nộp (URL): https://github.com/leminhthang96/K5-L23-DAY08-2A202603292-LeMinhThang-AIPrelabel
- Commit chốt bài (hash): cập nhật trước khi nộp
- Máy: Windows, RAM 32 GB; chạy model bằng CPU, không sử dụng GPU
- CVAT (Docker): v2.81.0

## Kết quả tóm tắt

Nguồn: submission/qc/review_stats.md, submission/qc/qc_report_auto.md và submission/time_log.csv.

- Máy: accepted 247/326 = 75,8%; edited 43/326 = 13,2%; deleted 35/326 = 10,7% (mẫu số: 326 box máy). Added: 239/534 = 44,8% (mẫu số: 534 đối tượng sau duyệt).
- So với gold: precision của máy 35/42 = 83,3%; recall 35/59 = 59,3%. Có 4 ảnh gold; ngưỡng được chọn trên chính tập này nên số đo có thể lạc quan.
- Bắt bẫy: 7/12 = 58,3%.
- Thời gian: gán tay 35/4 = 8,75 phút/ảnh; duyệt có pre-label 300/24 = 12,5 phút/ảnh. Hai tập khác độ khó nên không so sánh tốc độ trực tiếp.
- Lỗi QC: 50% theo ảnh (3/6); 3,4% theo đối tượng (3/89).
- **Quyết định: Rework** — ảnh lỗi vượt ngưỡng 2%, lỗi vị trí/kích thước lặp lại và bắt bẫy dưới 80%.