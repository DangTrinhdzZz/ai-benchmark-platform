# Product Requirements Document — AIP-03

## 1. Tổng quan tài liệu

| Thuộc tính | Giá trị |
| --- | --- |
| Tên sản phẩm | AI Agent tự động chạy Eval & Benchmark LLM và sinh báo cáo |
| Mã đề | AIP-03 |
| Phiên bản | 0.1 |
| Trạng thái | Draft for Team Review |
| Giai đoạn | G1 — Chốt bài toán và thiết kế |
| Nhóm | 4 thành viên |
| Phạm vi | MVP |

PRD này mô tả hành vi và tiêu chí cần thống nhất, không khóa kiến trúc hay công nghệ triển khai. Các nội dung `TBD` phải được người chịu trách nhiệm xác nhận trước PRD v1.0 hoặc trước mốc nghiệm thu liên quan.

## 2. Mục đích

Định nghĩa một MVP web có thể benchmark nhiều LLM trong điều kiện kiểm soát, lưu đủ bằng chứng để kiểm tra/tái lập, và chuyển kết quả thành báo cáo hỗ trợ lựa chọn model theo đánh đổi chất lượng, độ trễ và chi phí. PRD là cơ sở để nhóm thiết kế wireframe, kiến trúc, backlog và test plan.

## 3. Bối cảnh và vấn đề

So sánh LLM thường được thực hiện bằng script rời, dataset hoặc prompt thay đổi không được version hóa, và điểm số thiếu liên kết với output gốc. Một bảng điểm chất lượng đơn lẻ cũng không trả lời được model có phù hợp với ràng buộc latency và ngân sách hay không. Khi thiếu metadata, nhóm khó giải thích vì sao kết quả thay đổi hoặc tái lập quyết định.

Sản phẩm cần biến benchmark thành quy trình có kiểm soát: xác nhận đầu vào, ước tính chi phí, thực thi có trạng thái, ghi nhận lỗi/usage, so sánh minh bạch và sinh khuyến nghị có căn cứ. Sản phẩm không tìm một “model tốt nhất cho mọi trường hợp”.

## 4. Mục tiêu sản phẩm

- Cho phép Kỹ sư Eval chạy một benchmark nhất quán trên ít nhất 3 model.
- Đánh giá tối thiểu 4 loại metric đã được nhóm phê duyệt.
- Trình bày đồng thời Quality × Latency × Cost và dữ liệu thiếu/lỗi.
- Lưu cấu hình, phiên bản và provenance đủ để audit và tái lập hợp lý.
- Sinh báo cáo/khuyến nghị có giải thích, dẫn chiếu đến kết quả nguồn.
- Bảo vệ secret và dữ liệu test nhạy cảm theo quyền và chính sách retention.

## 5. Phi mục tiêu

- Tự động triển khai hoặc đổi model, prompt, routing, guardrail hay safety rule trong production.
- Tuyên bố một model tốt nhất cho mọi tác vụ hoặc mọi profile vận hành.
- Lập lịch, regression gate, cảnh báo nâng cao, Pareto frontier tự động và trace root-cause trong MVP.
- Xây dựng hệ thống quản trị model/dataset cấp doanh nghiệp ngoài nhu cầu benchmark tối thiểu.
- Cam kết tính quyết định tuyệt đối khi provider không bảo đảm deterministic output.

## 6. Persona

### P-01 — Kỹ sư Eval

- **Mục tiêu:** cấu hình phép so sánh công bằng, chạy và theo dõi benchmark, điều tra lỗi, kiểm tra mẫu, tái lập và xuất báo cáo.
- **Nhu cầu:** version rõ ràng, validation trước run, chi tiết output/metric, metadata kỹ thuật và audit trail.
- **Rủi ro cần tránh:** chạy nhầm dataset/model, lộ dữ liệu, vượt ngân sách, diễn giải điểm tổng hợp sai.

### P-02 — Quản lý

- **Mục tiêu:** hiểu phương án nào phù hợp với ưu tiên kinh doanh và bằng chứng nào hỗ trợ khuyến nghị.
- **Nhu cầu:** leaderboard rõ ràng, so sánh quality/latency/cost, giới hạn của dữ liệu và báo cáo có thể chia sẻ.
- **Rủi ro cần tránh:** quyết định chỉ dựa trên một điểm, xem kết quả partial như hoàn tất, hoặc hiểu khuyến nghị là tự động phê duyệt production.

## 7. Ma trận phân quyền

Quyền chính xác của Quản lý đối với thao tác chạy/duyệt đang `TBD`. Ma trận dưới đây là baseline an toàn để review, chưa phải quyết định cuối cùng.

| Năng lực | Kỹ sư Eval | Quản lý |
| --- | --- | --- |
| Đăng nhập, xem hồ sơ của mình | Có | Có |
| Xem model/dataset/metric đã công bố | Có | Có |
| Tạo/sửa cấu hình nháp | Có | Không |
| Xác nhận và khởi chạy | Có | TBD |
| Hủy/retry run do mình được phép quản lý | Có | TBD |
| Xem kết quả, leaderboard, báo cáo | Có | Có |
| Xem output chứa dữ liệu nhạy cảm | Theo quyền dữ liệu | Theo quyền dữ liệu |
| Sinh/export báo cáo | Có | Có |
| Quản lý người dùng/quyền | Ngoài MVP | Ngoài MVP |
| Xem audit log tối thiểu | Có theo phạm vi | Có theo phạm vi |

