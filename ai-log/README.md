# AI Log

## Mục đích

AI Log ghi lại việc sử dụng công cụ AI tạo sinh trong quá trình phân tích, viết tài liệu, thiết kế và phát triển AIP-03. Nhật ký giúp nhóm review nguồn gốc thay đổi, biết phần nào do AI hỗ trợ và phần nào đã được con người kiểm tra; đây không phải nơi lưu transcript đầy đủ hay dữ liệu nhạy cảm.

## Khi nào phải ghi log

Tạo hoặc cập nhật một entry khi AI tạo thay đổi đáng kể, ví dụ:

- Soạn hoặc tái cấu trúc tài liệu sản phẩm/kỹ thuật.
- Đề xuất quyết định thiết kế, kiến trúc hoặc phạm vi ảnh hưởng backlog.
- Tạo hoặc sửa code, test, cấu hình hay migration đáng kể.
- Phân tích lỗi dẫn đến một thay đổi được đưa vào repository.

Không cần entry riêng cho sửa chính tả nhỏ nếu không thay đổi ý nghĩa.

## Nội dung bắt buộc

- Ngày giờ và múi giờ.
- Người thực hiện/chịu trách nhiệm review.
- Công cụ và model AI, nếu biết.
- Prompt hoặc tóm tắt prompt đủ để hiểu nhiệm vụ, không chứa dữ liệu cấm.
- Danh sách file thay đổi.
- Phần đóng góp của AI và quyết định được AI hỗ trợ.
- Phần con người đã kiểm tra, chỉnh sửa hoặc cần kiểm tra tiếp.
- Rủi ro, giả định và giới hạn.
- Việc cần theo dõi tiếp.

## Quy tắc an toàn và trách nhiệm

- Không ghi secret, API key, access token, credential hoặc dữ liệu test nhạy cảm vào AI Log hay prompt summary.
- Có thể tóm tắt prompt thay vì chép nguyên văn để tránh đưa dữ liệu không cần thiết vào repository.
- AI output không tự động được coi là quyết định cuối cùng, bằng chứng kiểm thử hoặc phê duyệt của nhóm.
- Tác giả entry và reviewer con người chịu trách nhiệm kiểm tra tính đúng đắn trước khi đổi trạng thái tài liệu/code thành approved.
- Dùng [`TEMPLATE.md`](TEMPLATE.md) cho entry mới.
