# Problem statement + downstream contract

Tối đa nửa trang, viết **trước khi mở CVAT**. Đây là bằng chứng của gate G1 (topic lock). Thay mọi placeholder
mới là xong.

## Bài toán

Gắn nhãn biển báo giao thông theo nhóm chức năng (cấm, hiệu lệnh, nguy hiểm, ưu tiên, thông tin, biển phụ), chọn
`unknown` khi biển quá nhỏ, bị che hoặc mờ để xác định nhóm.

## Downstream contract

1. **Downstream task / model / user là ai?** Mô-đun phát hiện và phân nhóm biển báo của hệ thống ADAS / xe tự lái
   (detector huấn luyện trên ảnh camera trước). Kết quả nhóm biển được tầng lập kế hoạch dùng để quyết định dừng,
   nhường, giảm tốc, tuân thủ lệnh cấm/bắt buộc. Guideline dùng cho ảnh Đức (GTSDB) trong lớp và ảnh Việt Nam
   (QCVN 41:2024/BGTVT) ở dự án thật.
2. **Output annotation nào thực sự cần?** Một Rectangle `traffic_sign` ôm sát mỗi mặt biển; attribute `sign_group`
   (`prohibitory` / `mandatory` / `danger` / `priority` / `information` / `supplementary` / `unknown`) và checkbox
   `needs_review`; attribute `ego_relevant` (`relevant` / `not_relevant` / `uncertain`) cho biết biển có áp dụng cho
   làn ego đang chạy không, và `visibility` (`clear` / `partial` / `poor`) cho biết mức nhìn thấy mặt biển. Không cần
   đọc nội dung cụ thể (số tốc độ).
3. **Failure nào gây hậu quả lớn nhất?** Bỏ sót hoặc phân sai nhóm biển `priority` (dừng, nhường đường) và
   `prohibitory` (cấm vào, giới hạn tốc độ): xe có thể không dừng/nhường tại nút giao hoặc đi vào đường cấm. Đây là
   các decision `critical` trong gold. Cùng mức nghiêm trọng: ghi một biển `priority`/`prohibitory` đang áp dụng cho
   làn ego thành `ego_relevant = not_relevant` (hệ thống sẽ bỏ qua biển đó).
4. **Khi ambiguity không resolve được, ai / ở đâu là escalation path?** Annotator vẽ khung và bật `needs_review`
   (chọn nhóm nghiêng tới nhất hoặc `unknown`; không rõ làn thì `ego_relevant = uncertain`). Reviewer của nhóm (QA
   owner) lọc các khung `needs_review = true` trong export, quyết định cuối và ghi thành rule mới trong guideline nếu
   lặp lại.

## Scope

- **Trong scope (bắt buộc label):** mọi biển báo giao thông thật nhìn thấy được mặt biển (cố định, tạm công trường,
  điện tử), kể cả biển nhỏ ở xa, bị che một phần, bị cắt mép ảnh, ở đường nhánh; biển phụ vẽ khung riêng.
- **Ngoài scope (ignore):** biển chỉ hướng/địa danh (tên nơi, số đường, khoảng cách), mặt sau biển, đèn giao thông,
  biển quảng cáo/cửa hàng/tên phố, hình biển in trên xe hoặc quảng cáo, cọc tiêu, rào chắn, chữ sơn mặt đường, vật
  chỉ là một chấm màu không nhận ra là tấm biển, biển điện tử tắt.
- **Geometry tolerance:** khung ôm sát mép ngoài mặt biển nhìn thấy, không lấy cột và biển phụ; lệch ≤ 3 px mỗi cạnh
  ở ảnh gốc, biển nhỏ (cạnh ngắn dưới khoảng 20 px) lệch ≤ 2 px.

## Output chấm được

Blind test có cả bốn loại decision, đều nhìn thấy trong export CVAT for images 1.1:

- **LABEL:** có khung `traffic_sign`, `sign_group` là một trong sáu nhóm, `needs_review` tắt; chấm số khung và nhóm.
- **IGNORE:** không có khung cho vật ngoài scope (ví dụ biển chỉ hướng, mặt sau biển); chấm bằng số khung.
- **UNKNOWN:** khung có `sign_group = unknown` và `needs_review` bật.
- **ESCALATE:** khung có `needs_review` bật (kể cả `ego_relevant = uncertain`).
- **Ego relevance / visibility:** giá trị `ego_relevant` và `visibility` của từng khung trong export.
- **Geometry:** ít nhất một decision `geometry:` về khung ôm sát mặt biển, không lấy cột/biển phụ.

## Dữ liệu và giới hạn

- Nguồn: `data/gtsdb` (28 ảnh, 1360×800, biển Đức). Dùng 14 ảnh theo `sample_pack.csv`: 3 example, 6 calibration,
  5 blind.
- Giới hạn: chỉ có biển Đức, không có ảnh Việt Nam, nên các case riêng của VN (nền vàng, bảng ghép, biển chia làn trên
  giá long môn) chỉ có rule, chưa kiểm được trên ảnh. GTSDB ban ngày là chính, ít ảnh đêm/mưa. Ảnh GTSDB gắn
  "không có biển" vẫn có thể chứa biển nhỏ theo scope của nhóm (ví dụ GTS07), nên mọi decision "không có biển" phải
  kiểm ở cỡ gốc. `ego_relevant` chỉ suy ra từ một ảnh tĩnh, không biết ego sẽ rẽ hay đi thẳng.
- Trạng thái: guideline v1, CVAT schema đã dựng. Chưa calibration; các ngưỡng (chấm màu, che 1/4 và một nửa) và luật
  `ego_relevant` sẽ kiểm lại ở calibration trước khi lên v2.