Mọi endpoint và hành động UI phải kiểm tra quyền phía server; ẩn nút trên UI không được xem là cơ chế bảo vệ.

## 8. User stories

- **US-01:** Là Kỹ sư Eval, tôi muốn chọn model/version, dataset/version và metric để mọi model được đánh giá trong cùng cấu hình đã lưu.
- **US-02:** Là Kỹ sư Eval, tôi muốn validation và ước tính chi phí trước khi chạy để sửa cấu hình sai và kiểm soát ngân sách.
- **US-03:** Là Kỹ sư Eval, tôi muốn theo dõi tiến độ/lỗi theo model và batch để biết run còn hoạt động hay cần can thiệp.
- **US-04:** Là Kỹ sư Eval, tôi muốn xem output và điểm theo từng mẫu để kiểm chứng điểm tổng hợp.
- **US-05:** Là Kỹ sư Eval, tôi muốn retry phần lỗi hoặc tái lập từ cấu hình cũ mà không làm trùng kết quả hợp lệ.
- **US-06:** Là Quản lý, tôi muốn so sánh quality, latency và cost để chọn phương án phù hợp ưu tiên thay vì chỉ chọn điểm cao nhất.
- **US-07:** Là Quản lý, tôi muốn khuyến nghị nêu căn cứ, giả định và giới hạn để review trước quyết định production.
- **US-08:** Là người dùng được cấp quyền, tôi muốn export báo cáo và truy lại run nguồn để thông tin có thể audit.

## 9. Luồng nghiệp vụ chính

1. Người dùng đăng nhập; hệ thống áp dụng vai trò và phạm vi dữ liệu.
2. Kỹ sư Eval tạo cấu hình nháp, chọn tối thiểu 3 model, một dataset version và bộ metric.
3. Hệ thống validation tính đầy đủ/tương thích và tạo ước tính chi phí có giả định.
4. Người có quyền xác nhận cấu hình; run được đóng băng snapshot và đưa vào hàng đợi.
5. Worker thực thi theo batch, thu output, metric, usage, latency, cost và lỗi.
6. Hệ thống tổng hợp trạng thái; lỗi một phần không bị trình bày như hoàn tất đầy đủ.
7. Người dùng xem chi tiết, leaderboard và các chiều so sánh.
8. Hệ thống sinh bản nháp khuyến nghị/báo cáo; con người review trước khi dùng cho quyết định.
9. Cấu hình và provenance được giữ lại để audit hoặc tạo một run tái lập mới.

## 10. Yêu cầu chức năng

### FR-01 — Authentication và role-based access

- **Mô tả:** xác thực người dùng, gán tối thiểu hai vai trò và kiểm tra quyền trên UI lẫn server cho mọi tài nguyên/hành động.
- **Actor chính:** Kỹ sư Eval, Quản lý.
- **Priority:** Must.
- **Acceptance criteria:**
  1. Người dùng chưa xác thực bị từ chối khi truy cập tài nguyên benchmark cần bảo vệ.
  2. Sau đăng nhập, người dùng chỉ thấy và thực hiện hành động được cấp theo ma trận quyền.
  3. Server trả về phản hồi từ chối phù hợp nếu gọi trực tiếp hành động không có quyền, kể cả khi bỏ qua UI.
  4. Sự kiện đăng nhập và thao tác nhạy cảm được ghi audit metadata mà không ghi credential.

### FR-02 — Quản lý danh sách model và version

- **Mô tả:** lưu định danh logic của model, provider, version/endpoint identifier, trạng thái khả dụng và metadata cần cho benchmark; secret được tham chiếu an toàn, không lưu trong bản ghi hiển thị.
- **Actor chính:** Kỹ sư Eval.
- **Priority:** Must.
- **Acceptance criteria:**
  1. Người có quyền có thể tạo, xem và cập nhật metadata model mà không nhập secret vào trường mô tả.
  2. Mỗi model selection trong run trỏ đến một version/identifier cụ thể và snapshot cấu hình không bí mật.
  3. Model bị vô hiệu hóa không thể được thêm vào run mới nhưng lịch sử cũ vẫn xem được.
  4. Hệ thống phát hiện thiếu trường bắt buộc trước khi cho xác nhận run.

### FR-03 — Quản lý dataset và dataset version

- **Mô tả:** đăng ký dataset, version, schema, nguồn/provenance, checksum hoặc định danh bất biến, mức nhạy cảm và quyền truy cập.
- **Actor chính:** Kỹ sư Eval.
- **Priority:** Must.
- **Acceptance criteria:**
  1. Một run chỉ tham chiếu dataset version đã tồn tại và vượt qua schema validation.
  2. Dataset version đã được dùng không bị sửa âm thầm; thay đổi nội dung tạo version/định danh mới.
  3. Metadata run ghi lại chính xác dataset version và checksum/định danh bất biến.
  4. Người không có quyền dữ liệu không xem được nội dung mẫu nhạy cảm qua UI, API, export hoặc log.

### FR-04 — Cấu hình metric

