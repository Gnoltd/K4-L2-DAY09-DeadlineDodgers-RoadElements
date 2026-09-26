# Peer feedback + owner response

Phần 1 do **nhóm peer** trả lời (gửi kèm file export). Phần 2 do **nhóm owner** điền. Thay mọi placeholder mới
là xong (gate G5).

- **Nhóm peer:** nhóm Traffic Light (bài đèn giao thông: trạng thái màu + relevance), cặp peer với DeadlineDodgers
- **Người label blind:** an.holyann, an271220059_ (export `peer_output/annotations-2.xml`, 5 ảnh GTS09, GTS18, GTS23,
  GTS26, GTS27)

## 1. Peer trả lời

1. **Rule nào rõ nhất / giúp quyết định nhanh nhất?** Quy tắc nhận dạng nhóm biển theo hình học và màu sắc (Mục 4.1
   & 4.2) rất rõ ràng: tam giác đỉnh lên là danger, tam giác đỉnh xuống / hình thoi là priority, viền đỏ là
   prohibitory. Nguyên tắc "viền đỏ luôn thắng nền xanh" và "hình dạng trước, màu sắc sau" giúp quyết định sign_group
   cực kỳ nhanh và dứt khoát.
2. **Rule nào mơ hồ hoặc phải tự suy diễn?** Quy tắc xác định ego_relevant (Mục 4.3) tại các nút giao có góc nhìn chéo
   hoặc đường nhánh: người vẽ không biết xe sẽ đi thẳng hay rẽ nên rất khó phân định dứt khoát giữa relevant,
   not_relevant hay phải chọn uncertain. Ngoài ra, ranh giới kích thước biển quá xa (dưới 12 px thì bỏ qua, còn trên
   12 px mờ thì gán unknown) vẫn mang tính cảm tính khi zoom ảnh.
3. **Sample nào khiến guideline "vỡ"?** Ảnh có cụm biển tên địa danh/chỉ hướng xếp chồng nhau (như cụm biển
   Dortmund/Hattingen): nếu người vẽ không đọc rất kỹ mục loại trừ (Mục 5) thì rất dễ nhầm cụm này thành biển chỉ dẫn
   information. Tương tự, các biển ở góc đường nhánh rất dễ gây bất đồng về việc xe có rẽ vào đó hay không.
4. **Attribute / default nào trong CVAT dễ gây thao tác sai?** Giá trị mặc định của ego_relevant để sẵn là relevant
   và visibility để sẵn là clear rất dễ khiến người vẽ quên không đổi sang not_relevant (khi gặp biển đường nhánh)
   hoặc quên chọn poor (khi biển ở xa/bị mờ).
5. **Một thay đổi cụ thể giúp annotator mới ít hỏi hơn?** Nên bổ sung thêm 1 hình ảnh ví dụ trực quan trong Mục 9
   gạch chéo đỏ cụm biển tên địa danh xếp chồng (chỉ hướng đường) để người vẽ thấy ngay là KHÔNG VẼ mà không cần phải
   hỏi lại. Đồng thời, có thể cân nhắc để ego_relevant dạng bắt buộc chọn thay vì để mặc định là relevant để tránh lỗi
   bỏ quên.

## 2. Owner phân loại

Kết quả chấm (`transfer_score.csv`, `gts_summary.md`): **GTS 84.8** — D 12/14, C 2/3 (1 critical escape), G 1/1,
0 câu hỏi trong blind window. Owner không tranh luận để bảo vệ guideline. Mỗi feedback và mỗi decision peer làm sai
được xếp vào một hướng xử lý.

