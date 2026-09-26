# Team

- **Team:** DeadlineDodgers (theo task reference hiện có; xác nhận tên repo trước khi freeze).
- **Nhóm peer test bài của mình:** Chưa được Lab Coach ghép cặp; cập nhật khi có thông báo.
- **Nhóm mình test bài của:** Chưa được Lab Coach ghép cặp; cập nhật khi có thông báo.
- **Problem family:** Traffic-sign detection và macro-grouping, có thuộc tính liên quan đến hành lang xe ego.
- **Nguồn ảnh:** `gtsdb` (28 ảnh trong catalog của bài); ảnh là cảnh đường Đức, không phải dữ liệu Việt Nam.

| Thành viên | GitHub | Vai trò chính | File phụ trách |
|---|---|---|---|
| Member A | Chưa cung cấp | Topic, downstream contract, guideline owner | `00_team.md`, `01_problem_statement.md`, `02_guideline.md`, `08_revision_log.md` |
| Member B | Chưa cung cấp | CVAT ontology, task setup, chia dữ liệu | `03_cvat_labels.json`, `03_ontology_and_cvat_setup.md`, `sample_pack.csv`, `09_cvat_export_or_task_reference.txt` |
| Member C | Chưa cung cấp | Edge-case owner, gold decision owner | `04_edge_cases/edge_case_cards.md`, `04_edge_cases/gold_decisions.csv` |
| Member D | Chưa cung cấp | QA, calibration coordination, peer handoff | `05_qa_plan.md`, `06_calibration_report.csv`, `07_blind_handoff/` |

Tên thật, GitHub handle, tên nhóm và cặp peer phải được nhóm/Lab Coach xác nhận; không suy đoán từ repo mẫu.
Calibration cần tối thiểu hai thành viên label độc lập. Chỉ Member C giữ gold trước blind handoff; các thành viên còn lại
không mở file gold trong thời gian peer test.
