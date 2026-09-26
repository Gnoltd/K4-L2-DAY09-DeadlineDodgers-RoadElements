# QA plan + quality gates

Tài liệu này chỉ ghi những gì đã có bằng chứng trong repo. Các mục chưa có thông tin thực tế được đánh dấu
`CHƯA CHỐT`, không tự suy đoán người phụ trách, cỡ mẫu hoặc threshold.

## Flow

Guideline → Calibration → Production → Self-QC → Review → Rework → Quality Gate.

- **Ai review, review bao nhiêu:** `CHƯA CHỐT`. Cần ghi tên/role reviewer và số lượng ảnh hoặc tỷ lệ ảnh được review.
- **Chọn sample theo rule nào:** lấy mẫu phải bao phủ các nhóm rủi ro đã có trong guideline/edge-case library: critical,
  small-far, occlusion/truncation, ambiguity/escalation, supplementary, và ranh giới LABEL/IGNORE. Tỷ lệ hoặc số lượng
  cụ thể: `CHƯA CHỐT`.
- **Issue được ghi ở đâu, đóng thế nào:** `CHƯA CHỐT` nơi lưu issue và người đóng issue. Mỗi issue tối thiểu cần có
  `sample_id`, mô tả decision/geometry sai, severity, nguyên nhân (`guideline gap` / `data ambiguity` /
  `execution error`), action, người xử lý và trạng thái đóng.
- **Khi phát hiện guideline gap thì update và version ra sao:** ghi bằng chứng vào `08_revision_log.md`; guideline v1 là
  bản nháp đầu, v2 là sau calibration nội bộ, v3 là sau blind handoff. Mỗi lần đổi `Version` phải có dòng revision
  kèm lý do và bằng chứng.

## Defect severity

Severity phản ánh hậu quả đối với downstream contract. Mapping dưới đây là cách phân loại theo các rule hiện có; nếu
downstream contract được chốt khác thì phải cập nhật trước khi QA.

| Severity | Định nghĩa cho project này | Ví dụ | Action mặc định |
|---|---|---|---|
| Critical | Bỏ sót hoặc gán sai một quyết định có rủi ro an toàn cao, đặc biệt biển quy định quyền ưu tiên; hoặc lỗi làm mất một decision critical trong gold/blind | Không vẽ hoặc gán sai biển STOP/nhường đường trong các case critical như EC01/EC03 | Dừng gate; ghi issue, xác định nguyên nhân, rework và kiểm tra lại toàn bộ mẫu cùng loại |
| Major | Sai inclusion/exclusion, `sign_group`, `needs_review` hoặc geometry theo cách làm thay đổi kết quả chấm/đầu ra | Vẽ biển chỉ hướng; bỏ tấm `supplementary`; nhầm `danger` với `priority`; box không theo rule | Rework mẫu lỗi và rà soát các mẫu cùng rule; chưa PASS nếu còn lỗi major chưa đóng |
| Minor | Lỗi không làm thay đổi decision chính nhưng vẫn vi phạm quy ước annotation hoặc làm giảm khả năng kiểm tra | Sai thao tác/ghi chú hoặc sai nhỏ không ảnh hưởng nhóm; ví dụ cụ thể và ngưỡng: `CHƯA CHỐT` | Sửa trong vòng QC; theo dõi để xem có lặp lại thành lỗi major không |
| Question | Điểm chưa đủ bằng chứng để phân loại là lỗi annotation; cần xác minh rule, dữ liệu hoặc downstream contract | Không rõ vật là biển thật hay vật giống biển; guideline chưa nói tới và cần escalation | Ghi lại, chưa tự sửa gold; owner quyết định là guideline gap, data ambiguity hoặc execution error |

## Metrics

Tính riêng trên sample QA đã được chọn và ghi rõ mẫu số. Không gộp ảnh không có biển với object-level metric nếu cách tính
chưa được nhóm chốt.

| Metric | Cách tính | Vì sao phù hợp với bài toán |
|---|---|---|
| Inclusion / exclusion agreement | Số decision đúng về có vẽ/không vẽ trên tổng decision được review | Bắt lỗi bỏ sót biển, vẽ biển chỉ hướng, mặt sau, quảng cáo và vật giống biển |
| `sign_group` accuracy | Số object có `sign_group` đúng trên tổng object phải gán nhóm | Đây là output phân loại chính của guideline |
| `needs_review` compliance | Số object tuân thủ đúng điều kiện bật/tắt `needs_review` trên tổng object được review | Kiểm tra các rule UNKNOWN/ESCALATE và tránh bật tuỳ tiện |
| Geometry pass rate | Số box đạt rule Rectangle và tolerance đã ghi trong guideline trên tổng box được review | Kiểm tra box ôm phần mặt biển nhìn thấy, không lấy cột/tấm nền/biển phụ |
| Supplementary separation rate | Số trường hợp biển chính và biển phụ được tách đúng trên tổng trường hợp có biển phụ | Biển phụ là một annotation riêng và có ý nghĩa downstream |
| Critical defect escape rate | Số lỗi Critical phát hiện sau bước review hoặc trong blind trên tổng số cơ hội Critical | Bắt lỗi an toàn cao lọt qua QA; edge-case library đã xác định các case critical cần theo dõi |

**Metric high-risk:** critical defect escape rate và inclusion/exclusion agreement trên các mẫu critical. Cách tính đã xác
định ở trên; threshold số cụ thể: `CHƯA CHỐT`.

## Quality gate

Threshold là đề xuất của nhóm, không phải chuẩn ngành. Repo chưa có dữ liệu để chốt các con số dưới đây; không tự điền
threshold khi chưa có reviewer, cỡ mẫu và downstream contract.

```text
PASS if:
  - Tất cả sample QA đã được review theo sampling rule đã chốt.
  - Không còn Critical defect chưa đóng.
  - Các metric đã tính đủ và đạt threshold đã được nhóm chốt.
  - Các issue còn lại có severity, nguyên nhân, action và trạng thái đóng.
  - Nếu có guideline gap, đã cập nhật guideline + 08_revision_log.md đúng version/evidence.

REWORK if:
  - Có Major defect chưa đóng; hoặc
  - Một metric dưới threshold; hoặc
  - Sampling/review record thiếu khiến kết quả chưa chứng minh được chất lượng.

REJECT / ESCALATE if:
  - Có Critical defect chưa có quyết định xử lý; hoặc
  - Có ambiguity mà guideline và escalation path chưa đủ để quyết định; hoặc
  - Không xác định được gold/reviewer/evidence để kiểm tra lại.
```

**Các thông tin bắt buộc phải chốt trước khi chạy gate:** reviewer, cỡ/tỷ lệ sample, nơi quản lý issue, threshold từng
metric, downstream contract và escalation owner. Hiện các thông tin này chưa có trong repo.

**Trade-off:** threshold càng chặt và sample càng rộng thì chi phí review/rework tăng, nhưng giảm nguy cơ bỏ sót lỗi
Critical và lỗi guideline. Chưa thể định lượng trade-off cho project này vì chưa có cỡ mẫu, reviewer và downstream contract
được chốt.
