# AI Log — Khởi tạo tài liệu G1

- **Date:** 2026-11-15 (Asia/Saigon)
- **Author:** Nhóm AIP-03 (cần ghi nhận reviewer cụ thể khi review)
- **AI tool/model:** OpenAI Codex (model cụ thể theo phiên làm việc)
- **Task:** Khởi tạo cấu trúc repository và bộ tài liệu Product Brief/PRD v0.1 cho giai đoạn G1.
- **Prompt summary:** Tạo tài liệu tiếng Việt và cây thư mục ban đầu cho AIP-03, bám phạm vi MVP đã cung cấp, tách roadmap và để các quyết định chưa có dữ liệu ở trạng thái `TBD`.
- **Files changed:**
  - `README.md`, `.gitignore`, `.env.example`, `AGENTS.md`
  - `docs/brief.md`, `docs/prd.md` và README trong các khu vực tài liệu
  - README mô tả mục đích cho `apps/`, `workers/`, `packages/`, `infra/`, `tests/`
  - `ai-log/README.md`, `ai-log/TEMPLATE.md`, `ai-log/2026-11-15-g1-initialization.md`
- **AI contribution:** Hỗ trợ xây dựng Product Brief; chuẩn hóa đầu mục và acceptance criteria của PRD; đề xuất cây thư mục repository; rà soát ranh giới giữa MVP và roadmap.
- **Human verification:** Nội dung được kiểm tra tự động về cấu trúc, link, placeholder và định dạng trong phiên khởi tạo. Nhóm 4 người vẫn cần review tính đúng đắn sản phẩm, phân quyền, tiêu chí metric, security/privacy và các câu hỏi mở trước khi phê duyệt.
- **Decisions:** Chỉ ghi nhận phạm vi MVP và nguyên tắc đã được cung cấp. AI chưa quyết định model, dataset, metric chi tiết, công thức xếp hạng, ngân sách hoặc tech stack cuối cùng; các nội dung này vẫn là `TBD`.
- **Risks/limitations:** PRD v0.1 chưa có xác nhận của từng người chịu trách nhiệm; các ngưỡng định lượng và chính sách dữ liệu chưa được khóa. Không có benchmark hoặc dữ liệu vận hành thật được tạo trong bước này.
- **Follow-up:** Nhóm 4 thành viên review Brief/PRD, gán tên owner và deadline cụ thể cho câu hỏi mở, sau đó mới đổi trạng thái tài liệu nếu có bằng chứng phê duyệt.
