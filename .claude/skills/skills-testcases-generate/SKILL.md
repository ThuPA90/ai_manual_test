---
name: skills-testcases-generate
description: Sinh bộ Test Case Manual đầy đủ (Happy path, Negative case, Edge case) từ mô tả một chức năng. Dùng khi cần viết Test Case nhanh cho một chức năng cụ thể, có thể dùng ngay sau Skill skills-requirements-analyzer.
argument-hint: [mô tả chức năng cần test]
---

Đóng vai Senior Manual QA Engineer. Sinh bộ Test Case Manual cho chức năng sau: $ARGUMENTS

Nếu trong hội thoại đã có kết quả phân tích luồng (Happy/Alternate/Exception Path) từ Skill skills-requirements-analyzer, hãy dùng chính kết quả đó làm nền — không phân tích lại từ đầu.

Yêu cầu:
- Bao gồm Happy path, Negative case, Edge case (tối thiểu 30% là Negative/Edge case).
- Mỗi Test Case có: Tiền điều kiện, Các bước thực hiện, Dữ liệu test, Kết quả mong đợi, Độ ưu tiên (High/Medium/Low).
- Mã Test Case theo định dạng: TC_<MODULE>_<số thứ tự 3 chữ số>.
- Trình bày dạng bảng Markdown.
- Viết toàn bộ bằng tiếng Việt.