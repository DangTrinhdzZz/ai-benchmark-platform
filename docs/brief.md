# Product Brief — AIP-03 AI Benchmark Platform

## 1. Thông tin tài liệu

| Thuộc tính | Giá trị |
| --- | --- |
| Mã đề | AIP-03 |
| Phiên bản | 0.1 |
| Trạng thái | Draft for Team Review |
| Giai đoạn | G1 — Chốt bài toán và thiết kế |
| Nhóm thực hiện | 4 thành viên |
| Repository | `DangTrinhdzZz/ai-benchmark-platform` |

## 2. Bối cảnh

Đội phát triển cần chọn LLM phù hợp cho từng tác vụ nhưng kết quả thử nghiệm thường phân tán, cấu hình không đồng nhất và khó truy lại. Một model đạt chất lượng cao có thể không phù hợp khi độ trễ hoặc chi phí vượt giới hạn vận hành. So sánh thủ công cũng dễ bỏ sót phiên bản model, phiên bản dataset, prompt, tham số, lỗi từng mẫu và thay đổi giá của nhà cung cấp.

AIP-03 đặt nền móng cho một quy trình đánh giá có cấu trúc: cùng điều kiện đầu vào, cùng phương pháp đo, lưu đủ metadata và trình bày rõ sự đánh đổi giữa **Quality × Latency × Cost**.

## 3. Problem statement

Kỹ sư Eval chưa có một nơi thống nhất để cấu hình, chạy, theo dõi và tái lập benchmark trên nhiều model. Quản lý thiếu một báo cáo có căn cứ để so sánh phương án theo yêu cầu kinh doanh, thay vì chỉ nhìn một điểm tổng hợp hoặc tuyên bố model “tốt nhất”. Điều này làm quyết định khó kiểm chứng, tiềm ẩn chi phí ngoài dự kiến và khiến kết quả khó tái sử dụng.

## 4. Giải pháp đề xuất

Xây dựng web application cho phép người dùng được phân quyền:

1. Chọn các phiên bản model, dataset và metric trong một benchmark configuration.
2. Xác nhận cấu hình và ước tính chi phí trước khi chạy.
3. Thực thi theo batch, theo dõi trạng thái và xử lý lỗi một phần.
4. Thu thập output, điểm metric, latency, token usage, cost, lỗi và metadata cần thiết.
5. So sánh kết quả chi tiết trên leaderboard và theo các chiều chất lượng, độ trễ, chi phí.
6. Sinh báo cáo và khuyến nghị có giải thích; mỗi nhận định phải truy ngược được về cấu hình và kết quả đánh giá.

Hệ thống hỗ trợ ra quyết định, không tự động thay đổi model, prompt, routing hoặc safety rule trong production ở phạm vi MVP.

## 5. Người dùng mục tiêu

### Kỹ sư Eval

Cần tạo benchmark nhất quán, quan sát lỗi, kiểm tra từng kết quả, tái lập run và chuẩn bị bằng chứng kỹ thuật cho khuyến nghị.

### Quản lý

Cần nắm kết quả và đánh đổi giữa các phương án, hiểu vì sao có khuyến nghị, xem rủi ro/giới hạn và xuất báo cáo phục vụ quyết định. Phạm vi quyền duyệt hoặc khởi chạy benchmark đang `TBD`.

## 6. Phạm vi MVP

- Web deploy và authentication cho tối thiểu hai vai trò.
- Quản lý tham chiếu model/version và dataset/version dùng trong benchmark.
- Chọn ít nhất 3 model trong một kịch bản demo được đội xác nhận.
- Cấu hình và đo ít nhất 4 loại metric thuộc các nhóm dự kiến: task quality, faithfulness, format compliance, latency/cost.
- Tạo, xác nhận, ước tính chi phí, chạy và theo dõi benchmark theo batch.
- Hỗ trợ retry/cancel và biểu diễn partial failure ở mức tối thiểu.
- Lưu cấu hình, output, metric, latency, token usage, cost, lỗi và audit metadata.
- Hiển thị kết quả chi tiết và leaderboard, không che khuất dữ liệu thiếu hoặc lỗi.
- Sinh và export báo cáo có khuyến nghị lựa chọn model, căn cứ và giới hạn áp dụng.
- Cho phép truy lại lịch sử và tái lập benchmark từ cấu hình đã lưu.
- Bảo vệ API key và dữ liệu test nhạy cảm theo chính sách được đội phê duyệt.

## 7. Ngoài phạm vi MVP

- Lập lịch benchmark định kỳ và cảnh báo tự động.
- Phát hiện regression, held-out regression gate và chặn release.
- Human review workflow đầy đủ để hiệu chỉnh LLM-as-judge.
- Cảnh báo chi phí theo thời gian thực.
- Phân tích xu hướng benchmark dài hạn.
- Tự động tính và trực quan hóa Pareto frontier.
- Phân tích trace và tự động khoanh vùng nguyên nhân lỗi.
- Bộ kiểm thử prompt injection và safety regression chuyên sâu.
- Tự động thay model, prompt, routing hoặc safety rule trong production.

