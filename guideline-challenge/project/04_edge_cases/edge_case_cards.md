# Edge-case library

Tối thiểu **8 card**, khuyến nghị 10–12. Một edge case tốt là case mà hai annotator hợp lý có thể làm khác nhau nếu
guideline chưa rõ. Tám ảnh dễ có label rõ ràng không được tính là edge-case library.

Cần có đủ độ đa dạng: occlusion / truncation / small-far · ambiguous semantics · conflicting road elements · **một case
critical-risk** · **một case guideline cho phép escalation**.

File này là kho nội bộ của nhóm, **không gửi cho peer**. Card dùng ảnh example/calibration thì chép rule + ví dụ sang
`02_guideline.md` (mục 7 và 9) để peer đọc được. Card về ảnh blind chỉ nằm ở đây, và decision của nó phải có trong
`gold_decisions.csv` trước `make freeze`.

`make status` đếm số dòng `CASE ID:` đã điền (đã thay placeholder). Copy khối dưới cho mỗi case.

> Ghi chú nhóm: các card dưới đây viết theo guideline v1 và **chưa chốt split**. Trước khi đưa ảnh nào vào blind, mở
> cỡ gốc và kiểm lại Observation. Card nào dùng ảnh blind thì decision phải chép sang `gold_decisions.csv`.

---

CASE ID: EC01
Sample: GTS23
Scene: Đường rừng đi tới ngã ba, hai biển STOP hai bên, biển tròn xanh mũi tên trên đảo giữa, tấm chỉ đường nhỏ ở xa
Observation: 2 bát giác đỏ STOP; 1 tròn xanh mũi tên chéo; 2 tấm chỉ đường xanh/vàng nhỏ phía xa; một vật tròn tối nhỏ (nghi mặt sau biển)
Decision: LABEL (STOP, mũi tên) · IGNORE (chỉ đường, mặt sau biển)
Expected: 2 × `priority`, 1 × `mandatory`; không có khung cho tấm chỉ đường
Rationale: bỏ sót STOP làm xe không dừng tại nút giao — hậu quả lớn nhất của downstream contract
Common mistake: chọn STOP là `prohibitory` vì màu đỏ; vẽ luôn tấm chỉ đường
Diversity: critical

---

CASE ID: EC02
Sample: GTS06
Scene: Phố có xe buýt, biển tốc độ trên cột kèm hai tấm phụ
Observation: tròn viền đỏ "30"; tấm phụ mũi tên phạm vi + khoảng cách; tấm phụ giờ áp dụng
Decision: LABEL
Expected: `prohibitory` + 2 × `supplementary`, ba khung riêng, khung biển chính không lấy tấm phụ
Rationale: tấm phụ đổi ý nghĩa biển chính (chỉ áp dụng giờ nhất định); downstream cần biết có điều kiện đi kèm
Common mistake: một khung ôm cả biển và tấm phụ; bỏ qua tấm phụ
Diversity: conflict (biển chính + biển phụ)

---

CASE ID: EC03
Sample: GTS02
Scene: Ngã tư dưới cầu, hai cột mỗi cột tam giác ngược trên tròn xanh mũi tên, nhiều đèn giao thông
Observation: 2 tam giác đỉnh xuống; 2 tròn xanh mũi tên rẽ; mặt sau một biển tròn ở mép trái; 5+ đèn giao thông
Decision: LABEL (4 biển) · IGNORE (mặt sau biển, đèn)
Expected: 2 × `priority`, 2 × `mandatory`
Rationale: tam giác ngược (nhường đường) quy định quyền ưu tiên; nhầm thành `danger` làm mô hình không biết phải nhường
Common mistake: gọi tam giác ngược là `danger`; vẽ mặt sau biển
Diversity: ambiguous semantics · critical

---

CASE ID: EC04
Sample: GTS26
Scene: Đường quê hai bên cây, trông như không có biển
Observation: một tam giác rất nhỏ ở xa giữa ảnh, chỉ thấy khi xem cỡ gốc
Decision: LABEL hoặc UNKNOWN tuỳ độ rõ
Expected: 1 khung; `danger` nếu thấy rõ tam giác đỉnh lên, không thì `unknown` + `needs_review`
Rationale: ảnh "không có biển" là chỗ gold hay sai nhất; peer vẽ đúng vẫn bị chấm sai nếu gold để trống
Common mistake: để trống ảnh
Diversity: small_far

---

CASE ID: EC05
Sample: GTS09
Scene: Vòng xuyến chạng vạng, cột có 6 tấm chỉ đường xếp chồng, hai biển người đi bộ vuông xanh, biển tròn xanh thấp, biển bị cắt ở mép phải
Observation: 6 tấm chỉ đường (xanh, vàng, trắng) có tên nơi; 2 vuông xanh người đi bộ; 1–2 tròn xanh mũi tên; tấm vuông xanh và tấm phụ bị cắt ở mép phải
Decision: IGNORE (chỉ đường) · LABEL (người đi bộ, mũi tên) · ESCALATE (biển bị cắt mép)
Expected: 2 × `information`, `mandatory` cho mỗi tròn xanh; biển mép phải: nhóm theo phần thấy hoặc `unknown`, `needs_review` bật
Rationale: kiểm ranh giới `information` và biển chỉ hướng; ảnh ánh sáng yếu
Common mistake: vẽ 6 tấm chỉ đường; gọi người đi bộ vuông xanh là `danger`
Diversity: ambiguity · low_visibility · truncation

