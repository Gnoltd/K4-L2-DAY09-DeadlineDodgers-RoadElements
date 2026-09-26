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

CASE ID: EC11
Sample: GTS02
Scene: Ngã tư dưới cầu, hai cột hai bên: mỗi cột một tam giác ngược trên một biển tròn xanh mũi tên (trái: rẽ trái; phải: rẽ phải)
Observation: ego đi sau xe trắng, không thấy rõ mũi tên sơn trên làn ego; hai biển mũi tên chỉ hai hướng khác nhau
Decision: ESCALATE (ego_relevant)
Expected: 2 × `priority` `relevant`; 2 × `mandatory` `ego_relevant = uncertain`, `needs_review` bật
Rationale: biển hướng đi bắt buộc có thể chỉ dành cho một làn (guideline 4.3 dòng 7); không có bằng chứng làn thì không được đoán `not_relevant`
Common mistake: đánh `not_relevant` cho biển cột trái chỉ vì nằm bên trái; hoặc để mặc định `relevant` cho cả hai biển mâu thuẫn nhau
Diversity: ambiguity · escalation · conflict

---

CASE ID: EC12
Sample: GTS24
Scene: Ngã tư phố có đường ray; biển nhỏ ở góc phố bên trái phía bên kia ngã tư
Observation: biển P xanh, cấm dừng/đỗ, tròn đỏ nhỏ đặt ở góc đường nhánh bên trái; biển tròn xanh mũi tên thẳng và vuông xanh bên phải trên đường ego
Decision: LABEL + ESCALATE
Expected: biển bên phải `relevant`, `clear`; biển góc trái: `visibility = poor`, `ego_relevant = not_relevant` nếu thấy rõ quay vào đường nhánh, không thì `uncertain` + `needs_review`
Rationale: biển của đường nhánh vẫn phải vẽ (detector cần) nhưng không được coi là lệnh cho ego (4.3 dòng 1)
Common mistake: để mặc định `relevant` và `clear` cho biển xa ở đường nhánh
Diversity: small_far · conflict

---

## Case dự phòng cho ảnh Việt Nam (chưa có ảnh trong `data/`)

Chưa tính là card vì không có sample. Rule tương ứng **đã có** trong guideline v1; khi dự án có ảnh VN, chọn ảnh
thật cho từng case rồi chuyển thành card.

