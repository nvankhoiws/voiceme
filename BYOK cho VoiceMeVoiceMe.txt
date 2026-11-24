**BYOK** (Bring Your Own Key) nghĩa là app không dùng API key của anh. Mỗi người dùng tự tạo tài khoản Soniox và Anthropic, tự nạp tiền, rồi dán key của họ vào app. Tiền API họ trả thẳng cho Soniox và Anthropic. Anh chỉ thu tiền cho phần mềm của mình.

## Anh hiểu đúng phần lớn

Bán license app và mở khóa tính năng trên nền BYOK là mô hình hợp lệ và khá phổ biến. TypingMind và BoltAI làm đúng như vậy với key OpenAI và Anthropic. Mô hình này đứng được về mặt điều khoản vì:

- ToS của Soniox cấm sublicense, resell, lease, rent, provide service bureau access to, or redistribute the Services themselves. Chữ "themselves" là điểm quan trọng: anh bán phần mềm client của anh, không bán dịch vụ Soniox. Người dùng tự có hợp đồng trực tiếp với Soniox.
- Quyền sử dụng được cấp cho internal business or development purposes. Người dùng cuối dùng key của chính họ cho công việc của họ thì nằm trong phạm vi này.
- Có một điểm cần đính chính so với báo cáo: điều khoản chống sao chép (Section 6) chỉ nói về việc reverse engineer, extract, approximate, copy, imitate, or replicate Soniox models, systems, không nói về tính năng của app. Làm một app có tính năng tương tự Soniox App (dictation, dịch, ghi họp) không vi phạm điều này.

Vậy về mặt điều khoản hiện tại, anh **không bắt buộc phải xin phép Soniox**. Tuy vậy, cần thiết kế đúng ở mấy điểm sau.

## Những điểm dễ vi phạm khi làm

1. **Tuyệt đối không nhúng key của anh vào app**, kể cả để cho dùng thử miễn phí. ToS ghi rõ You may not disclose API keys publicly or embed secret API keys in client-side code. Nếu cho dùng thử bằng key của anh thì anh đang bán lại dịch vụ Soniox, tức là rơi vào đúng điều bị cấm. Nếu sau này muốn có trial, phải đi qua backend cấp temporary key và xin Soniox đồng ý bằng văn bản.
2. **Không chuyển key hay audio của người dùng qua server của anh.** Key và audio nên đi thẳng từ máy người dùng tới Soniox. Nếu đi qua server, anh trở thành bên xử lý dữ liệu và kéo theo trách nhiệm pháp lý về dữ liệu cá nhân.
3. **Trách nhiệm thuộc về chủ key.** ToS quy định You are responsible for all activity under your account, organization, projects, API keys, credentials, and users. EULA của anh nên ghi rõ: người dùng tự chịu trách nhiệm với tài khoản và chi phí API của họ, và phải tuân thủ ToS của Soniox và Anthropic.
4. **Thương hiệu.** any use of Soniox names, logos, trademarks, or quotes must comply with Soniox brand guidelines. Không đặt tên kiểu "Soniox Desktop for Linux". Chỉ nên ghi mô tả như "Works with Soniox API" và không dùng logo của họ.
5. **Không dùng transcript để train model.** ToS cấm dùng output to train, fine-tune, improve, distill, or develop any machine learning model.

## Rủi ro kinh doanh

Không xin phép thì vẫn hợp lệ, nhưng anh đang cạnh tranh trực tiếp với Soniox App, sản phẩm có thu phí Pro của chính họ. Họ có thể sửa ToS bất cứ lúc nào: ToS hiện tại có ghi Last updated: June 29, 2026, nghĩa là văn bản này có được cập nhật. Vì vậy tôi vẫn khuyên hai việc:

- **Thiết kế app hỗ trợ nhiều nhà cung cấp STT**, ví dụ Soniox là mặc định và thêm một provider dự phòng. Như vậy anh không bị phụ thuộc vào một nhà cung cấp.
- **Gửi một email ngắn cho Soniox.** Mục đích không phải xin phép, mà để biết họ có chương trình partner hoặc referral hay không. Họ có thể còn trả hoa hồng cho mỗi tài khoản Console mà app của anh mang về.

Với Anthropic, BYOK cũng là cách làm phổ biến, nhưng tôi chưa kiểm tra lại Commercial Terms hiện hành của họ. Nên rà lại trước khi bán. Tôi không phải luật sư. Nếu doanh thu đáng kể, nên nhờ luật sư xem EULA và chính sách dữ liệu, nhất là phần ghi âm người khác trong cuộc họp: app nên có thông báo hoặc xin đồng ý ghi âm.

## Gợi ý chia gói Free / Pro

- **Free:** dictation bằng hotkey và live transcript cơ bản.
- **Pro (license một lần hoặc theo năm):** overlay dịch hai chiều, tóm tắt và action items bằng Claude, template presales, export SRT/DOCX, tìm kiếm lịch sử, đồng hồ chi phí. Phần kiểm tra license có thể dùng Lemon Squeezy, Paddle hoặc Keygen.