---

CASE ID: EC06
Sample: GTS24
Scene: Phố có đường ray, trời mưa, đèn giao thông mũi tên, biển nhỏ ở phía bên kia ngã tư
Observation: tròn xanh mũi tên thẳng; vuông xanh ký hiệu trắng dưới nó; phía trái xa: biển P xanh, tròn nền xanh viền đỏ (cấm dừng/đỗ), tròn đỏ nhỏ (nghi cấm vào) ở cỡ khoảng 10–15 px; chữ thập xanh hiệu thuốc
Decision: LABEL (biển gần) · UNKNOWN/ESCALATE (biển nhỏ xa) · IGNORE (hiệu thuốc, đèn)
Expected: `mandatory`, `information`; biển xa: vẽ nếu nhận ra là tấm biển, nhóm nếu rõ, không thì `unknown` + `needs_review`
Rationale: biển đường nhánh vẫn vẽ vì scope không xét làn; kiểm ngưỡng "chấm màu" và luật viền đỏ thắng nền xanh
Common mistake: bỏ biển đường nhánh; gọi cấm dừng/đỗ là `mandatory`; vẽ chữ thập hiệu thuốc
Diversity: small_far · escalation · conflict

---

CASE ID: EC07
Sample: GTS18
Scene: Đường hẹp qua cầu, một cột có tam giác cảnh báo trên, tròn "30" dưới; tấm trắng trên hàng rào bên phải
Observation: tam giác đỉnh lên (đường cong kép); tròn viền đỏ "30"; tấm thông báo trắng trên hàng rào (không phải biển giao thông)
Decision: LABEL · IGNORE (tấm trên hàng rào)
Expected: `danger` + `prohibitory`, hai khung riêng
Rationale: hai biển khác nhóm trên cùng cột phải tách; tấm tư nhân không có hiệu lực giao thông
Common mistake: một khung ôm cả hai biển; vẽ tấm trên hàng rào
Diversity: conflict

---

CASE ID: EC08
Sample: GTS07
Scene: Cao tốc dưới cầu đang thi công, đường ướt, vạch vàng tạm
Observation: một tròn đỏ rất nhỏ (khoảng 8 px) ở giữa ảnh phía xa; hai tấm vuông nhỏ ở bên kia dải phân cách; rào chắn sọc đỏ trắng
Decision: IGNORE (tròn đỏ quá nhỏ, rào chắn) · ESCALATE (hai tấm vuông nếu nhận ra là biển)
Expected: không có khung cho tròn đỏ 8 px; hai tấm vuông: `unknown` + `needs_review` nếu nhận ra là tấm biển
Rationale: GTSDB coi ảnh này là không có biển, nhưng theo scope của mình vẫn có vật cần quyết định; kiểm ranh giới "chấm màu"
Common mistake: vẽ rào chắn sọc; hai người lệch nhau ở tròn đỏ 8 px
Diversity: small_far · ambiguity · escalation

---

CASE ID: EC09
Sample: GTS21
Scene: Ngã ba phố, ba tấm chỉ đường, biển người đi bộ vuông xanh, tròn xanh mũi tên chéo, cọc sọc xanh trắng, biển quảng cáo bia
Observation: như Scene
Decision: LABEL (người đi bộ, mũi tên) · IGNORE (chỉ đường, cọc sọc, quảng cáo)
Expected: `information` + `mandatory`
Rationale: ví dụ chuẩn cho ranh giới information / chỉ hướng / vật giống biển
Common mistake: vẽ cọc sọc như biển; vẽ quảng cáo
Diversity: ambiguous semantics

---

CASE ID: EC10
Sample: GTS27
Scene: Đường quê ngược sáng, tam giác bông tuyết trên cột điện
Observation: tam giác đỉnh lên, nền tối do ngược sáng; cọc tiêu trắng
Decision: LABEL
Expected: `danger`; cọc tiêu không vẽ
Rationale: ngược sáng làm mất màu nền; luật "hình trước, màu sau" vẫn cho ra `danger`
Common mistake: chọn `unknown` chỉ vì tối
Diversity: low_visibility

---

## Case dự phòng cho ảnh Việt Nam (chưa có ảnh trong `data/`)

Chưa tính là card vì không có sample. Rule tương ứng **đã có** trong guideline v1; khi dự án có ảnh VN, chọn ảnh
thật cho từng case rồi chuyển thành card.

