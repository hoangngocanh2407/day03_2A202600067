# Individual Report: Lab 3 - Chatbot vs ReAct Agent

- **Student Name**: Hoàng Ngọc Anh
- **Role**: Data/NLP Map Engineer
- **Date**: 2026-04-06

---

## I. Technical Contribution (15 Points)

Nhiệm vụ của em bảo vệ tính toàn vẹn của dữ liệu trong CSDL, thông qua việc chuẩn hoá ngữ nghĩa (NLP) trước khi lưu. Em thiết kế cơ chế mapping từ khóa phân mảnh thành các danh mục tiêu chuẩn.

- **Modules Implemented**: `src/tools/expense_tools.py` (`_normalize_category`)
- **Code Highlights**:
```python
STANDARD_CATEGORIES = {
    "Ăn uống": ["ăn", "uống", "food", "phở", "trà sữa", "cà phê"...],
    "Đi lại":  ["xăng", "xe", "grab", "gojek", "taxi"...],
}
def _normalize_category(raw_category: str) -> str:
    # Keyword mapping check...
```
- **Documentation**: Mọi từ khoá mà Agent tự sinh đều được đi qua cửa ải logic kiểm duyệt, để bẻ hướng về các danh mục cố định nhằm phục vụ biểu đồ vẽ UI.

---

## II. Debugging Case Study (10 Points)

- **Problem Description**: Thống kê danh mục UI bị phá vỡ vì sự xuất hiện của rất nhiều loại danh mục trùng hình thái ngữ nghĩa như: "Xăng", "Xăng xe", "Di chuyển bằng Grab", "Đi lại".
- **Diagnosis**: Dù Prompt quy định rõ là LLM phải truyền danh mục chuẩn, đôi khi LLM vẫn ngẫu nhiên điền nguyên chữ của người dùng (như "Xăng xe") vào tham số `category` của hàm. Bản tính ngẫu nhiên của Generation Models gây nhiễu loạn CSDL của một ứng dụng phần mềm truyền thống.
- **Solution**: Em không thèm tin tưởng LLM. Thiết kế một hàm nội bộ truyền thống `_normalize_category`, duyệt vòng lặp bắt keyword để ép cứng bất cứ category gì rác rưởi vào 7 danh mục tổng quan vững chãi.

---

## III. Personal Insights: Chatbot vs ReAct (10 Points)

1. **Reasoning**: Mô hình Agentic cho thấy rằng ta không nên giao khoán toàn bộ 100% việc kinh doanh cho LLM xử lý. LLM (Não) chỉ nên dịch ý định, còn Code truyền thống (Vi xử lý) nên kiểm soát định dạng rủi ro.
2. **Reliability**: Sự bướng bỉnh cứng đầu của LLM thường xuyên gây gián đoạn quy trình phân tích tự động. Agent đôi khi kém thông minh (deterministic) hơn hẳn những câu If-Else Regex thuần túy.
3. **Observation**: Khi hệ thống báo "Lưu thành công với category: Đi lại", Action đã đính chính sự ảo giác category bên trên của suy nghĩ Agent.

---

## IV. Future Improvements (5 Points)

- **Scalability**: Dùng thư viện NLP mạnh hơn như NLTK hoặc dùng Text Embeddings Cosine Similarity để so khoảng cách ngữ nghĩa giữa Category user nhắc đến và CSDL, giúp việc tự quy loại tốt hơn.
- **Safety**: Từ chối lưu những mục không định dạng được và yêu cầu Agent tự hỏi người dùng chọn danh mục (Human-in-the-loop mechanism).
- **Performance**: Caching: Các từ vựng lạ khi User nhắc đến được ánh xạ xong sẽ lưu vào cache Redis để LLM không phải tốn Token phân loại lại sau này.
