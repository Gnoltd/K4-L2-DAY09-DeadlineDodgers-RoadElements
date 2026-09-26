# Ontology + CVAT setup

Bảng ontology là **source of truth** cho schema CVAT: `03_cvat_labels.json` phải khớp từng dòng ở đây.

## Ontology table

| Name | Geometry | Type (class / attribute) | Allowed values | Default | Mutable? | Rationale |
|---|---|---|---|---|---|---|
| `traffic_sign` | Rectangle ôm sát mép ngoài mặt biển nhìn thấy (không lấy cột, tấm nền, biển phụ) | Class | — | — | No | Mọi mặt biển cùng một kiểu hình học và cùng rule vẽ; khác biệt nằm ở ý nghĩa nên đưa vào attribute |
| `sign_group` | Attribute của `traffic_sign` | Attribute (select) | `__undefined__`, `prohibitory`, `mandatory`, `danger`, `priority`, `information`, `supplementary`, `unknown` | `__undefined__` | No | Output phân loại chính (guideline mục 4.1, 4.2); không đặt mặc định vì nhóm sai là lỗi nặng nhất, còn `__undefined__` trong export là chưa làm xong |
| `ego_relevant` | Attribute của `traffic_sign` | Attribute (select) | `relevant`, `not_relevant`, `uncertain` | `relevant` | No | Biển có áp dụng cho làn ego không (mục 4.3); mặc định là trường hợp gặp nhiều nhất để giảm thao tác |
| `visibility` | Attribute của `traffic_sign` | Attribute (select) | `clear`, `partial`, `poor` | `clear` | No | Mức nhìn thấy mặt biển (mục 4.4); mặc định là trường hợp gặp nhiều nhất |
| `needs_review` | Attribute của `traffic_sign` | Attribute (checkbox) | bật / tắt | tắt (`false`) | No | Cờ ESCALATE cho một khung, bật theo 6 điều kiện ở mục 7 |

IGNORE không có label riêng: vật ngoài scope (mục 5) thì **không vẽ khung**. Không dùng tag cho cả ảnh vì guideline
không có quyết định nào ở mức cả ảnh.

## Class hay attribute

- **Một class `traffic_sign`, nhóm là attribute `sign_group`:** các nhóm biển giống nhau về hình học và rule vẽ khung;
  chỉ khác nghĩa. Để nhóm ở attribute giúp đổi nhóm không phải vẽ lại khung, và `make calib` so được riêng số khung với
  giá trị nhóm.
- **`ego_relevant`, `visibility`** là thuộc tính độc lập của cùng một mặt biển; tách thành class sẽ nổ ra
  7 × 3 × 3 tổ hợp.
- **Default gây bias:** `ego_relevant = relevant` và `visibility = clear` là giá trị gặp nhiều nhất nhưng nếu người vẽ
  quên đổi, biển đường nhánh sẽ bị ghi `relevant`, biển mờ bị ghi `clear`. Kiểm soát bằng rule QA: khung
  `sign_group = unknown` mà `visibility = clear` là lỗi; theo dõi ở calibration, nếu quên đổi nhiều thì chuyển default
  về `__undefined__`. `sign_group` giữ `__undefined__` để không có nhóm "im lặng".
- `mutable = false` cho mọi attribute vì task là ảnh tĩnh, không có track.

## CVAT

- **Phiên bản CVAT** (`make cvat-status`): CVAT 2.75.1 đang chạy tại http://localhost:8080.
- **Tên task calibration:** `DeadlineDodgers-calib-v1-<annotator>`, mỗi người một task trên máy mình.
- **Guide của task đã dán `02_guideline.md`?** Có với task cũ; task tạo lại theo schema mới phải dán lại bản v1 hiện
  tại (có ảnh ở `guideline_assets/`, tải ảnh lên Guide của CVAT nếu muốn xem trong task).
- **Nhóm dùng Track hay Shape, vì sao:** Shape rectangle; mọi sample là ảnh tĩnh, không dùng Track.
- **Schema:** 1 rectangle label `traffic_sign` với 4 attribute như bảng trên; `03_cvat_labels.json` parse hợp lệ bằng
  `python -m json.tool`.

## Setup test

**Trạng thái:** schema đã đổi từ 5 class (`prohibitory`, `mandatory`, `danger`, `other`, `unknown_sign`) + tag
`image_escalate` sang 1 class `traffic_sign` + 4 attribute cho khớp guideline v1. Setup test cũ không còn giá trị;
cần một thành viên chưa dựng task mở task mới và trả lời: label gì, tool nào, gán 4 attribute nào, khi nào bật
`needs_review`. Ghi người test và chỗ vấp vào đây trước khi bắt đầu calibration.