- **Mô tả:** chọn evaluator/metric version, tham số, hướng tốt hơn, đơn vị và quy tắc áp dụng cho task; hỗ trợ bốn nhóm metric MVP.
- **Actor chính:** Kỹ sư Eval.
- **Priority:** Must.
- **Acceptance criteria:**
  1. Cấu hình lưu tên, version, tham số và hướng diễn giải của từng metric.
  2. Validation từ chối evaluator không tương thích schema input/output đã khai báo.
  3. Kết quả phân biệt rõ điểm không áp dụng, thiếu và lỗi với giá trị số hợp lệ.
  4. Nếu dùng LLM-as-judge, hệ thống lưu judge model/version, prompt version và tham số không bí mật.

### FR-05 — Tạo benchmark run

- **Mô tả:** tạo run ở trạng thái `DRAFT` từ lựa chọn model, dataset, metric và tham số thực thi; cấp ID duy nhất.
- **Actor chính:** Kỹ sư Eval.
- **Priority:** Must.
- **Acceptance criteria:**
  1. Người dùng có thể lưu nháp và tiếp tục chỉnh sửa trước khi xác nhận.
  2. Run có ID duy nhất, người tạo, timestamps và liên kết tới các version đã chọn.
  3. Một run dự kiến demo không thể qua validation nếu có ít hơn 3 model hoặc không đáp ứng bộ metric đã duyệt.
  4. Sau khi rời `DRAFT`, snapshot thực thi không bị sửa; thay đổi tạo run mới.

### FR-06 — Xác nhận cấu hình và ước tính chi phí

- **Mô tả:** validation toàn bộ cấu hình, trình bày khối lượng/giả định và ước tính chi phí trước khi người có quyền xác nhận.
- **Actor chính:** Kỹ sư Eval; Quản lý nếu quyền được duyệt.
- **Priority:** Must.
- **Acceptance criteria:**
  1. Hệ thống liệt kê lỗi chặn chạy và cảnh báo không chặn theo từng trường/cấu phần.
  2. Ước tính hiển thị currency, thời điểm/bảng giá tham chiếu, giả định token và phạm vi không chắc chắn nếu có.
  3. Người dùng phải xác nhận rõ ràng trước khi run chuyển sang `QUEUED`.
  4. Nếu không thể ước tính đáng tin cậy, hệ thống nêu lý do và áp dụng quy tắc phê duyệt `TBD`, không hiển thị số bịa đặt.

### FR-07 — Chạy batch và theo dõi trạng thái

- **Mô tả:** đưa công việc vào hàng đợi, xử lý theo batch và hiển thị trạng thái/progress ở cấp run và model.
- **Actor chính:** Kỹ sư Eval.
- **Priority:** Must.
- **Acceptance criteria:**
  1. Run hợp lệ đi qua state machine đã định nghĩa, không nhảy qua điều kiện bảo vệ.
  2. UI/API hiển thị trạng thái hiện tại, timestamps và tiến độ dựa trên số đơn vị đã xử lý so với tổng dự kiến.
  3. Việc refresh hoặc polling không tạo thêm benchmark task.
  4. Mỗi đơn vị thực thi có correlation/run ID để truy vết mà không lộ dữ liệu nhạy cảm.

### FR-08 — Thu thập output, metric, latency, token usage, cost và lỗi

- **Mô tả:** lưu kết quả thô/đã chuẩn hóa và metadata cần thiết theo model, mẫu, lần thử và evaluator.
- **Actor chính:** Hệ thống; Kỹ sư Eval xem dữ liệu.
- **Priority:** Must.
- **Acceptance criteria:**
  1. Với mỗi attempt, hệ thống ghi model version, dataset item reference, timestamps, latency, trạng thái và lỗi đã làm sạch.
  2. Token usage/cost ghi giá trị, nguồn và currency khi provider cung cấp; dữ liệu không có được đánh dấu missing, không mặc định bằng 0.
  3. Output và điểm từng metric liên kết được với sample/run/evaluator version.
  4. Secret và dữ liệu bị cấm không xuất hiện trong application log hoặc error message.

### FR-09 — Kết quả chi tiết và leaderboard

- **Mô tả:** trình bày tổng hợp theo model và drill-down tới mẫu/metric; leaderboard tuân thủ quy tắc ở mục 12.
- **Actor chính:** Kỹ sư Eval, Quản lý.
- **Priority:** Must.
- **Acceptance criteria:**
  1. Người dùng xem được score/summary, latency, cost, sample count, tỷ lệ lỗi và mức dữ liệu thiếu cho từng model.
  2. Có thể truy từ số tổng hợp tới kết quả mẫu được phép xem.
  3. Run `PARTIALLY_COMPLETED` và model thiếu dữ liệu được gắn nhãn nổi bật, không xếp hạng như dữ liệu đầy đủ nếu chưa có quy tắc đã duyệt.
  4. Leaderboard hiển thị công thức/profile/version đang dùng và không mặc định coi hạng 1 là phù hợp mọi bối cảnh.

### FR-10 — Sinh khuyến nghị có giải thích