## 8. Nguyên tắc sản phẩm

- **Fair comparison:** các model trong cùng run phải dùng điều kiện đánh giá có thể so sánh; ngoại lệ phải được ghi rõ.
- **Reproducibility:** lưu phiên bản và snapshot/reference bất biến cần thiết để tái lập hợp lý.
- **Traceability:** báo cáo, khuyến nghị và điểm tổng hợp phải truy ngược tới run, mẫu dữ liệu, evaluator và cấu hình.
- **Human oversight:** AI hỗ trợ đánh giá và giải thích nhưng con người phê duyệt quyết định; kết quả LLM-as-judge phải được kiểm chứng nếu được dùng.
- **Cost control:** hiển thị ước tính trước run, ghi nhận chi phí thực tế theo dữ liệu khả dụng và không âm thầm vượt ngân sách đã duyệt.

## 9. Chỉ số thành công MVP

- Một người dùng được cấp quyền có thể hoàn tất luồng từ cấu hình đến báo cáo cho ít nhất 3 model.
- Kết quả hiển thị đủ ít nhất 4 loại metric đã được đội định nghĩa và phê duyệt trước nghiệm thu.
- Mỗi kết quả và khuyến nghị có thể truy ngược đến model version, dataset version, cấu hình evaluator và run liên quan.
- Một run đã hoàn tất có thể được tạo lại từ cấu hình lưu trữ; sai khác do provider hoặc yếu tố không quyết định phải được công khai.
- Secret không xuất hiện trong UI, log, báo cáo hoặc repository; quyền truy cập dữ liệu tuân theo vai trò.
- Người dùng phân biệt được run hoàn tất, hoàn tất một phần, thất bại và bị hủy.
- Ngưỡng định lượng về hiệu năng, độ tin cậy, human agreement và sai lệch chi phí đang `TBD`, do Product Owner và Tech Lead phê duyệt trước PRD v1.0.

## 10. Ràng buộc

- **Human-in-the-loop (HITL):** con người review khuyến nghị trước khi dùng cho quyết định production; MVP không tự áp dụng thay đổi.
- **Bảo mật dữ liệu test:** phân loại dữ liệu, giới hạn truy cập, che/redact dữ liệu trong log và định nghĩa retention trước khi nhập dữ liệu nhạy cảm.
- **Kiểm chứng LLM-as-judge:** nếu sử dụng, phải version prompt/judge model, công khai giới hạn và so sánh với human sampling theo kế hoạch được duyệt.
- **Kiểm soát chi phí:** phải ước tính trước chạy, ghi nhận usage/cost thực tế khi provider hỗ trợ và xử lý rõ khi sắp vượt giới hạn; ngân sách cụ thể đang `TBD`.
- **Biến động bên ngoài:** model endpoint, giá và hành vi provider có thể đổi; metadata phải ghi thời điểm và phiên bản/định danh khả dụng.

## 11. Roadmap sau MVP

1. Chạy định kỳ, lịch sử theo thời gian và regression detection.
2. Held-out regression gate tích hợp quy trình phát hành.
3. Human review workflow và hiệu chỉnh LLM-as-judge.
4. Cảnh báo ngân sách và cost anomaly.
5. Pareto frontier cho Quality × Latency × Cost.
6. Trace analysis, phân loại nguyên nhân lỗi và drill-down nâng cao.
7. Prompt injection testing và safety regression suite.

Thứ tự, nguồn lực và tiêu chí của roadmap sẽ được lập kế hoạch riêng; các mục này không thuộc nghiệm thu MVP.

## 12. Quyết định cần đội xác nhận

| ID | Quyết định | Trạng thái | Người cần xác nhận |
| --- | --- | --- | --- |
| D-01 | Ba model dùng trong demo | TBD | Product Owner, Kỹ sư Eval |
| D-02 | Dataset/tác vụ và chính sách versioning đầu tiên | TBD | Product Owner, Kỹ sư Eval |
| D-03 | Định nghĩa chính xác bốn metric nghiệm thu | TBD | Kỹ sư Eval, Product Owner |
| D-04 | Có dùng LLM-as-judge và kế hoạch human sampling | TBD | Kỹ sư Eval, Quản lý |
| D-05 | Công thức xếp hạng và các profile lựa chọn | TBD | Product Owner, Quản lý |
| D-06 | Ngân sách mỗi run và toàn bộ demo | TBD | Quản lý |
| D-07 | Quyền duyệt/chạy của vai trò Quản lý | TBD | Product Owner, Quản lý |
| D-08 | Định dạng export Markdown, PDF hoặc cả hai | TBD | Product Owner |
| D-09 | Phân loại dữ liệu nhạy cảm và retention | TBD | Security/Data Owner |
| D-10 | Tech stack, hosting và kiến trúc cuối cùng | TBD | Tech Lead |
