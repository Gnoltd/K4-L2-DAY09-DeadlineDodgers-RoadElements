# Revision log

Guideline v1 = bản nháp đầu; v2 = sau calibration nội bộ; v3 = sau blind handoff. Mỗi lần tăng `Version` trong
`02_guideline.md`, thêm một hoặc nhiều dòng vào bảng: đổi gì và vì sao, kèm bằng chứng (sample_id, dòng
calibration report, câu hỏi trong clarification log, feedback của peer).

Cột Version ghi dạng `v1`, `v2`, `v3` — `make status` tìm dòng bảng có `v2` và dòng có `v3`.

| Version | Đổi gì | Vì sao | Bằng chứng |
|---|---|---|---|
| v1 | Bản nháp đầu: 10 mục; label `traffic_sign` + `sign_group` (prohibitory/mandatory/danger/priority/information/supplementary/unknown) + `ego_relevant` (relevant/not_relevant/uncertain) + `visibility` (clear/partial/poor) + `needs_review`; nhóm theo chức năng dùng chung cho biển Đức và VN, đối chiếu QCVN 41:2024; không vẽ biển chỉ hướng/địa danh; ví dụ mục 9 chỉ dùng ảnh split example (GTS05, GTS06, GTS07) kèm ảnh minh hoạ trong `guideline_assets/` | Khởi tạo guideline trước calibration; review nội bộ phát hiện ví dụ GTS26 bị ghi nhầm "không có biển" và ví dụ cũ dùng ảnh blind | Mở cỡ gốc GTS02, GTS05, GTS06, GTS07, GTS09, GTS18, GTS21, GTS23, GTS24, GTS26, GTS27; QCVN 41:2024 Điều 11, 14, 15, 16.2, 18, 29, 33, 41, 42 |
| v2 | 4.3 dòng 8: biển nhắc lại trên dải phân cách / lề trái đường đôi và biển trên đảo giao thông ngay trước ego là `relevant`; "bỏ trống không phải cách escalate"; thêm ví dụ GTS04 (kèm ảnh) và GTS03 vào mục 9; thêm lỗi thường gặp 15; thêm mục Self-QC trước khi export; xác nhận tách `priority` và `supplementary` khỏi nhóm chung | Calibration 2 người (long, thaiha) trên 6 ảnh: đồng thuận count 91.7%, attribute 80%; lệch số khung ở biển bên trái GTS04, GTS08; cả hai bỏ trống attribute ở cùng các khung (GTS01, GTS10, GTS11); cả hai dồn hình thoi và tấm mũi tên vào `other` (GTS03). Giới hạn: hai export dùng schema cũ (5 class, `ego_relevance`) nên chưa đo được `priority`, `uncertain`, `partial`/`poor` | `06_calibration_report.csv` 6 dòng; `06_calibration_measure.csv` |
