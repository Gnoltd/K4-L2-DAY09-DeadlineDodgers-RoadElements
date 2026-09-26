# Annotation guideline — Gắn nhãn biển báo giao thông theo nhóm chức năng

**Version:** v1

<!--
v0 = chưa có bản nháp. Đổi dòng Version ở trên thành v1 khi xong bản nháp đầu, v2 sau calibration, v3 sau blind
handoff; mỗi lần tăng version ghi một dòng vào 08_revision_log.md. `make freeze` đòi v2 trở lên.

File này là thứ nhóm peer nhận nguyên văn trong blind pack và là Guide dán vào CVAT. Peer KHÔNG nhận
edge_case_cards.md, gold_decisions.csv hay sample_pack.csv. Rule nào peer cần biết phải nằm ở đây.
No hidden rules: rule chỉ giải thích bằng miệng thì coi như không tồn tại.
Ví dụ trong guideline chỉ dùng ảnh split example hoặc calibration, không dùng ảnh blind.
-->

## 1. Objective + scope

**Mục đích:** tạo dữ liệu huấn luyện cho mô-đun nhận biết biển báo của xe tự lái / ADAS: mỗi biển có **một khung** và
**một nhóm chức năng** (`sign_group`). Nhóm cho biết biển tác động thế nào tới việc lái: cấm, bắt buộc, cảnh báo, quy
định quyền ưu tiên, thông tin, hay bổ sung ý nghĩa cho biển khác. Bài này **không** đọc nội dung cụ thể (ví dụ 30
hay 50 km/h) và **không** xác định biển áp dụng cho làn nào.

Nhóm được định nghĩa theo **chức năng**, không theo chương của một quy chuẩn quốc gia, để cùng một guideline dùng được
cho ảnh Đức (dữ liệu lớp học, GTSDB) và ảnh Việt Nam (dự án thật, QCVN 41:2019/BGTVT). Bảng ở mục 4 cho biết cách nhận
dạng ở từng nước.

**Trong scope:** mọi **biển báo giao thông thật** (tấm biển cố định, biển tạm công trường, biển điện tử) mà **mặt biển
nhìn thấy được** từ camera, kể cả biển ở đường nhánh hay phía bên kia đường, miễn là thấy mặt biển.

**Ngoài scope (không vẽ):** xem danh sách đầy đủ ở mục 5.

## 2. Annotation unit

- Đơn vị là **ảnh tĩnh**. Không có track.
- **Một mặt biển = một khung.** Mặt biển là một hình biển hoàn chỉnh (một hình tròn, một tam giác, một bát giác, một
  hình thoi, một tấm chữ nhật).
- **Nhiều biển trên một cột:** mỗi mặt biển một khung, kể cả khi chúng sát nhau.
- **Biển phụ** (tấm nhỏ ngay dưới/trên biển chính) là **một khung riêng** với `sign_group = supplementary`. Không gộp
  vào khung biển chính.
- **Bảng ghép** (một tấm nền lớn chứa nhiều hình biển, hay gặp ở VN, ví dụ "cấm ô tô + cấm xe máy + chữ giờ"): mỗi
  hình biển bên trong là một khung; toàn bộ vùng chữ trên bảng là **một** khung `supplementary`. Không vẽ khung cho
  tấm nền.
- **Biển phân làn trên giá long môn** (một tấm chữ nhật chia ô theo từng làn): **một khung cho cả tấm**, nhóm theo mục 4.
- Hai biển giống hệt nhau ở hai bên đường là hai khung.

## 3. Geometry rule

- Dùng **Rectangle**, khung thẳng theo trục ảnh, **ôm sát mép ngoài mặt biển nhìn thấy** (kể cả viền).
- **Không lấy** cột, giá treo, khung sắt, bóng, tấm nền của bảng ghép.
- Biển tròn, tam giác, bát giác, thoi: khung là hình chữ nhật nhỏ nhất bao hết hình.
- Biển bị che hoặc bị cắt ở mép ảnh: khung ôm **phần nhìn thấy**, không suy đoán phần bị che.
- **Được zoom** trong CVAT để đặt mép khung cho chính xác.
- **Dung sai:** mỗi cạnh lệch không quá 3 px so với mép biển ở ảnh gốc; biển nhỏ (khung có cạnh ngắn dưới khoảng
  20 px) lệch không quá 2 px.

## 4. Taxonomy

Một label: `traffic_sign` (Rectangle). Phân loại nằm ở attribute. Bảng đầy đủ ở `03_ontology_and_cvat_setup.md`;
hai nơi phải khớp nhau.