- **Mô tả:** tạo khuyến nghị dựa trên kết quả, profile/ràng buộc đã chọn và nêu bằng chứng, đánh đổi, giới hạn.
- **Actor chính:** Kỹ sư Eval, Quản lý.
- **Priority:** Must.
- **Acceptance criteria:**
  1. Mỗi khuyến nghị nêu profile/ràng buộc, model đề xuất, phương án thay thế và lý do theo quality/latency/cost.
  2. Các số liệu/căn cứ có link hoặc ID truy về run và kết quả nguồn.
  3. Nếu dữ liệu thiếu, run partial hoặc không có model thỏa ràng buộc, hệ thống cảnh báo hoặc không đưa khuyến nghị chắc chắn.
  4. Giao diện ghi rõ đây là hỗ trợ quyết định cần human review, không tự động áp dụng vào production.

### FR-11 — Sinh và export báo cáo

- **Mô tả:** sinh báo cáo có cấu hình, phương pháp, kết quả, khuyến nghị, giới hạn và provenance; hỗ trợ định dạng export sẽ được chốt.
- **Actor chính:** Kỹ sư Eval, Quản lý.
- **Priority:** Must.
- **Acceptance criteria:**
  1. Báo cáo chứa run ID, thời điểm, model/dataset/evaluator version, trạng thái và mức độ đầy đủ.
  2. Báo cáo thể hiện quality, latency, cost, lỗi và căn cứ khuyến nghị, không chỉ điểm tổng hợp.
  3. Export tuân thủ quyền dữ liệu và redaction giống giao diện nguồn.
  4. File export có định dạng/version và thời điểm sinh; lựa chọn Markdown/PDF/cả hai là `TBD`.

### FR-12 — Lịch sử benchmark và khả năng tái lập

- **Mô tả:** tìm/xem run cũ và tạo một run mới từ snapshot cấu hình đã lưu, đồng thời ghi rõ khác biệt môi trường có thể ảnh hưởng.
- **Actor chính:** Kỹ sư Eval, Quản lý (chỉ xem).
- **Priority:** Must.
- **Acceptance criteria:**
  1. Người dùng được phép có thể tìm run theo ID, thời gian, model, dataset version và trạng thái.
  2. Hành động tái lập tạo run ID mới và liên kết `reproduced_from`; không sửa kết quả gốc.
  3. Hệ thống so sánh cấu hình gốc với tài nguyên hiện có và cảnh báo model/endpoint/price không còn giống.
  4. Snapshot đủ để xác định input version, evaluator, tham số và code/config version khả dụng.

### FR-13 — Retry, cancel và partial failure

- **Mô tả:** cho phép hủy công việc còn hoạt động, retry có kiểm soát các đơn vị lỗi và tổng hợp partial failure chính xác.
- **Actor chính:** Kỹ sư Eval; Quản lý nếu quyền được duyệt.
- **Priority:** Must.
- **Acceptance criteria:**
  1. Cancel là idempotent; yêu cầu lặp lại không tạo trạng thái mâu thuẫn và task chưa bắt đầu không tiếp tục được phân phối.
  2. Retry chỉ tạo attempt mới cho phạm vi được chọn, giữ lịch sử attempt cũ và không nhân đôi kết quả đã chấp nhận.
  3. Run có cả đơn vị thành công và thất bại kết thúc ở `PARTIALLY_COMPLETED` theo quy tắc đã duyệt.
  4. UI hiển thị phạm vi retry, số attempt và nguyên nhân lỗi đã làm sạch.

### FR-14 — Audit log tối thiểu

- **Mô tả:** ghi lại ai làm gì, khi nào và trên tài nguyên nào cho các hành động ảnh hưởng cấu hình, thực thi, quyền và báo cáo.
- **Actor chính:** Hệ thống; người dùng được cấp quyền xem.
- **Priority:** Must.
- **Acceptance criteria:**
  1. Tạo/sửa/xác nhận run, cancel, retry, export và thay đổi metadata quan trọng tạo audit event.
  2. Event có actor, action, target ID, timestamp, outcome và correlation ID; không chứa secret hoặc raw credential.
  3. Audit event đã ghi không thể bị sửa qua chức năng người dùng thông thường.
  4. Quyền xem và thời gian lưu audit log tuân theo chính sách retention `TBD`.

## 11. Yêu cầu metric và phương pháp chấm

### Bốn nhóm dự kiến cho MVP

1. **Accuracy hoặc task quality:** mức đúng/phù hợp với mục tiêu tác vụ; định nghĩa và ground truth phụ thuộc dataset, đang `TBD`.
2. **Hallucination hoặc faithfulness:** mức câu trả lời bám nguồn/context và không thêm khẳng định không được hỗ trợ; phương pháp chấm đang `TBD`.
3. **Format compliance:** đáp ứng schema, cấu trúc hoặc ràng buộc output đã công bố.
4. **Latency và cost:** số đo vận hành gồm latency và chi phí/usage.

Đội phải chốt rõ khi nghiệm thu: **latency và cost là một nhóm metric vận hành hay được tính là hai metric riêng**. Việc nói “ít nhất 4 metric” chỉ được coi là đạt khi định nghĩa, cách tính, đơn vị, chiều tốt hơn và test case của từng metric đã được phê duyệt.

### Quy tắc chung

