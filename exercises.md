# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 14h00–18h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.7, 1.2 và 1.8 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Hà Nội."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi? Ở mức nào phản hồi bắt đầu
kém mạch lạc?** (2–3 câu)
> Temperature 0: phản hồi ngắn gọn, thường là những điều hiển nhiên, phổ biến. Khi tăng temperature lên thì độ sáng tạo và đa dạng tăng lên, 0.7 có lẽ là mức tốt nhất còn cao hơn thì hơi chi tiết và lan man quá.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho trợ lý soạn thảo hợp đồng pháp lý,
và bao nhiêu cho trợ lý viết slogan quảng cáo? Giải thích khác biệt.**
> Trợ lý soạn thảo hợp đồng pháp lý: đặt temperature thấp(0-0.2) vì cần sự chính xác, chắc chắn. Còn viết slogan quảng cáo thì cần sáng tạo và đa dạng nhưng cũng không được quá lan man dài dòng nên để 0.8 

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 20.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 2 lần,
mỗi lần trung bình ~500 token đầu ra.

**Ước tính chi phí mỗi ngày của model lớn so với model nhỏ cho workload này
(dựa trên bảng giá trong template). Nêu một trường hợp model lớn xứng đáng
với chi phí và một trường hợp model nhỏ là lựa chọn đúng:**
> Workload tạo ra `20.000.000` token đầu ra mỗi ngày. Theo bảng giá trong template, Gemini 2.5 Flash tốn khoảng `20.000.000 / 1.000 × 0,0025 = 50 USD/ngày`, còn Flash-Lite khoảng `8 USD/ngày`, model lớn đắt hơn 6,25 lần. Model lớn đáng dùng cho yêu cầu cần suy luận phức tạp hoặc tổng hợp tài liệu quan trọng,... còn model nhỏ phù hợp với FAQ, phân loại, trả lời ngắn ở lưu lượng lớn...

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích máy học (machine learning) là gì?"** nhưng hai system prompt
khác nhau:
- "Bạn là một nhà thơ, trả lời mọi thứ bằng hình ảnh ví von, tránh thuật ngữ."
- "Bạn là kỹ sư phần mềm senior, trả lời chính xác, có ví dụ code khi phù hợp."

**Hai phản hồi khác nhau như thế nào (giọng văn, độ dài, mức kỹ thuật)?
Từ đó rút ra system prompt điều khiển được những khía cạnh nào của phản hồi?**
(3–4 câu)
> Phản hồi theo vai nhà thơ thường văn vẻ, dễ hiểu hơn còn kỹ sư phần mềm sẽ dùng nhiều từ chuyên môn, code, dài hơn vì hiểu rõ hơn. Từ đó có thể thấy system prompt điều khiển được cách phản hồi của mô hình, từ độ dài, cách nói,...

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~150 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Nếu dùng ước lượng thô để dự
toán ngân sách API cho ứng dụng tiếng Việt, bạn sẽ dự toán thiếu hay thừa —
và vì sao?**
> Với đoạn văn tiếng Việt 155 từ, `count_tokens` theo cấu hình Gemini hiện tại trả về 178 token, còn ước lượng `số từ / 0,75` là khoảng 206,67 token. Hai kết quả lệch khoảng 13,9%. Vậy là dự toán thừa.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Xét ba ứng dụng: (a) chatbot văn bản, (b) trợ lý giọng nói đọc to phản hồi,
(c) pipeline dịch tài liệu chạy ngầm ban đêm. Ứng dụng nào hưởng lợi nhiều
nhất từ streaming, ứng dụng nào không cần — và tại sao?** (1 đoạn văn)
> Chatbot văn bản hưởng lợi từ streaming vì cần phản hồi ngay. Trợ lý giọng cần streaming để bắt đầu đọc sớm. Pipeline dịch tài liệu chạy ngầm ban đêm gần như không cần streaming vì không có người chờ trực tiếp. (b) cần nhất vì phản hồi cần đọc to ngay lúc đó chứ để có các phản hồi khác rồi mới đọc phản hồi trước thì không có tác dụng.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**Khi API quá tải và hàng nghìn client cùng retry, exponential backoff giúp
gì so với delay cố định? Tra cứu thêm: kỹ thuật "jitter" (thêm độ trễ ngẫu
nhiên) giải quyết vấn đề gì còn sót lại?**
> Exponential backoff giúp giảm tải cho máy chủ bằng cách tăng dần thời gian chờ sau mỗi lần retry, số lượng request được phân tán theo thời gian, giảm nguy cơ máy chủ tiếp tục bị quá tải. Kỹ thuật jitter khắc phục vấn đề nếu nhiều client cùng dùng công thức backoff thì vẫn có thể xảy ra retry đồng thời.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Viết lại system prompt bạn dùng cho trợ lý của mình. Chỉ ra 2 chỗ trong
prompt mà nếu xóa đi, hành vi trợ lý sẽ thay đổi rõ rệt — và mô tả thay đổi
đó:**
> System prompt của tôi là: “Bạn là trợ giảng AI thân thiện. Hãy trả lời ngắn gọn bằng tiếng Việt, giải thích theo từng bước và đưa ví dụ Python khi phù hợp. Nếu không chắc chắn, hãy nói rõ giới hạn thay vì tự tạo dữ kiện.” Nếu xóa cụm “bằng tiếng Việt”, trợ lý có thể chuyển sang tiếng Anh theo ngữ cảnh hoặc tài liệu kỹ thuật. Nếu xóa câu “Nếu không chắc chắn...”, trợ lý dễ trả lời đầy tự tin ngay cả khi thiếu dữ kiện, làm tăng nguy cơ thông tin sai.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn giữ history 4 lượt cuối. Hãy mô tả một tình huống hội thoại
cụ thể mà giới hạn này khiến trợ lý trả lời sai/mất ngữ cảnh, và đề xuất một
cách khắc phục (ví dụ: tóm tắt các lượt cũ, tăng giới hạn có chọn lọc...):**
> Ở lượt đầu tôi nói dự án đang dùng Gemini qua endpoint tương thích OpenAI và yêu cầu không đổi chữ ký hàm, sau hơn bốn lượt trao đổi về các lỗi khác, tôi nói “hãy sửa lại hàm lúc đầu”. Vì hai ràng buộc ban đầu đã bị cắt khỏi history, trợ lý có thể dùng sai provider hoặc đổi chữ ký hàm. Cách khắc phục là duy trì một bản tóm tắt chứa các quyết định và ràng buộc quan trọng, đặt nó trước phần history gần nhất.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên GitHub cá nhân và nộp link repo vào vlearn (theo hướng dẫn README)