| Attribute | Giá trị | Mặc định | Ghi chú |
|---|---|---|---|
| `sign_group` | `prohibitory` · `mandatory` · `danger` · `priority` · `information` · `supplementary` · `unknown` | `__undefined__` | Bắt buộc chọn; còn `__undefined__` trong export là chưa làm xong |
| `needs_review` | checkbox | tắt | Bật theo đúng các điều kiện ở mục 7 |

### 4.1 Nhóm và cách nhận dạng

| `sign_group` | Chức năng | Nhận dạng ở Đức | Nhận dạng ở VN (QCVN 41:2019) |
|---|---|---|---|
| `prohibitory` | Cấm một hành vi, hoặc **hết** một lệnh cấm | Tròn viền đỏ nền trắng (tốc độ tối đa, cấm vượt, cấm xe…); tròn nền xanh viền đỏ (cấm dừng/đỗ); tròn đỏ gạch ngang trắng (cấm vào); tròn trắng gạch chéo đen/xám (hết lệnh cấm) | Nhóm P và DP: tròn viền đỏ nền trắng; cấm đi ngược chiều (tròn đỏ gạch ngang trắng); cấm dừng/đỗ (nền xanh viền đỏ); hết lệnh cấm (tròn trắng gạch chéo đen) |
| `mandatory` | Bắt buộc làm theo | Tròn nền xanh dương, ký hiệu trắng (mũi tên, vòng xuyến, đường dành cho…) | Nhóm R dạng tròn nền xanh; biển phân làn chữ nhật xanh có ký hiệu bắt buộc cho từng làn |
| `danger` | Cảnh báo nguy hiểm phía trước | Tam giác **đỉnh hướng lên**, viền đỏ, **nền trắng** | Nhóm W: tam giác **đỉnh hướng lên**, viền đỏ, **nền vàng** |
| `priority` | Quy định quyền ưu tiên tại nút giao | Dừng (bát giác đỏ STOP); nhường đường (tam giác **đỉnh hướng xuống**); đường ưu tiên và hết đường ưu tiên (hình thoi vàng viền trắng) | Dừng lại (bát giác đỏ); giao nhau với đường ưu tiên (tam giác đỉnh hướng xuống); đường ưu tiên / hết đường ưu tiên (thoi vàng) |
| `information` | Thông tin **làm thay đổi cách lái** | Vuông/chữ nhật xanh có **ký hiệu**: người đi bộ sang đường, đường một chiều, đường cụt, nơi đỗ xe, khu dân cư, bắt đầu/hết cao tốc | Nhóm I dạng ký hiệu: đường người đi bộ sang ngang, đường một chiều, nơi đỗ xe, bắt đầu/hết khu đông dân cư, đường cao tốc |
| `supplementary` | Bổ sung ý nghĩa cho biển chính | Tấm chữ nhật trắng nhỏ dưới biển chính: khoảng cách, giờ, loại xe, mũi tên phạm vi | Nhóm S: phạm vi tác dụng, khoảng cách, thời gian, loại xe, "trừ …" |
| `unknown` | Chắc chắn là biển nhưng không đủ bằng chứng để chọn nhóm | — | — |

### 4.2 Thứ tự quyết định nhóm (áp dụng từ trên xuống, dừng ở dòng đầu tiên khớp)

1. Tấm nhỏ **gắn ngay sát** một biển chính, chỉ có chữ/số/mũi tên phạm vi/hình xe → `supplementary`.
2. Hình **bát giác**, tam giác **đỉnh hướng xuống**, hoặc **hình thoi** → `priority`.
3. Hình tròn có **viền đỏ**, hoặc tròn có **gạch chéo/gạch ngang** → `prohibitory` (viền đỏ thắng nền xanh).
4. Hình tròn **nền xanh**, không viền đỏ → `mandatory`.
5. Tam giác **đỉnh hướng lên**, viền đỏ (nền trắng hoặc vàng) → `danger`.
6. Vuông/chữ nhật có **ký hiệu** thuộc danh sách `information` ở bảng 4.1 → `information`.
7. Vuông/chữ nhật chỉ có **tên địa danh, số đường, khoảng cách tới nơi, mũi tên chỉ hướng đi tới nơi** → **không vẽ**
   (biển chỉ hướng, mục 5).
8. Biển không khớp dòng nào ở trên, hoặc nhìn không rõ hình/màu → `unknown` và bật `needs_review`.

Dùng **hình dạng trước, màu sau**. Màu dễ sai khi lóa, tối, phai; hình dạng ổn định hơn.

## 5. Inclusion / exclusion