| Case | Rủi ro | Cách xử lý trong v1 | Chỗ trong guideline |
|---|---|---|---|
| Tam giác nguy hiểm **nền vàng** (VN) so với nền trắng (Đức) | Người quen biển Đức chọn `unknown` | Hình trước màu sau: tam giác đỉnh lên viền đỏ là `danger` | 4.1, 4.2 dòng 5 |
| **Bảng ghép** nhiều hình biển trên một tấm nền, kèm chữ giờ | Vẽ một khung cho cả bảng | Mỗi hình biển một khung, vùng chữ một khung `supplementary`, không vẽ tấm nền | 2 |
| **Biển phụ "trừ xe buýt", giờ, loại xe** (S) | Bỏ qua, dù nó đổi ý nghĩa | `supplementary`, khung riêng | 2, 4.1 |
| **Biển phân làn trên giá long môn** | Tách từng ô, hoặc gọi là chỉ hướng | Một khung cho cả tấm; có ký hiệu bắt buộc từng làn → `mandatory` | 2, 4.1 |
| **Biển chỉ hướng trên cao tốc** (xanh lá/xanh dương, tên nơi) | Vẽ vì to và rõ | Không vẽ | 4.2 dòng 7, 5 |
| **Biển tạm công trường**, biển trên xe công trình | Bỏ qua vì "không phải biển cố định" | Vẽ bình thường theo nhóm | 1, 5 |
| **Biển điện tử** tốc độ / thông tin | Không biết chọn nhóm | Theo nội dung đang hiển thị; tắt → `unknown` + `needs_review` | 6 |
| **Biển bị cây, dây điện, bảng quảng cáo che** (rất phổ biến ở VN) | Hai người khác nhau ở ngưỡng che | Che từ khoảng một nửa → `needs_review`; mất hình → `unknown` | 6, 7 |
| **Biển phai màu, bị dán quảng cáo đè, bị bẻ cong** | Chọn `unknown` quá nhiều hoặc đoán | Hình còn rõ thì chọn nhóm + `needs_review` | 6, 7 |
| **Hình biển trên thân xe, quảng cáo, áp phích** | Vẽ như biển thật | Không vẽ | 5 |
| **Biển cấm đi ngược chiều** | GTSDB xếp vào "other", QCVN xếp vào cấm | `prohibitory` (theo chức năng) | 4.1, 4.2 dòng 3 |
| **Dừng lại (R.122)** thuộc nhóm hiệu lệnh trong QCVN | Chọn `mandatory` theo quy chuẩn | `priority` (theo chức năng, bát giác) | 4.2 dòng 2 |

Mã QCVN trong bảng là tham khảo; đối chiếu lại với QCVN 41:2019/BGTVT trước khi dùng cho dự án thật.

## Hướng xử lý cho v2 và v3

**Chỗ dự đoán sẽ lệch ở calibration (v2)** — theo dõi các dòng này trong `06_calibration_measure.csv`:

| Chỗ dễ lệch | Dấu hiệu trong `make calib` | Hướng sửa nếu lệch |
|---|---|---|
| Ngưỡng "chấm màu" vs "tấm biển" (EC06, EC08) | Số khung khác nhau ở ảnh có biển xa | Thêm ảnh crop mẫu vào mục 9: một ví dụ vẽ, một ví dụ không vẽ; hoặc chốt bằng kích thước đo được trong CVAT nếu B xác nhận CVAT hiện kích thước khung |
| `information` vs chỉ hướng (EC05, EC09) | Số khung lệch ở GTS09/GTS21 | Liệt kê đóng các ký hiệu `information`; mọi tấm vuông/chữ nhật khác không vẽ |
| `needs_review` bật tuỳ tiện | Cột `needs_review` lệch dù `sign_group` khớp | Giữ đúng 5 điều kiện ở mục 7; bỏ điều kiện nào gây lệch mà không giúp downstream |
| Biển bị che "khoảng một nửa" | `needs_review` lệch ở biển bị che | Đổi sang mốc dễ nhìn hơn (ví dụ "không thấy tâm biển") |
| `supplementary` bị bỏ sót | Số khung lệch 1–2 ở ảnh có tấm phụ | Nhấn mạnh trong Common mistakes, thêm ví dụ GTS06 lên đầu |

**Hướng cho v3 (sau blind):**

- Mỗi câu peer hỏi trong `clarification_log.csv` → một dòng rule hoặc một ví dụ mới trong guideline, không trả lời miệng.
- Nếu peer sai nhóm ở cùng một loại biển nhiều lần → thêm dòng vào thứ tự quyết định 4.2 chứ không chỉ thêm ví dụ.
- Nếu peer bỏ sót biển nhỏ → thêm bước "quét ảnh" (trái → phải, gần → xa) vào đầu mục 5.
- Ứng viên attribute mới, chỉ thêm khi có bằng chứng downstream cần: `temporary` (biển tạm công trường ghi đè biển
  cố định), `applies_to_ego` (biển có áp dụng cho làn xe mình). Thêm attribute làm tăng bất đồng, nên cân nhắc kỹ.