| Feedback / decision sai | Nguyên nhân (guideline gap / data ambiguity / execution error) | Xử lý (accept + revise / reject with evidence / add escalation rule) | Bằng chứng |
|---|---|---|---|
| **GTS23 d1 (critical):** hai biển STOP gán `prohibitory` thay vì `priority` | Guideline gap. Bát giác → `priority` có ở 4.1 và 4.2 dòng 2, nhưng biển STOP toàn màu đỏ nên người vẽ dễ áp dòng 3 ("viền đỏ") theo màu; mục 10 không cảnh báo lỗi này, và bảng hình dạng (`sign_group_legend.png`) không đi theo gói blind | Accept + revise: thêm lỗi thường gặp "STOP / nhường đường màu đỏ vẫn là `priority`, không phải `prohibitory`"; ghi rõ ở 4.2 dòng 3 "trừ bát giác (đã xử lý ở dòng 2)"; đưa bảng hình dạng dạng chữ vào guideline | `transfer_score.csv` GTS23 d1; peer_evidence `sign_group=prohibitory ×2`; peer câu 1 chỉ nhắc tam giác ngược / hình thoi là priority |
| **GTS27 d2:** biển ngược sáng để `visibility=clear` thay vì `poor` | Guideline gap (default). Mục 6 ghi ngược sáng → `poor`, nhưng default `clear` làm lỗi quên đổi không lộ ra; chính peer nêu rủi ro này ở câu 4 | Accept + revise: thêm dòng Self-QC "ảnh ngược sáng / lóa / đêm → `visibility` không được `clear`"; cân nhắc đổi default `visibility` về `__undefined__` ở lượt gán nhãn tiếp theo | `transfer_score.csv` GTS27 d2; calibration GTS01/GTS10/GTS11 cũng có lỗi quên attribute |
| Feedback 2: `ego_relevant` ở nút giao / đường nhánh phải tự suy diễn vì không biết xe đi thẳng hay rẽ | Data ambiguity (ảnh tĩnh không có lộ trình) + guideline gap (thiếu câu chốt) | Add escalation rule: ghi rõ ở 4.3 "không biết ego đi thẳng hay rẽ → giả định ego đi thẳng theo làn hiện tại; biển chỉ áp dụng cho hướng rẽ → `uncertain` + `needs_review`" | Peer câu 2, câu 3; peer vẫn làm đúng các dòng ego_relevant trong gold (GTS18 d3, GTS23 d2) |
| Feedback 2: ngưỡng 12 px / chấm màu mang tính cảm tính khi zoom | Guideline gap | Accept + revise: định nghĩa đo bằng khung đã vẽ (cạnh ngắn của khung ở zoom 100%), thêm ví dụ crop một biển vẽ và một chấm không vẽ (GTS07) | Peer câu 2; calibration report bảng "Hướng xử lý v2" |
| Feedback 3: cụm biển chỉ hướng xếp chồng dễ nhầm thành `information` | Guideline gap (thiếu ví dụ trực quan) | Accept + revise: thêm ảnh ví dụ có gạch chéo đỏ cụm biển chỉ hướng xếp chồng (dùng ảnh example/calibration, ví dụ GTS04 đã có tấm chỉ hướng) và câu "biển có tên nơi / số đường → không vẽ, kể cả khi nền xanh" | Peer câu 3, câu 5; peer vẫn làm đúng GTS09 d1 (không vẽ 6 tấm chỉ đường) |
| Feedback 4–5: default `relevant` / `clear` dễ quên đổi; đề xuất bắt buộc chọn `ego_relevant` | Guideline gap (thiết kế default) | Accept + revise: v3 đổi default `ego_relevant` và `visibility` về `__undefined__` (bắt buộc chọn) cho lượt gán nhãn tiếp theo; Self-QC lọc `__undefined__`. Không đổi schema của blind đã freeze | Peer câu 4, 5; GTS27 d2; calibration có 4+ khung bỏ trống attribute |
| Peer vẽ thêm biển tròn xanh nhỏ ở GTS09 [353,548,369,565] (`mandatory`, `poor`) không có trong gold | Data ambiguity (gold không có dòng cho biển này) | Reject with evidence: không trừ điểm; biển nhận ra là tấm biển nên vẽ là đúng mục 6 | `transfer_score.csv` GTS09 d3 note |
