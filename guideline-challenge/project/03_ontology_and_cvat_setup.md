# Ontology + CVAT setup

Bảng ontology là **source of truth** cho schema CVAT: `03_cvat_labels.json` phải khớp từng dòng ở đây.

## Ontology table

| Name | Geometry | Type (class / attribute) | Allowed values | Default | Mutable? | Rationale |
|---|---|---|---|---|---|---|
| `prohibitory` | Rectangle quanh mặt biển | Class | Macro class | N/A | No | Nhóm cấm/hạn chế theo đặc tả R&D |
| `mandatory` | Rectangle quanh mặt biển | Class | Macro class | N/A | No | Nhóm hiệu lệnh theo đặc tả R&D |
| `danger` | Rectangle quanh mặt biển | Class | Macro class | N/A | No | Nhóm cảnh báo nguy hiểm theo đặc tả R&D |
| `other` | Rectangle quanh mặt biển | Class | Biển xác nhận ngoài ba macro | N/A | No | Catch-all để không ép biển chỉ dẫn/phụ vào sai macro; loại khỏi tập train 3-way nếu downstream yêu cầu |
| `unknown_sign` | Rectangle quanh mặt biển | Class | Biển xác nhận nhưng chưa phân loại được | N/A | No | Biểu diễn UNKNOWN object-level; luôn bật `needs_review` |
| `ego_relevance` | Attribute của rectangle | Attribute | `relevant`, `irrelevant`, `unknown` | `__undefined__` | No | Đánh giá thị giác về hành lang ego; default buộc annotator chọn |
| `visibility` | Attribute của rectangle | Attribute | `readable`, `blurry`, `occluded`, `unknown` | `__undefined__` | No | Mô tả bằng chứng ảnh; default buộc annotator chọn |
| `needs_review` | Checkbox của rectangle | Attribute | `false` / bật checkbox | `false` | No | Escalate một object cần lead xem |
| `image_escalate` | Tag ảnh | Image tag | Có / không | Không có tag | N/A | Escalate khi không thể tạo object-level decision |

## Class hay attribute

Macro-group là class vì downstream detection cần nhóm đối tượng; `ego_relevance` và `visibility` là thuộc tính độc lập
của cùng một mặt biển, không tạo class tổ hợp. `needs_review` là cờ QA. `other` giữ biển đã xác nhận ngoài ba nhóm;
`unknown_sign` giữ object-level UNKNOWN, không phải macro thứ tư hay nhãn train mục tiêu. Hai select dùng
`__undefined__` để phát hiện quên chọn; không được export khi còn giá trị này.

Mapping nhóm GTSDB phải được kiểm theo class ID/dataset reference và đối chiếu với cách nhóm của R&D. STOP/YIELD được
xếp vào `prohibitory` theo đặc tả của bài lab; biển ưu tiên/biển chỉ dẫn không thuộc ba nhóm được giữ ở `other` để
review. Đây là quyết định operational cho bài lab, không phải ánh xạ pháp lý chính thức của Việt Nam.

## CVAT

- **Phiên bản CVAT** (`make cvat-status`): CVAT 2.75.1 đang chạy tại http://localhost:8080.
- **Tên task calibration:** `<team>-calib-v1-<annotator>` (ví dụ: `calib-v1-annotatorA`, `calib-v1-annotatorB`).
- **Guide của task đã dán `02_guideline.md`?** Có; dán toàn bộ `02_guideline.md` vào phần Guide của task trên CVAT để người vẽ tra cứu trực tiếp.
- **Nhóm dùng Track hay Shape, vì sao:** Shape rectangle; mọi sample là ảnh tĩnh, không dùng Track.
- **Schema:** Bốn rectangle labels (`prohibitory`, `mandatory`, `danger`, `other`) và một image tag (`image_escalate`); schema JSON đã parse hợp lệ bằng Python `json.tool` và khớp hoàn toàn bảng ontology.

## Setup test

**Trạng thái:** Đã kiểm tra setup schema trên CVAT 2.75.1. Khi task được mở, người vẽ truy cập tab Guide để nắm rõ: 4 macro/catch-all classes, vẽ shape rectangle ôm sát mặt biển, bắt buộc chọn hai attributes `ego_relevance` và `visibility` (mặc định `__undefined__`), và sử dụng checkbox `needs_review` hoặc tag `image_escalate` khi cần báo cáo nghi vấn.