| Case | Rủi ro | Cách xử lý trong v1 | Căn cứ QCVN 41:2024 | Chỗ trong guideline |
|---|---|---|---|---|
| Tam giác nguy hiểm **nền vàng** (VN) so với nền trắng (Đức) | Người quen biển Đức chọn `unknown` | Hình trước màu sau: tam giác đỉnh lên viền đỏ là `danger` | Điều 11.3, 29.1 | 4.1, 4.2 dòng 5 |
| **W.208** tam giác đỉnh xuống nằm trong nhóm W | Chọn `danger` theo chương quy chuẩn | `priority` | Điều 29.1 (ngoại lệ đỉnh hướng xuống) | 4.2 dòng 2 |
| **R.122 "Dừng lại"** nằm trong nhóm R | Chọn `mandatory` theo chương quy chuẩn | `priority` | Phụ lục D.1 ("biển hiệu lệnh dạng đặc biệt") | 4.2 dòng 2 |
| **I.401/I.402** đường ưu tiên nằm trong nhóm I | Chọn `information` | `priority` | Điều 36.1 | 4.2 dòng 2 |
| **P.132** nhường xe ngược chiều qua đường hẹp | Chọn `priority` vì có chữ "nhường" | `prohibitory` (tròn viền đỏ); I.406 chiều ngược lại là `information` | Điều 22.1, 36.1 | 4.2 dòng 3, 6 |
| **Biển hết hiệu lệnh** (nền xanh vạch chéo đỏ: R.307, R.404, R.412i–p, R.421) | Chọn `prohibitory` vì thấy vạch chéo | `mandatory` | Điều 33.1 | 4.2 dòng 4, mục 10.7 |
| **Bắt đầu/hết khu đông dân cư R.420/R.421** (nhóm R, dù trông giống biển chỉ dẫn) | Bỏ qua vì có tên nơi, hoặc chọn `information` | `mandatory` | Điều 32.1 | 4.1, 4.2 dòng 4 |
| **P.127b/c** tốc độ tối đa từng làn (tấm chữ nhật xanh chia làn) | Chọn `mandatory` vì nền xanh chữ nhật | Một khung cả tấm, `prohibitory` (chứa vòng tròn viền đỏ) | Phụ lục B.27 | 2, 4.2 dòng 3 |
| **Biển ghép** nhiều hình biển trên một tấm nền, kèm chữ giờ | Vẽ một khung cho cả tấm | Mỗi hình biển đơn một khung, vùng chữ một khung `supplementary` | Điều 18.4, 18.5 | 2 |
| **Biển phụ "TẠM THỜI"**, "trừ xe buýt", giờ, loại xe | Bỏ qua, dù nó đổi ý nghĩa | `supplementary`, khung riêng | Điều 14.3, 41 | 2, 4.1 |
| **S.507 "Hướng rẽ"** đặt độc lập ở đường cong | Không biết nhóm vì không có biển chính đi kèm | `supplementary` | Điều 41.1 | 4.2 dòng 1 |
| **Biển điện tử VMS** chỉ hiện chữ | Không biết chọn nhóm | Nhóm theo màu chữ: đỏ cấm, trắng/da cam hiệu lệnh, vàng cảnh báo, xanh lam chỉ dẫn; tắt thì không vẽ | Điều 14.2.5 | 6 |
| **Biển viết bằng chữ** đứng riêng | Chọn `unknown` vì không có hình | Nhóm theo màu nền: đỏ "Cấm…" `prohibitory`, đỏ khác `mandatory`, vàng `danger`, xanh `information` | Điều 42.2 | 4.2 dòng 8 |
| **Biển chỉ hướng** thường và trên cao tốc (I.414, I.415, I.419, IE) | Vẽ vì to và rõ | Không vẽ | Điều 36.1, chương 9 | 4.2 dòng 7, 5 |
| **Biển tạm công trường**, biển trên xe công trình | Bỏ qua vì "không phải biển cố định" | Vẽ bình thường theo nhóm | Điều 14.3 (biển tạm có hiệu lực cao hơn biển cố định) | 1, 5 |
| **Biển bị cây, dây điện, bảng quảng cáo che** (rất phổ biến ở VN) | Hai người khác nhau ở ngưỡng che | Che từ khoảng 1/4 → `partial`; từ một nửa → thêm `needs_review`; mất hình → `unknown` | — | 4.4, 6, 7 |
| **Biển phai màu, bị dán quảng cáo đè, bị bẻ cong** | Chọn `unknown` quá nhiều hoặc đoán | Hình còn rõ thì chọn nhóm, `poor` + `needs_review` | — | 6, 7 |
| **Hình biển trên thân xe, quảng cáo, áp phích** | Vẽ như biển thật | Không vẽ | — | 5 |
| **Giá long môn nhiều biển, mỗi biển trên một làn** (VN hay dùng cho biển cấm/hiệu lệnh trên đường nhiều làn) | Đánh `relevant` cho tất cả | Biển trên làn ego `relevant`, trên làn khác `not_relevant` | Điều 15.2, 17.4 | 4.3 dòng 3 |
| **Biển có S.504 "Làn đường"** | Bỏ qua biển phụ, đánh `relevant` | `not_relevant` nếu biển phụ không gồm làn ego | Điều 41.2.1 | 4.3 dòng 5 |
| **Ego làn trái, biển "hướng phải đi" rẽ phải đặt bên phải** cạnh làn rẽ phải có mũi tên sơn | Để mặc định `relevant` | `not_relevant` khi mũi tên sơn làn ego mâu thuẫn; không thấy mũi tên sơn → `uncertain` | Điều 15.2 | 4.3 dòng 7, 9 |
| **Biển đường gom / đường song song** sau dải phân cách cứng (đường đô thị VN nhiều làn) | Đánh `relevant` vì cùng chiều | `not_relevant` | — | 4.3 dòng 1 |
| **Biển cho làn xe máy riêng** (R.412d) khi ego là ô tô ở làn ô tô | Đánh `not_relevant` theo loại xe | Theo vị trí làn (4.3 dòng 3, 5, 6), không theo loại xe | Điều 15.2 | 4.3 |
| **Biển bên trái** cùng chiều trên đường một chiều hoặc dải phân cách giữa | Đánh `not_relevant` vì ở bên trái | `relevant` (biển được phép đặt bổ sung bên trái) | Điều 16.2 | 4.3, mục 10.14 |

