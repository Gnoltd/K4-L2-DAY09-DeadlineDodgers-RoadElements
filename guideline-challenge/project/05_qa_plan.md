# QA plan + quality gates

Tài liệu này chỉ ghi những gì đã có bằng chứng trong repo. Các mục chưa có thông tin thực tế được đánh dấu
`CHƯA CHỐT`, không tự suy đoán người phụ trách, cỡ mẫu hoặc threshold.

## Flow

Guideline → Calibration → Production → Self-QC → Review → Rework → Quality Gate.

- **Ai review, review bao nhiêu:** Member D điều phối QA; Member A hoặc C review độc lập, không tự review annotation của
  mình. Tự kiểm 100% ảnh; reviewer kiểm 100% ảnh/tag critical, mọi object `needs_review=true`, và 20% ảnh còn lại
  (làm tròn lên, tối thiểu 5 ảnh mỗi batch).
- **Chọn sample:** lấy mẫu ngẫu nhiên có seed ghi trong issue log, sau đó bảo đảm có critical, small/far,
  occlusion/truncation, ambiguity/escalation, `other`, và ranh giới LABEL/IGNORE. Nếu risk strata chưa xuất hiện trong
  phần ngẫu nhiên, thêm ảnh để đủ coverage.
- **Issue:** ghi trong `project/10_qa_issue_log.csv`; mỗi issue có sample, decision/geometry, severity, nguyên nhân,
  action, owner, trạng thái và bằng chứng. Reviewer chỉ đóng issue sau khi sửa được kiểm lại độc lập.
- **Guideline gap:** ghi bằng chứng vào `06_calibration_report.csv` hoặc `07_blind_handoff/peer_feedback.md`; đổi
  guideline v1 sau nháp, v2 sau calibration thật, v3 sau blind handoff thật; mỗi lần ghi revision tương ứng.

## Defect severity

Severity phản ánh hậu quả đối với downstream contract. Mapping dưới đây là cách phân loại theo các rule hiện có; nếu
downstream contract được chốt khác thì phải cập nhật trước khi QA.

| Severity | Định nghĩa cho project này | Ví dụ | Action mặc định |
|---|---|---|---|
| Critical | Bỏ sót hoặc gán sai một quyết định có rủi ro cao trong downstream contract, hoặc lỗi làm mất một decision critical trong gold/blind | Bỏ sót STOP trong blind, hoặc gán biển của hướng đối diện thành `relevant` khi evidence xác định `irrelevant` | Dừng gate; ghi issue, xác định nguyên nhân, rework và kiểm tra lại toàn bộ mẫu cùng rule |
| Major | Sai inclusion/exclusion, macro class, attributes hoặc geometry làm thay đổi kết quả chấm/đầu ra | Bỏ bảng phụ là biển độc lập; nhầm `danger` với `prohibitory`; box gộp nhiều mặt biển | Rework mẫu lỗi và rà soát các mẫu cùng rule; chưa PASS nếu còn lỗi major chưa đóng |
| Minor | Lỗi không làm thay đổi decision chính nhưng vẫn vi phạm quy ước annotation hoặc làm giảm khả năng kiểm tra | Sai thao tác/ghi chú hoặc sai nhỏ không ảnh hưởng nhóm; ví dụ cụ thể và ngưỡng: `CHƯA CHỐT` | Sửa trong vòng QC; theo dõi để xem có lặp lại thành lỗi major không |
| Minor | Lỗi không làm thay đổi decision chính nhưng giảm độ nhất quán hình học/metadata | Lệch box nhỏ nhưng IoU vẫn đạt ngưỡng, ghi chú thiếu nhưng object/class đúng | Sửa trong QC; theo dõi lỗi lặp |
| Question | Điểm chưa đủ bằng chứng để phân loại là lỗi annotation; cần xác minh rule, dữ liệu hoặc downstream contract | Không rõ vật là biển thật hay vật giống biển; guideline chưa nói tới và cần escalation | Ghi lại, chưa tự sửa gold; owner quyết định là guideline gap, data ambiguity hoặc execution error |

## Metrics

Tính riêng trên sample QA đã được chọn và ghi rõ mẫu số. Không gộp ảnh không có biển với object-level metric nếu cách tính
chưa được nhóm chốt.

| Metric | Cách tính | Vì sao phù hợp với bài toán |
|---|---|---|
| Inclusion / exclusion agreement | Số decision đúng về có vẽ/không vẽ trên tổng decision được review | Bắt lỗi bỏ sót biển, vẽ biển chỉ hướng, mặt sau, quảng cáo và vật giống biển |
| Macro-class accuracy | Số object có macro class đúng trên tổng object xác nhận thuộc ba macro | Đây là output phân loại chính của guideline |
| `needs_review` compliance | Số object tuân thủ đúng điều kiện bật/tắt `needs_review` trên tổng object được review | Kiểm tra các rule UNKNOWN/ESCALATE và tránh bật tuỳ tiện |
| Geometry pass rate | Số box đạt rule Rectangle và tolerance đã ghi trong guideline trên tổng box được review | Kiểm tra box ôm phần mặt biển nhìn thấy, không lấy cột/tấm nền/biển phụ |
| Attribute completeness | Số object không còn select `__undefined__` trên tổng object đã label | Ngăn thiếu `ego_relevance`/`visibility` khi export |
| Critical defect escape rate | Số lỗi Critical phát hiện sau bước review hoặc trong blind trên tổng số cơ hội Critical | Bắt lỗi an toàn cao lọt qua QA; edge-case library đã xác định các case critical cần theo dõi |

**Metric high-risk:** critical defect escape rate và inclusion/exclusion agreement trên các mẫu critical. Đếm theo
decision, không gộp theo ảnh; một critical miss bất kỳ làm batch không PASS.

## Quality gate

Threshold là đề xuất cho bài lab nhỏ, không phải chuẩn ngành hay deployment gate.

```text
PASS if:
  - Tất cả sample QA theo risk-stratified sampling đã được review.
  - Critical defect escape rate = 0; không có critical issue chưa đóng.
  - Macro-class accuracy >= 0.95; geometry pass rate (IoU >= 0.50) >= 0.90.
  - Attribute completeness = 1.00; `needs_review` compliance >= 0.95.
  - Mọi issue có severity, diagnosis, action, evidence và trạng thái đóng.
  - Guideline gap đã được cập nhật và ghi version/evidence trong revision log.

REWORK if:
  - Có Major defect chưa đóng; hoặc
  - Một metric dưới threshold nhưng không có Critical defect chưa xử lý; hoặc
  - Thiếu sampling/review record.

REJECT / ESCALATE if:
  - Có Critical defect chưa có quyết định xử lý; hoặc
  - Có ambiguity ảnh hưởng macro/ego relevance mà không có đường escalation; hoặc
  - Gold/reviewer/evidence không truy xuất được.
```

**Các thông tin cần điền trước khi chạy gate:** tên thật của reviewer và seed lấy mẫu. Tỷ lệ, ngưỡng và nơi ghi issue là
đề xuất của nhóm, cần được thống nhất trong buổi calibration trước khi áp dụng.

**Trade-off:** kiểm toàn bộ critical và object bị đánh dấu tốn reviewer time, nhưng giảm nguy cơ để lỗi rủi ro cao lọt
qua; 20% random sample giữ chi phí vừa phải cho các object còn lại. Với dataset 28 ảnh, đây là screening cho lớp học,
không phải ước lượng thống kê độ tin cậy sản xuất.