- Metric phải có ID/version, phạm vi áp dụng, input/output schema, đơn vị và quy tắc dữ liệu thiếu.
- Phân biệt deterministic evaluator, heuristic, model-based evaluator và phép đo provider.
- Tổng hợp phải lưu số mẫu hợp lệ, số mẫu loại bỏ/thất bại và cách xử lý missing value.
- Không điền 0 thay cho missing/error; không gộp metric khác đơn vị nếu chưa có normalization được duyệt.
- Nếu dùng LLM-as-judge: version judge model/prompt/rubric, giữ bằng chứng chấm theo quyền, và thực hiện human sampling. Cỡ mẫu, tiêu chí agreement và ngưỡng chấp nhận đang `TBD` do Kỹ sư Eval và Product Owner duyệt.
- Latency cần định nghĩa điểm đo, percentile/thống kê và điều kiện concurrency; cost cần định nghĩa nguồn giá, currency, thời điểm và cách xử lý cache/free tier. Tất cả đang `TBD` trước test plan.

## 12. Quy tắc leaderboard

- Chỉ so sánh các kết quả dùng cùng dataset version, metric/evaluator version và điều kiện được xác định là tương đương; khác biệt phải hiển thị và có thể làm run không đủ điều kiện xếp hạng.
- Hiển thị từng chiều quality, latency, cost và mức độ đầy đủ trước hoặc cùng với điểm tổng hợp.
- Công thức tổng hợp, trọng số, normalization, tie-breaker và ngưỡng dữ liệu tối thiểu đều `TBD`; UI không được âm thầm áp dụng mặc định không công bố.
- Nếu có nhiều profile như `balanced`, `quality-first`, `cost-first`, mỗi profile phải version hóa và hiển thị trọng số/ràng buộc; việc có ba profile này đang `TBD`.
- Kết quả partial, metric error hoặc sample coverage không đủ phải được gắn nhãn; điều kiện được xếp hạng đang `TBD`.
- Hạng nhất chỉ có nghĩa trong dataset, metric, profile và thời điểm cụ thể; không được diễn giải là tốt nhất cho mọi trường hợp.
- Khi đồng hạng, hiển thị đồng hạng cho tới khi tie-breaker được đội phê duyệt.

## 13. Quy tắc khuyến nghị model

- Đầu vào khuyến nghị gồm benchmark run đã chọn, profile/ràng buộc đã công bố và dữ liệu quality/latency/cost khả dụng.
- Khuyến nghị phải nêu: model phù hợp, điều kiện áp dụng, bằng chứng định lượng, đánh đổi, dữ liệu thiếu, rủi ro và ít nhất một phương án thay thế khi có.
- Mọi số liệu phải truy ngược được về result ID/metric/run; nội dung do AI sinh phải phân biệt với dữ liệu đo.
- Không đưa khuyến nghị chắc chắn khi run thất bại, coverage dưới ngưỡng được duyệt hoặc không model nào thỏa ràng buộc.
- Công thức/profile chưa được duyệt thì chỉ trình bày so sánh, không tự suy ra lựa chọn cuối cùng.
- Human review là bắt buộc trước khi khuyến nghị được dùng cho quyết định production; MVP không tự áp dụng thay đổi.

## 14. Yêu cầu dữ liệu

- **Thực thể tối thiểu:** User/Role, Model/ModelVersion, Dataset/DatasetVersion, Metric/EvaluatorVersion, BenchmarkConfiguration, BenchmarkRun, Task/Attempt, SampleResult, MetricResult, Usage/Cost, Recommendation, Report và AuditEvent.
- **Version/provenance:** lưu ID bất biến hoặc checksum cho dataset, model/provider identifier, evaluator/prompt version, tham số, code/config revision và timestamps.
- **Dữ liệu nhạy cảm:** mỗi dataset có phân loại và chính sách truy cập; danh sách dữ liệu được coi là nhạy cảm đang `TBD` với Data Owner/Security.
- **Secret:** chỉ tham chiếu qua cơ chế quản lý secret; không lưu plaintext trong database field hiển thị, log, report hoặc repository.
- **Integrity:** kết quả gốc và audit trail không bị ghi đè bởi retry/reproduction; attempt mới có liên kết riêng.
- **Retention/deletion:** thời hạn lưu output, báo cáo, log, audit và cơ chế xóa theo yêu cầu đang `TBD`; việc xóa phải giữ đủ dấu vết không nhạy cảm theo chính sách được duyệt.
- **Export:** áp dụng cùng authorization/redaction như nguồn; export mang provenance và thời điểm sinh.

## 15. Yêu cầu phi chức năng

### NFR-01 — Security

- **Yêu cầu:** TLS khi truyền; secret ngoài source/log; authorization phía server; least privilege; input validation; redaction lỗi/log; bảo vệ dữ liệu theo phân loại.
- **Cách xác minh:** security review và test chứng minh người không có quyền không đọc/chạy/export tài nguyên bị giới hạn; scan repository/log fixture không phát hiện secret mẫu thật.
- **Ngưỡng/duyệt:** thuật toán mã hóa, cơ chế secret và tiêu chuẩn security cuối cùng `TBD` — Tech Lead/Security duyệt.

### NFR-02 — Reproducibility