Mã biển và số điều đối chiếu với QCVN 41:2024/BGTVT (bản kèm Thông tư ban hành 15/11/2024, file
`51-bgtvt-kem.pdf`).

## Hướng xử lý cho v2 và v3

**Chỗ dự đoán sẽ lệch ở calibration (v2)** — theo dõi các dòng này trong `06_calibration_measure.csv`:

| Chỗ dễ lệch | Dấu hiệu trong `make calib` | Hướng sửa nếu lệch |
|---|---|---|
| Ngưỡng "chấm màu" vs "tấm biển" (EC06, EC08) | Số khung khác nhau ở ảnh có biển xa | Thêm ảnh crop mẫu vào mục 9: một ví dụ vẽ, một ví dụ không vẽ; hoặc chốt bằng kích thước đo được trong CVAT nếu B xác nhận CVAT hiện kích thước khung |
| `information` vs chỉ hướng (EC05, EC09) | Số khung lệch ở GTS09/GTS21 | Liệt kê đóng các ký hiệu `information`; mọi tấm vuông/chữ nhật khác không vẽ |
| `needs_review` bật tuỳ tiện | Cột `needs_review` lệch dù `sign_group` khớp | Giữ đúng 5 điều kiện ở mục 7; bỏ điều kiện nào gây lệch mà không giúp downstream |
| Biển bị che "khoảng một nửa" | `needs_review` lệch ở biển bị che | Đổi sang mốc dễ nhìn hơn (ví dụ "không thấy tâm biển") |
| `supplementary` bị bỏ sót | Số khung lệch 1–2 ở ảnh có tấm phụ | Nhấn mạnh trong Common mistakes, thêm ví dụ GTS06 lên đầu |
| `ego_relevant` giữa `relevant` / `uncertain` (EC11, EC12) | Cột `ego_relevant` lệch ở biển đường nhánh, biển mũi tên | Thêm ảnh ví dụ cho từng dòng 1–7 của 4.3; nếu vẫn lệch nhiều, thu hẹp `not_relevant` về các dòng có bằng chứng hình học rõ (1, 3, 5, 6) |
| `visibility` giữa `clear` / `partial` / `poor` | Cột `visibility` lệch dù nhóm khớp | Chốt mốc 1/4 bằng ảnh ví dụ; nếu vẫn lệch, gộp còn hai mức `clear` / `degraded` |
| Người vẽ quên đổi mặc định | `relevant`/`clear` xuất hiện ở biển `unknown` hoặc biển đường nhánh | QA lọc tự động: `unknown` mà `clear` là lỗi; nếu tỉ lệ cao, đổi mặc định về `__undefined__` |

**Hướng cho v3 (sau blind):**

- Mỗi câu peer hỏi trong `clarification_log.csv` → một dòng rule hoặc một ví dụ mới trong guideline, không trả lời miệng.
- Nếu peer sai nhóm ở cùng một loại biển nhiều lần → thêm dòng vào thứ tự quyết định 4.2 chứ không chỉ thêm ví dụ.
- Nếu peer bỏ sót biển nhỏ → thêm bước "quét ảnh" (trái → phải, gần → xa) vào đầu mục 5.
- Ứng viên attribute mới, chỉ thêm khi có bằng chứng downstream cần: `temporary` (biển tạm ghi đè biển cố định theo
  QCVN 41:2024 Điều 14.3; biển VMS ghi đè biển tĩnh theo Điều 14.1). Thêm attribute làm tăng bất đồng, nên cân nhắc kỹ.
