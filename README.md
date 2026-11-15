# AIP-03 — AI Benchmark Platform

Nền tảng web hỗ trợ Kỹ sư Eval thiết lập và chạy benchmark LLM, đồng thời giúp Quản lý so sánh có căn cứ giữa chất lượng, độ trễ và chi phí để lựa chọn model phù hợp theo từng nhu cầu. Hệ thống hướng tới kết quả có thể truy vết và tái lập, không tuyên bố một model là tốt nhất cho mọi trường hợp.

> **Trạng thái:** G1 — Chốt bài toán và thiết kế. Repository hiện chỉ chứa tài liệu và cấu trúc khởi tạo; chưa có ứng dụng có thể chạy.

## Phạm vi MVP đã chốt

- Web deploy với đăng nhập và phân quyền tối thiểu cho **Kỹ sư Eval** và **Quản lý**.
- Benchmark ít nhất 3 model bằng ít nhất 4 loại metric.
- Lưu cấu hình, phiên bản dữ liệu, metadata thực thi và kết quả benchmark để kiểm tra, tái lập.
- Hiển thị kết quả chi tiết và leaderboard.
- Sinh báo cáo cùng khuyến nghị lựa chọn model có giải thích và căn cứ truy ngược được.
- So sánh theo đánh đổi **Quality × Latency × Cost**.
- Bảo vệ API key và dữ liệu test nhạy cảm.

Các khả năng nâng cao như chạy định kỳ, regression gate, cảnh báo chi phí, Pareto frontier, trace analysis và safety regression thuộc roadmap sau MVP, không phải cam kết nghiệm thu MVP.

## Vai trò người dùng

- **Kỹ sư Eval:** quản lý cấu hình đánh giá, tạo và theo dõi benchmark run, phân tích kết quả, tái lập run và xuất báo cáo.
- **Quản lý:** xem so sánh, leaderboard, khuyến nghị và báo cáo; quyền duyệt hoặc chạy benchmark vẫn là `TBD`.

## Deliverable G1

- [Product Brief](docs/brief.md)
- [PRD v0.1](docs/prd.md)
- [Wireframe/UI Flow](docs/wireframes/README.md)
- GitHub repository và cấu trúc ban đầu
- [AI Log](ai-log/README.md)

## Cấu trúc repository

```text
.
├── apps/                 # Khu vực frontend và backend dự kiến
├── workers/benchmark/    # Batch worker và queue consumer dự kiến
├── packages/evaluation/  # Hợp đồng metric/evaluator dự kiến
├── docs/                 # Brief, PRD, wireframe, flow, kiến trúc
├── ai-log/               # Nhật ký sử dụng AI
├── infra/                # Cấu hình triển khai ở giai đoạn sau
└── tests/                # Kiểm thử ở giai đoạn sau
```

## Chưa được triển khai

Chưa có source code ứng dụng, dependency, cơ sở dữ liệu, hạ tầng deploy, tích hợp model provider, benchmark worker, bộ test hay kết quả benchmark thật. Tài liệu G1 là bản nháp để nhóm review, không phải bằng chứng hệ thống đã vận hành.

## Trạng thái quyết định

Các model demo, dataset/tác vụ đầu tiên, định nghĩa metric chi tiết, việc dùng LLM-as-judge, công thức xếp hạng, ngưỡng nghiệm thu, ngân sách và tech stack cuối cùng đều đang **TBD**. Danh sách đầy đủ và người chịu trách nhiệm được theo dõi trong [bảng câu hỏi mở của PRD](docs/prd.md#23-bảng-câu-hỏi-mở-trước-prd-v10).