- **Yêu cầu:** mọi run lưu snapshot version, tham số, provenance và liên kết attempt; reproduction tạo run mới và công khai yếu tố không deterministic.
- **Cách xác minh:** chọn một run hoàn tất, tạo lại cấu hình và đối chiếu metadata bắt buộc không bị thiếu.
- **Ngưỡng/duyệt:** mức tương đồng output/score chấp nhận được `TBD` — Kỹ sư Eval duyệt theo từng metric/provider.

### NFR-03 — Reliability và idempotency

- **Yêu cầu:** submit, polling, cancel và retry không tạo task/kết quả trùng do gọi lặp; lỗi worker không làm mất trạng thái đã ghi; partial failure được bảo toàn.
- **Cách xác minh:** test gửi lặp cùng idempotency key, restart worker giữa task và retry phần lỗi; kiểm tra số attempt/kết quả nhất quán.
- **Ngưỡng/duyệt:** availability, retry limit và recovery objective `TBD` — Tech Lead/Product Owner duyệt.

### NFR-04 — Performance

- **Yêu cầu:** UI/API phản hồi phù hợp cho thao tác tương tác; danh sách lớn có phân trang; benchmark chạy bất đồng bộ và không chặn request web.
- **Cách xác minh:** load test với quy mô dataset/run demo đã chốt, đo percentile cho các API trọng yếu và throughput worker.
- **Ngưỡng/duyệt:** dataset size, concurrency, response-time percentile và throughput cụ thể `TBD` — Product Owner/Tech Lead duyệt; không giả định số trước khi có workload.

### NFR-05 — Observability

- **Yêu cầu:** structured log, metric và trace/correlation ID cho vòng đời run; dashboard/cảnh báo tối thiểu phản ánh lỗi thực thi mà không lộ dữ liệu nhạy cảm.
- **Cách xác minh:** truy một run từ API đến worker bằng correlation ID và xác định được failed component từ telemetry đã redaction.
- **Ngưỡng/duyệt:** retention, sampling và alert threshold `TBD` — Tech Lead duyệt.

### NFR-06 — Maintainability

- **Yêu cầu:** ranh giới module rõ, interface evaluator/provider có version, tài liệu quyết định và test cho logic quan trọng; không khóa công nghệ trước phê duyệt.
- **Cách xác minh:** code review, test suite và thử thêm một adapter giả mà không sửa logic domain không liên quan.
- **Ngưỡng/duyệt:** coverage và quality gate `TBD` — Tech Lead duyệt.

### NFR-07 — Deployability

- **Yêu cầu:** cấu hình tách khỏi artifact; môi trường không chứa secret trong source; có quy trình deploy/rollback được tài liệu hóa; web và worker có health signal phù hợp.
- **Cách xác minh:** triển khai vào môi trường demo từ hướng dẫn sạch và thực hiện rollback rehearsal theo kế hoạch.
- **Ngưỡng/duyệt:** hosting, container/orchestrator, thời gian deploy/rollback `TBD` — Tech Lead duyệt.

### NFR-08 — Privacy và data retention

- **Yêu cầu:** data minimization, phân quyền theo dataset, redaction, retention/deletion và export policy; không đưa dữ liệu nhạy cảm vào công cụ ngoài phạm vi đã duyệt.
- **Cách xác minh:** test quyền trên UI/API/export/log, kiểm tra xóa/retention bằng fixture phân loại và review data flow.
- **Ngưỡng/duyệt:** loại dữ liệu nhạy cảm và thời hạn lưu từng loại `TBD` — Data Owner/Security duyệt.

## 16. Màn hình và UI Flow dự kiến

### Màn hình

1. Đăng nhập.
2. Dashboard/lịch sử benchmark.
3. Danh sách và chi tiết model version.
4. Danh sách và chi tiết dataset version.
5. Tạo/sửa benchmark configuration.
6. Validation, ước tính chi phí và xác nhận.
7. Theo dõi run/progress/error.
8. Kết quả chi tiết và sample drill-down.
9. Leaderboard/so sánh quality-latency-cost.
10. Khuyến nghị và report preview/export.
11. Audit view tối thiểu nếu quyền cho phép.

### UI Flow chính

`Đăng nhập → Lịch sử → Tạo run → Chọn model/dataset/metric → Validation & cost estimate → Xác nhận → Theo dõi → Kết quả → So sánh → Khuyến nghị → Human review → Export`

Nhánh lỗi gồm: validation thất bại quay lại cấu hình; run đang chạy có thể cancel; task lỗi dẫn tới partial result/retry; không đủ dữ liệu dẫn tới cảnh báo và không sinh khuyến nghị chắc chắn. Wireframe được quản lý tại [`docs/wireframes/`](wireframes/README.md), flow artifact tại [`docs/flows/`](flows/README.md).

## 17. Trạng thái của benchmark run