**Bắt buộc vẽ:** mọi mặt biển thuộc scope nhận ra được là biển (mục 6), kể cả biển nhỏ ở xa, ở mép ảnh, ở đường
nhánh, ở phía bên kia đường, biển tạm công trường (trên giá chân, trên rào chắn, trên xe công trình), biển điện tử.

**Không vẽ (IGNORE):**

- **Biển chỉ hướng / địa danh:** tấm chỉ có tên nơi, số đường, khoảng cách tới nơi, mũi tên chỉ đường đi tới nơi
  (thường xanh, vàng hoặc trắng, nhiều tấm xếp chồng ở ngã tư). Đây là dữ liệu cho bài toán dẫn đường, không thuộc
  bài này.
- **Mặt sau** của biển (tấm xám/kim loại trơn) và biển quay cạnh không thấy mặt.
- **Hình ảnh của biển** không có hiệu lực giao thông: biển in trên quảng cáo, trên thân xe buýt/xe tải, phản chiếu
  trong kính, gương, vũng nước.
- Vật giống biển nhưng không phải biển báo giao thông: đèn giao thông, biển hiệu cửa hàng, biển quảng cáo, biển tên
  phố, số nhà, biển thông báo tư nhân trên hàng rào, cọc tiêu, cọc sọc chỉ hướng, rào chắn sọc đỏ trắng, chữ/mũi tên
  sơn trên mặt đường.
- Ảnh **không có biển** nào đủ điều kiện: để trống. Đây là đáp án đúng, nhưng chỉ kết luận sau khi đã quét hết ảnh ở
  cỡ gốc (mục 10.1).

## 6. Visibility / occlusion

| Tình huống | Cách làm |
|---|---|
| Chỉ là một **chấm màu**, không nhận ra được là tấm biển (thường cạnh dưới khoảng 12 px) | **Không vẽ** |
| Nhận ra là **tấm biển**, thấy rõ hình và màu | Vẽ khung, chọn nhóm theo 4.2 |
| Nhận ra là tấm biển nhưng **không rõ hình hoặc màu** (nhỏ, xa, mờ) | Vẽ khung, `unknown`, bật `needs_review` |
| **Bị che một phần** nhưng còn đủ hình và màu để chọn nhóm | Vẽ khung phần thấy, chọn nhóm |
| Bị che **từ khoảng một nửa trở lên** nhưng vẫn đoán được nhóm | Vẽ khung phần thấy, chọn nhóm, bật `needs_review` |
| Bị che mất **hình đặc trưng** (không biết tròn hay tam giác) | Vẽ khung phần thấy, `unknown`, bật `needs_review` |
| **Bị cắt ở mép ảnh** | Như dòng bị che ở trên, tuỳ phần còn thấy |
| **Lóa, ngược sáng, ban đêm, mưa, phai màu** | Chọn nhóm nếu **hình dạng** còn rõ; chỉ còn màu mà không rõ hình → `unknown` + `needs_review` |
| **Nhìn chéo, biển nghiêng, biển bị gãy/đổ** | Vẫn là biển; chọn nhóm theo hình nhìn thấy, bật `needs_review` |
| **Biển điện tử** đang hiển thị | Chọn nhóm theo nội dung đang hiển thị; tắt / không đọc được → `unknown` + `needs_review` |
| **Biển bị bọc, bị gạch chéo tạm** (ngừng hiệu lực) | Vẽ khung, `unknown`, bật `needs_review` |

**Không suy đoán:** được zoom để nhìn, nhưng nhóm chỉ chọn theo cái nhìn thấy trong ảnh. Không dùng ngữ cảnh (ví dụ
"sắp tới ngã tư nên chắc là biển nhường đường") để đoán nhóm của biển không nhìn rõ.

## 7. Ambiguity / escalation

| Quyết định | Khi nào | Thể hiện trong CVAT |
|---|---|---|
| **LABEL** | Biển thuộc scope, chọn được nhóm chắc chắn | Có khung, `sign_group` là một trong sáu nhóm, `needs_review` tắt |
| **IGNORE** | Thuộc danh sách "không vẽ" ở mục 5, hoặc chỉ là chấm màu | **Không có khung** |
| **UNKNOWN** | Chắc chắn là biển, không đủ bằng chứng chọn nhóm | Có khung, `sign_group = unknown`, `needs_review` bật |
| **ESCALATE** | Có nghi ngờ cần người thứ hai xem | Có khung, `needs_review` bật, `sign_group` là nhóm bạn nghiêng tới nhất hoặc `unknown` |

**`needs_review` bật khi và chỉ khi** có ít nhất một điều kiện sau:

1. `sign_group = unknown`.
2. Biển bị che hoặc cắt từ khoảng một nửa trở lên.
3. Phân vân giữa hai nhóm (chọn nhóm nghiêng tới nhất).
4. Không chắc vật đó là biển báo giao thông hay không (vẫn vẽ).
5. Biển nghiêng, gãy, đổ, bị bọc, hoặc biển điện tử.

Quy tắc khi phân vân:

- **Không chắc có nên vẽ hay không → vẽ và bật `needs_review`.** Bỏ sót một biển cấm/ưu tiên nghiêm trọng hơn vẽ thừa.
  Ngoại lệ: biển chỉ hướng/địa danh luôn không vẽ.
- **`unknown` không bị tính là sai** khi ảnh thật sự không đủ bằng chứng. Đoán sai nhóm thì bị tính sai.
- Trong lúc vẽ không có ai để hỏi. Tình huống guideline chưa nói tới: dùng thứ tự 4.2; không khớp thì `unknown` +
  `needs_review`.

## 8. Temporal rule

**Không áp dụng — task ảnh tĩnh.** Mỗi ảnh vẽ độc lập.

## 9. Examples

| sample_id | Thấy gì | Expected output | Rule áp dụng |
|---|---|---|---|
| GTS27 | Tam giác đỉnh lên viền đỏ (bông tuyết) trên cột điện, ngược sáng; cọc tiêu trắng bên đường | 1 khung ôm tam giác, không lấy cột: `danger`. Cọc tiêu: không vẽ | 3, 4.2 (dòng 5), 5 |
| GTS06 | Biển tròn viền đỏ "30" và **hai tấm phụ** bên dưới (khoảng cách, giờ); đèn giao thông bị cắt ở mép phải trên | 3 khung: `prohibitory`, `supplementary`, `supplementary`. Đèn: không vẽ | 2 (biển phụ), 4.2 (dòng 1, 3), 5 |
| GTS02 | Hai cột, mỗi cột một tam giác **đỉnh hướng xuống** trên một biển tròn xanh mũi tên; mặt sau một biển tròn ở mép trái; nhiều đèn giao thông | 4 khung: 2 × `priority`, 2 × `mandatory`. Mặt sau biển và đèn: không vẽ | 2, 4.2 (dòng 2, 4), 5 |
| GTS21 | Ba tấm chỉ đường có tên nơi (trái); biển vuông xanh người đi bộ; biển tròn xanh mũi tên chéo; cọc sọc xanh trắng; biển quảng cáo bia | 2 khung: `information` (người đi bộ), `mandatory` (mũi tên). Ba tấm chỉ đường, cọc sọc, quảng cáo: không vẽ | 4.2 (dòng 4, 6, 7), 5 |
| GTS26 | Đường trong rừng; **một tam giác rất nhỏ ở xa** giữa ảnh, khó thấy ở cỡ thu nhỏ | 1 khung: `danger` nếu thấy rõ tam giác đỉnh lên viền đỏ, không thì `unknown` + `needs_review`. **Không được để trống ảnh này** | 6, 10.1 |

## 10. Common mistakes

1. **Kết luận "ảnh không có biển" khi chưa quét cỡ gốc.** Zoom và quét cả hai bên đường, phía trên, phía xa (GTS26).
2. **Lấy cột, tấm nền, biển phụ vào khung biển chính.**
3. **Gộp nhiều biển trên một cột vào một khung.** Mỗi mặt biển một khung.
4. **Nhầm hai loại biển người đi bộ:** tam giác đỉnh lên là `danger` (cảnh báo); vuông xanh là `information`.
5. **Nhầm tam giác:** đỉnh hướng lên là `danger`; đỉnh hướng xuống là `priority`.
6. **Nhầm biển nền xanh viền đỏ (cấm dừng/đỗ) là `mandatory`.** Viền đỏ thì là `prohibitory`.
7. **Biển hết lệnh cấm (gạch chéo) chọn `unknown` hoặc `information`.** Nó là `prohibitory`.
8. **Vẽ biển chỉ hướng/địa danh.** Chỉ vẽ tấm vuông/chữ nhật có ký hiệu trong danh sách `information`.
9. **Đoán nhóm cho biển không rõ** thay vì `unknown` + `needs_review`.
10. **Quên bật `needs_review`** ở các điều kiện của mục 7, hoặc bật tuỳ tiện ở biển rõ ràng.
11. **Vẽ mặt sau biển, đèn giao thông, biển quảng cáo** vì trông giống biển.