| Trạng thái | Ý nghĩa | Chuyển tiếp hợp lệ chính |
| --- | --- | --- |
| `DRAFT` | Cấu hình còn chỉnh sửa, chưa xác nhận | `VALIDATING`, `CANCELLED` |
| `VALIDATING` | Kiểm tra cấu hình, quyền, tài nguyên và estimate | `DRAFT` nếu lỗi/cần sửa; `QUEUED` khi hợp lệ và được xác nhận; `CANCELLED` |
| `QUEUED` | Snapshot đã đóng băng, chờ worker | `RUNNING`, `CANCELLED`, `FAILED` nếu không thể khởi tạo an toàn |
| `RUNNING` | Có ít nhất một task đang thực thi/được xử lý | `COMPLETED`, `PARTIALLY_COMPLETED`, `FAILED`, `CANCELLED` |
| `PARTIALLY_COMPLETED` | Có kết quả hợp lệ nhưng một phần phạm vi thất bại/không hoàn thành | trạng thái kết thúc; retry tạo attempt/hoặc run liên quan theo thiết kế được duyệt |
| `COMPLETED` | Toàn bộ phạm vi bắt buộc hoàn tất theo điều kiện đã duyệt | trạng thái kết thúc |
| `FAILED` | Không đạt điều kiện tối thiểu để tạo kết quả sử dụng được | trạng thái kết thúc; retry/reproduction tạo lịch sử mới phù hợp |
| `CANCELLED` | Yêu cầu hủy được chấp nhận; kết quả dở dang không được coi là hoàn tất | trạng thái kết thúc |

Quy tắc chuyển trạng thái:

- Chỉ cấu hình hợp lệ và được người có quyền xác nhận mới vào `QUEUED`.
- Mỗi chuyển trạng thái ghi timestamp, actor/system reason và audit event.
- Terminal state không bị ghi đè. Retry/reproduction giữ lịch sử và tạo attempt/run mới theo định danh rõ ràng.
- Cancel là best-effort với task đã gọi provider; output đến sau phải được đánh dấu và không làm run thành `COMPLETED`.
- Điều kiện định lượng phân biệt `COMPLETED`, `PARTIALLY_COMPLETED`, `FAILED` đang `TBD`, phải xét coverage, metric bắt buộc và tỷ lệ lỗi; Product Owner và Kỹ sư Eval phê duyệt trước test plan.

## 18. Tiêu chí nghiệm thu MVP

1. Web được deploy ở môi trường demo và hai tài khoản/vai trò có thể đăng nhập với quyền server-side khác nhau.
2. Kỹ sư Eval hoàn tất luồng cấu hình → validation/estimate → chạy → theo dõi → kết quả → báo cáo trên ít nhất 3 model đã được đội chốt.
3. Hệ thống đo và hiển thị ít nhất 4 loại metric có định nghĩa/test case đã duyệt, đồng thời giải quyết rõ cách đếm latency và cost.
4. Kết quả chi tiết, leaderboard và báo cáo thể hiện quality, latency, cost, error/missing coverage; partial run không bị hiểu là hoàn tất.
5. Khuyến nghị nêu căn cứ truy về run/result, đánh đổi và giới hạn, đồng thời yêu cầu human review.
6. Cấu hình và metadata bắt buộc được lưu; một run có thể được tạo lại mà không sửa lịch sử gốc.
7. Retry, cancel và ít nhất một kịch bản partial failure vượt qua acceptance test.
8. Secret không xuất hiện trong repository/log/report; dữ liệu test nhạy cảm bị chặn đối với vai trò không có quyền.
9. Audit log ghi các hành động tối thiểu theo FR-14.
10. Các ngưỡng hiệu năng, ngân sách và policy còn `TBD` phải được chốt trước khi test tương ứng; không dùng roadmap làm điều kiện nghiệm thu.

## 19. Analytics và chỉ số vận hành

Thu thập ở mức không lộ nội dung nhạy cảm:

- Số run theo trạng thái và thời gian.
- Thời gian ở từng trạng thái; queue time và execution time.
- Số task/sample thành công, lỗi, retry và bị hủy.
- Coverage của metric và tỷ lệ missing/error theo evaluator/model.
- Token usage, estimated cost, actual/reported cost và độ lệch khi dữ liệu có sẵn.
- Tần suất xem/export báo cáo và tạo reproduction run.
- Tỷ lệ khuyến nghị được human review/chấp nhận nếu workflow này được đội chốt.

Dashboard, retention, ngưỡng cảnh báo và KPI mục tiêu đang `TBD`. Analytics không được thu raw prompt/output nếu chưa có phê duyệt dữ liệu.

## 20. Rủi ro và biện pháp giảm thiểu

| Rủi ro | Tác động | Biện pháp giảm thiểu MVP |
| --- | --- | --- |
| Dataset không đại diện | Khuyến nghị sai bối cảnh | Version/provenance, công khai phạm vi, human review |
| LLM-as-judge thiên lệch/không ổn định | Điểm khó tin cậy | Version rubric/judge, human sampling, không dùng nếu chưa duyệt |
| Provider/model thay đổi | Khó tái lập | Lưu identifier, thời điểm, tham số và cảnh báo khác biệt |
| Công thức tổng hợp che lấp đánh đổi | Hạng gây hiểu nhầm | Hiển thị từng chiều và công thức/profile; không mặc định bí mật |
| Dữ liệu thiếu được hiểu là 0 | Sai thứ hạng | Trạng thái missing/error riêng, coverage rõ ràng |
| Vượt chi phí | Ảnh hưởng ngân sách | Estimate, xác nhận, usage tracking; limit cụ thể `TBD` |
| Lộ API key/dữ liệu test | Sự cố bảo mật | Secret manager, RBAC, redaction, data classification |
| Retry tạo trùng kết quả | Sai tổng hợp/chi phí | Idempotency key, attempt history, test restart/retry |
| Phạm vi roadmap tràn vào MVP | Trễ tiến độ | Tách backlog, không đưa roadmap vào acceptance criteria |

## 21. Giả định và phụ thuộc

- Nhóm sẽ cung cấp quyền hợp lệ tới ít nhất 3 model/provider sau khi D-01 được chốt.
- Dataset đầu tiên có quyền sử dụng và quy tắc xử lý dữ liệu rõ ràng trước khi upload.
- Provider cung cấp API/usage metadata ở mức đủ dùng; phần thiếu phải được hiển thị, không suy đoán.
- Product Owner, Kỹ sư Eval, Quản lý, Tech Lead và Data Owner/Security có thể là các vai trò do thành viên nhóm kiêm nhiệm; người cụ thể đang `TBD`.
- Wireframe, kiến trúc và test plan phụ thuộc các câu hỏi mở quan trọng được khóa.
- Tech stack và môi trường deploy chưa được chọn; PRD không ngầm phê duyệt stack gợi ý nào.

## 22. Roadmap ngoài MVP

- Lập lịch benchmark định kỳ và lịch sử xu hướng dài hạn.
- Regression detection và held-out regression gate.
- Human review workflow để hiệu chỉnh LLM-as-judge.
- Cảnh báo chi phí và budget anomaly.
- Pareto frontier Cost × Latency × Quality.
- Trace analysis và hỗ trợ khoanh vùng lỗi.
- Prompt injection testing và safety regression.

Các mục trên cần PRD/ADR và tiêu chí riêng; không phải cam kết MVP.

## 23. Bảng câu hỏi mở trước PRD v1.0

| ID | Câu hỏi | Tại sao cần chốt | Người chịu trách nhiệm | Deadline | Trạng thái |
| --- | --- | --- | --- | --- | --- |
| OQ-01 | Ba model demo là gì, dùng version/endpoint nào? | Khóa tích hợp, quyền truy cập và phép so sánh | Product Owner + Kỹ sư Eval | Trước thiết kế tích hợp | TBD |
| OQ-02 | Dataset/tác vụ đầu tiên là gì và version hóa ra sao? | Khóa schema, quyền dữ liệu và phương pháp chấm | Product Owner + Kỹ sư Eval | Trước wireframe cấu hình | TBD |
| OQ-03 | Bốn metric nghiệm thu được định nghĩa chính xác thế nào? | Tránh đếm mơ hồ và tạo test case | Kỹ sư Eval + Product Owner | Trước test plan | TBD |
| OQ-04 | Có dùng LLM-as-judge trong MVP không? | Ảnh hưởng chi phí, độ tin cậy và provenance | Kỹ sư Eval + Quản lý | Trước thiết kế evaluator | TBD |
| OQ-05 | Human sampling bao nhiêu và agreement được đo thế nào? | Kiểm chứng model-based evaluator | Kỹ sư Eval | Trước nghiệm thu metric | TBD |
| OQ-06 | Công thức xếp hạng, normalization và tie-breaker là gì? | Bảo đảm leaderboard minh bạch | Product Owner + Quản lý | Trước wireframe leaderboard | TBD |
| OQ-07 | Có các profile `balanced`, `quality-first`, `cost-first` không? | Xác định cách biểu diễn ưu tiên và khuyến nghị | Product Owner + Quản lý | Trước thiết kế khuyến nghị | TBD |
| OQ-08 | Báo cáo export Markdown, PDF hay cả hai? | Khóa phạm vi FR-11 và công cụ export | Product Owner | Trước lập backlog export | TBD |
| OQ-09 | Quản lý chỉ đọc hay có quyền duyệt/chạy/hủy? | Khóa RBAC, UI và audit | Product Owner + Quản lý | Trước thiết kế auth | TBD |
| OQ-10 | Ngân sách một run và toàn bộ demo là bao nhiêu? | Xác định guardrail và kịch bản nghiệm thu cost | Quản lý | Trước tích hợp provider | TBD |
| OQ-11 | Dữ liệu nào được xem là nhạy cảm và retention bao lâu? | Khóa authorization, logging, export và xóa dữ liệu | Data Owner/Security | Trước nhập dataset | TBD |
| OQ-12 | Điều kiện định lượng cho hoàn tất, hoàn tất một phần và thất bại? | Khóa state machine và aggregation | Kỹ sư Eval + Product Owner | Trước test state machine | TBD |
| OQ-13 | Tech stack cuối cùng có khóa theo stack gợi ý hay không? | Khóa kiến trúc, dependency, deploy và kỹ năng đội | Tech Lead | Trước bắt đầu code | TBD |
| OQ-14 | Latency và cost là một nhóm vận hành hay hai metric riêng khi nghiệm thu? | Xác nhận yêu cầu “ít nhất 4 metric” được đếm nhất quán | Kỹ sư Eval + Product Owner | Trước nghiệm thu metric | TBD |
| OQ-15 | Ngưỡng hiệu năng, concurrency và quy mô dataset demo là gì? | Tạo NFR test có ý nghĩa mà không bịa số | Tech Lead + Product Owner | Trước performance test | TBD |
