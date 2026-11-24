# Soniox Desktop App: tính năng, giá, kiến trúc và gợi ý cho bản clone đa nền tảng (cập nhật 24/11/2026)

Soniox Desktop App là ứng dụng "3 trong 1" chạy trên macOS và Windows (chưa có Linux), gồm Voice Typing (gõ bằng giọng nói vào mọi ứng dụng), Smart Scribe (ghi và chép lời cuộc họp, có AI tóm tắt) và Translator (dịch trực tiếp 60+ ngôn ngữ, một chiều hoặc hai chiều).\[1\] App dùng tài khoản Soniox App riêng, tính phí theo credit hoặc gói Pro. Người dùng không thể nhập API key của chính mình, và app hiện không hỗ trợ Linux. Đây là hai khoảng trống mà bản clone BYOK đa nền tảng có thể khai thác.

## TL;DR

- **Sản phẩm:** Voice Typing trên desktop (macOS và Windows) ra mắt ngày 15/01/2026. Bản desktop đầy đủ ngang tính năng với mobile ra ngày 12/02/2026, gồm Smart Scribe, Translator, Voice Typing và tab Settings. Giá: gói Free cho 10 credit/tuần dùng chung mọi nền tảng; gói Pro $19.99/tháng hoặc $120/năm (App Store của Soniox Inc liệt kê in-app purchase "Pro $19.99" và "Pro $119.99"). App không có BYOK và không có Linux.
- **Nền tảng API phía dưới rẻ và mạnh:** STT v6 (stt-rt-v6 / stt-async-v6) ra ngày 03/11/2026. Theo trang giá Soniox, STT là $0.12/giờ cho real-time (streaming) và $0.10/giờ cho async; trang Speech Translation ghi $0.18/giờ cho dịch real-time và $0.16/giờ cho dịch async. API có sẵn diarization, nhận diện ngôn ngữ, context/từ điển, manual finalization cho push-to-talk và dịch hai chiều. Tiếng Việt và tiếng Nhật đều được hỗ trợ, gồm cả cặp dịch Việt↔Nhật.
- **Khuyến nghị cho bản clone:** MVP nên gồm 4 trụ cột: dictation bằng hotkey, ghi họp từ mic + system audio, dịch overlay hai chiều, và Claude cho tóm tắt/action items. Nên dùng Tauri 2 + Rust và ưu tiên Linux (PipeWire) ngay từ đầu. Rủi ro lớn nhất là pháp lý: Điều khoản Soniox cấm "resell/service bureau" nếu không có thỏa thuận bằng văn bản, vì vậy mô hình BYOK cần được Soniox xác nhận trước khi thương mại hóa.

## Key Findings

1. **Dòng thời gian sản phẩm**
   - 15/01/2026: Soniox công bố "native desktop Voice Typing apps for macOS and Windows". Người dùng có thể đọc chính tả vào mọi ứng dụng và ô nhập liệu (docs, Slack, email, trình duyệt).\[2\]
   - 12/02/2026: blog "The end of English-first voice AI, now on desktop" ra mắt Soniox Desktop App, mô tả là "full parity with our mobile app", gồm Smart Scribe, Translator và Voice Typing trong "one app, one account, one workflow".\[3\]
   - Một nguồn thứ cấp (FreeVoiceReader) gắn đợt ra mắt này với bản cập nhật model v4.\[4\] Sau đó Soniox ra STT v5 (tháng 6/2026) và STT v6 (03/11/2026).\[5\]\[6\]
2. **Nền tảng:**
   - Trang tải chính thức chỉ có "Download for macOS and Windows". Mobile có trên iOS và Android.\[7\]\[8\] Web app (app.soniox.com) dùng để quản lý, chỉnh sửa và chia sẻ, nhưng không ghi âm được.\[9\]
   - Không tìm thấy bất kỳ tuyên bố nào về Linux.
   - Link tải công khai trỏ tới `cdn.crabnebula.app/.../dmg-x86_64` và `.../nsis-x86_64`.\[7\]\[8\] Chưa xác minh được có bản native Apple Silicon/ARM hay đây là universal build.
3. **Công nghệ (suy luận, chưa được Soniox xác nhận):** Bộ cài được phân phối qua CrabNebula Cloud, đối tác chính thức của Tauri. Bộ cài Windows là NSIS, đúng định dạng bundler của Tauri.\[10\]\[11\] Vì vậy nhiều khả năng app viết bằng Tauri (Rust + WebView), không phải Electron.
4. **Tài khoản tách biệt với API:**
   - FAQ desktop có câu hỏi "I have a Soniox Console account. Why can't I log in?".\[12\] Điều này cho thấy tài khoản App và tài khoản Console (API) là hai hệ thống riêng.
   - App không có chỗ nhập API key. Mọi gói "built on Soniox API" nhưng người dùng trả tiền qua credit hoặc Pro.\[7\]
5. **Mức độ trưởng thành:**
   - Help Center còn mâu thuẫn. Trang chủ Help Center vẫn ghi "You cannot access transcriptions or recordings in the Desktop App. It can't be used with the Mobile and Web apps." Trong khi đó, trang AI Actions của Smart Scribe desktop nói kết quả "can be accessed from the desktop app, web app, and mobile app".\[13\]\[14\]
   - Có thể trang chủ chưa được cập nhật sau đợt ra mắt tháng 2. Đây là dấu hiệu sản phẩm còn đang thay đổi nhanh.
6. **Đánh giá độc lập rất mỏng:**
   - G2 chưa có review nào cho Soniox.\[15\]
   - Phần lớn nội dung "review" trên mạng là bài tổng hợp hoặc SEO (FreeVoiceReader, Stork, PracticeAll), không phải trải nghiệm thực tế.
   - Không tìm thấy thread Reddit, Hacker News hay Product Hunt có thực chất về bản desktop.

## Details

### 1. Tổng quan và cài đặt

- **Cài đặt:** Theo Help Center, người dùng tải bộ cài miễn phí cho Windows/macOS từ soniox.com/soniox-app, rồi đi qua "quick onboarding. It will guide you through permissions setup and usage".\[1\]
- **Yêu cầu hệ thống:** Soniox không công bố công khai yêu cầu tối thiểu (phiên bản macOS/Windows) và không có changelog cho desktop app. Số phiên bản desktop mới nhất **chưa xác minh được**.
- **Cấu trúc app gồm 4 tab:**\[1\]
  - **Voice Typing:** xem tất cả dictation đã lưu cục bộ.\[1\]
  - **Smart Scribe:** ghi phiên dài và chép lời.\[1\]
  - **Translator:** ghi phiên và dịch real-time.\[1\]
  - **Settings.**

### 2. Danh sách tính năng chi tiết

**Voice Typing (dictation vào mọi app)**
- Gõ bằng giọng nói vào mọi ứng dụng, kích hoạt bằng phím tắt. FAQ có các mục "The shortcut isn't working" và "…after granting permissions", nghĩa là cần cấp quyền hệ điều hành, nhiều khả năng là Accessibility trên macOS.\[12\]
- Phím tắt mặc định và chế độ hold-to-talk hay toggle **chưa xác minh được**.
- Dictation được lưu cục bộ để copy/paste lại sau.\[13\]
- **Styles:** có các style định sẵn (ví dụ trang marketing nhắc tới formal/casual) và style tùy chỉnh. Có thể "restyle" một dictation đã lưu sang style khác.\[8\]\[13\] Bước này nhiều khả năng dùng LLM hậu xử lý.
- **Dictionary:** gồm hai phần.\[16\]
  - *Context*: đoạn mô tả môi trường làm việc, ví dụ "I'm an endocrinologist…".\[16\]
  - *Terms*: danh sách từ khó nhận dạng.\[16\]
- Help Center còn có các mục **Snippets**, **Dictation** và **Translation** cho Voice Typing (dịch khi đang đọc chính tả).\[16\] Chi tiết từng mục chưa đọc được.
- Gói Pro: "Unlimited voice typing and styling on desktop".\[17\]

**Smart Scribe (ghi họp, ghi chú)**
- Chép lời real-time cho cuộc họp, cuộc gọi, phỏng vấn, bài giảng. Có tách người nói (speaker detection) và hỗ trợ hội thoại trộn nhiều ngôn ngữ.\[18\]\[19\]
- Theo blog, có thể "capture meetings while working in other apps".\[3\]
- **AI Actions** sau khi lưu bản ghi:\[14\]
  - Summary
  - Key points
  - To-do list
  - Named entities
  - Speaker summary
  - Speaker quotes
  - **Custom** (người dùng tự nhập prompt)\[14\]
- Kết quả AI Actions được đồng bộ sang web và mobile.\[14\]
- Mobile có quy trình "refine": sau khi lưu, Soniox xử lý lại để có transcript chính xác hơn bản live.\[20\] Desktop nhiều khả năng giống vậy, vì FAQ có mục "I closed my app while my recording was uploading".\[12\]
- **Chưa xác minh:** Smart Scribe trên desktop có thu được system audio (âm thanh Zoom/Teams/Meet) hay chỉ thu microphone. Soniox không tuyên bố rõ. Câu "capture meetings while working in other apps" chưa đủ để kết luận.

**Translator**
- 60+ ngôn ngữ, "3,600 language pairs".\[21\] Dịch "word by word… without waiting for the end of a sentence".\[8\]\[22\]
- **Hai chế độ** (theo tài liệu mobile, nhiều khả năng desktop giống):
  - *Translate to*: dịch mọi lời nói sang một ngôn ngữ, hiển thị cả bản gốc lẫn bản dịch.\[23\]
  - *Translate between*: hai khung, mỗi khung cho một ngôn ngữ, dịch qua lại hai chiều; lời nói ở ngôn ngữ thứ ba hiện ở cả hai khung.\[23\]
- Cần internet để dịch real-time. Có thể ghi offline rồi xử lý khi có mạng lại.\[22\]

**Các năng lực khác**

| Hạng mục | Trạng thái trong Soniox App |
|---|---|
| Số ngôn ngữ | 60+ (có tiếng Việt, tiếng Nhật, Thái, Hàn, Trung…) |
| Tiếng Việt | Có, kể cả giọng Bắc, Trung, Nam theo trang API; dịch Việt↔Nhật real-time |
| Language identification / code-switching | Có (chuyển ngôn ngữ giữa câu) |
| Speaker diarization | Có |
| Custom vocabulary / context | Có (Dictionary cho Voice Typing; language hints và context trên mobile) |
| AI tóm tắt / LLM | Có (AI Actions + custom prompt); nhà cung cấp LLM không công bố |
| Offline | Ghi âm offline được; nhận dạng và dịch cần cloud |
| Lưu trữ | Dictation lưu cục bộ; "Audio recordings stay on your device unless you choose to sync them"\[24\] |
| Search lịch sử | Có trên web app ("search and filters"); trên desktop chưa xác minh\[9\] |
| Export | Web app có "Share or copy the transcript" và public link; **định dạng file (TXT/SRT/DOCX…) chưa xác minh**\[13\] |
| Subtitle/caption overlay nổi | **Không thấy tài liệu** về overlay luôn-nổi-trên-cùng cho desktop |
| TTS / speech-to-speech trong app | **Không thấy** trong app (dù Soniox đã có TTS API tts-rt-v2)\[25\] |

### 3. UI/UX và quy trình điển hình

- **Dictation:** đặt con trỏ vào ô nhập liệu, nhấn phím tắt, nói, rồi văn bản được chèn vào. Văn bản được áp style nếu có, và dictation được lưu ở tab Voice Typing.
- **Họp:** mở tab Smart Scribe, nhấn Start và theo dõi transcript live. Khi kết thúc thì lưu, app upload và refine bản ghi, sau đó người dùng chạy AI Actions và chia sẻ qua web app.
- **Dịch:** mở tab Translator, chọn "Translate to" hoặc "Translate between", chọn ngôn ngữ rồi nhấn Start.
- **Thiếu so với kỳ vọng của nhóm "meeting translator":** không có tài liệu về cửa sổ caption overlay trong suốt, luôn nổi trên cùng. Các dự án mã nguồn mở dùng Soniox như My Translator đã làm được tính năng này.\[26\]

### 4. Giá và tài khoản

**Soniox App**

| Gói | Giá | Nội dung |
|---|---|---|
| Free | $0 | 10 credit/tuần, dùng chung giữa mobile, web và desktop, không cộng dồn; mời bạn được +50 credit mỗi bên\[1\]\[27\] |
| Pro | $19.99/tháng hoặc $120/năm (≈$10/tháng; App Store ghi "Pro $119.99") | Unlimited Smart Scribe, Translator, Voice Typing; xử lý ưu tiên; truy cập sớm tính năng mới |
| Business | $30/người/tháng (nguồn thứ cấp PracticeAll) | Quản lý seat, workspace chung, data residency\[28\] |

- Soniox khẳng định Pro **không** có chất lượng nhận dạng tốt hơn: "All plans use the same high-accuracy models".\[7\]
- Quy đổi 1 credit ra bao nhiêu phút không được công bố. Đây là điểm **chưa xác minh**.

**Soniox API (để tính chi phí BYOK)**

| Dịch vụ | Giá công bố (≈/giờ audio) |
|---|---|
| STT real-time (stt-rt-v6) | $0.12 ($2.00/1M token audio vào, theo trang giá Soniox) |
| STT async (stt-async-v6) | $0.10 |
| Dịch STT real-time | $0.18 (trang Speech Translation) |
| Dịch STT async | $0.16 |
| TTS (tts-rt-v2) | ~$0.70/giờ audio tạo ra ($4.00/1M token text vào, $21.50/1M token audio ra)\[25\] |

- **Cách tính phí:** Billing theo token, không theo phút. Chính trang giá Soniox ghi "1 hour of audio is ~30,000 input audio tokens • 1 hour of speech is ~15,000 output text tokens".
- **Cảnh báo quan trọng cho dev:**
  - Một PR của dự án Omi (11/2026) cho rằng Soniox tính phí theo thời gian **mở kết nối, kể cả im lặng**. Họ đo được 3,2 giờ-kết-nối cho mỗi giờ audio thực, nên phải nâng chi phí nội bộ từ $0.0754 lên $0.33 cho mỗi giờ audio.\[29\]
  - README của một app dictation mã nguồn mở cũng ghi "Soniox bills the whole stream time, also silence".\[30\]
  - Đây là thông tin thứ cấp, nhưng hệ quả thiết kế rất rõ: **phải đóng WebSocket khi không nói và dùng VAD hoặc push-to-talk**.
- **Model lifecycle:** Theo trang Models chính thức của Soniox, stt-rt-v5 và stt-async-v5 sẽ bị gỡ ngày 15/12/2026; sau ngày đó, request dùng stt-rt-v5 tự động chuyển sang stt-rt-v6. Bản clone nên đọc tên model từ cấu hình, không hard-code.
- **Kinh tế BYOK:** Một người họp 40 giờ/tháng tốn khoảng $4.80 tiền STT, hoặc khoảng $7.20 nếu kèm dịch. Con số thực có thể cao hơn nếu giữ kết nối mở lâu. So với $19.99 của Soniox Pro, người dùng nặng vẫn có lợi đáng kể, chưa tính chi phí Claude.

### 5. Kiến trúc kỹ thuật

- **App chính thức:** nhiều khả năng là Tauri (suy luận từ CrabNebula + NSIS). Không có mã nguồn công khai và không có tài liệu kiến trúc.
- **Năng lực API mà bản clone có thể dùng:**
  - WebSocket real-time với token tạm thời (provisional) và token chốt (final).\[31\]
  - **Manual finalization**: Soniox mô tả là "a natural fit for push-to-talk and keyboard-shortcut workflows".\[31\]
  - Endpoint detection.
  - Language hints và language identification.
  - Speaker diarization.\[32\]
  - Context (từ điển, mô tả miền nghiệp vụ).
  - Khối `translation` trong cùng phiên, hỗ trợ one-way và two-way.\[33\]
  - Định dạng audio `pcm_s16le`, mulaw, alaw hoặc "auto".\[6\]
  - SDK Python và Node.\[34\]
  - STT v6 bổ sung "agent text input" và lọc tiếng vọng của agent (hữu ích cho voice agent, ít liên quan đến app ghi họp).\[35\]
- **Độ trễ:** Theo MarkTechPost (03/11/2026), Soniox chưa công bố số liệu độ chính xác hay độ trễ cho v6; thế hệ trước stt-rt-v5 đạt 1.34% semantic WER, median TTFS 260 ms và P95 305 ms. Một nguồn thứ cấp khác (Dograh) nêu stt-rt-v6 có median 266 ms trên benchmark Pipecat, nhưng chưa được Soniox xác nhận; nên tự đo trên audio thật.
- **Bảo mật và quyền riêng tư:**
  - Theo ToS ngày 29/06/2026: "Real-time API requests are processed transiently". Soniox không dùng Customer Content để train. Log vận hành không chứa audio hay transcript.\[36\]
  - Có data residency theo vùng (cần API key và endpoint của vùng tương ứng).\[24\]\[36\]
  - ToS cấm nhúng secret API key vào client-side "unless the applicable Soniox feature is specifically designed for that use".\[36\] Soniox có cơ chế **Temporary API keys** (giới hạn thời gian, single-use, có trường client_reference_id) cho client không tin cậy.\[37\]
- **Dự án tham khảo mã nguồn mở dùng Soniox (rất sát ý tưởng của user):**
  - **phuc-nt/my-translator** (tác giả người Việt):
    - Stack: Tauri 2 (Rust + WebView), ScreenCaptureKit cho system audio trên macOS, WASAPI cho Windows, cpal cho microphone.\[38\]
    - Tính năng: kết nối thẳng WebSocket tới Soniox, overlay luôn nổi trên cùng, độ trễ khoảng 2 giây, one-way và two-way, TTS tùy chọn (Edge TTS miễn phí, Google, ElevenLabs), BYOK.\[38\]\[39\]
    - Yêu cầu macOS 13+ hoặc Windows 10+. **Không có Linux.**\[38\]
  - **sokuji** (Electron, có tag linux-app) và **DeLive** (Electron, Windows/Linux): bằng chứng Linux khả thi với Electron.\[40\]
  - **VoiceX** (Tauri, đa nhà cung cấp STT) và **fatehiman/STT**: app tray trên Windows, hotkey mặc định Ctrl+Shift+Space, API key mã hóa bằng Windows DPAPI, gõ text bằng Unicode key events.\[30\] Đây là mẫu tốt cho phần dictation.
  - Một ghi chú từ dự án GestaltIRC: WebKitGTK trên Ubuntu không có WebRTC (`RTCPeerConnection` undefined).\[41\] Với Tauri trên Linux, **không nên** dựa vào Web Audio/WebRTC trong WebView để thu âm; hãy thu âm ở tầng Rust.

### 6. Đánh giá thực tế

- **Ưu điểm** (theo Soniox và các bài tổng hợp):
  - Một model đa ngôn ngữ, xử lý được việc chuyển ngôn ngữ giữa câu.
  - Dịch hai chiều thật sự.
  - Giá Pro thấp so với Otter/Fireflies.
  - Chính sách không train trên dữ liệu người dùng.
- **Nhược điểm và hạn chế:**
  - Không có Linux.
  - Không có BYOK.
  - Tài liệu desktop còn mâu thuẫn.
  - Không công khai định dạng export, changelog hay yêu cầu hệ thống.
  - Chưa rõ có thu system audio hay không.
  - Không có overlay caption.
  - Không có tích hợp lịch hay bot tự vào phòng họp như Otter/Fireflies.
  - Credit của gói Free không quy đổi minh bạch.
- **Độ chính xác tiếng Việt:** Soniox khẳng định "native-speaker accuracy" và xử lý được giọng Hà Nội, Huế, TP.HCM.\[21\]\[42\] **Không tìm thấy benchmark độc lập** cho tiếng Việt. User nên tự đo WER và CER trên bộ audio họp thật của công ty (có code-switching Anh–Việt và thuật ngữ IT) trước khi cam kết.

### 7. So sánh đối thủ

Giá của đối thủ lấy từ các trang so sánh bên thứ ba (tháng 10–11/2026), cần đối chiếu lại với trang giá chính thức của từng hãng.

| Sản phẩm | Trọng tâm | Nền tảng | Linux | Giá cá nhân (tham khảo) | Dịch live 2 chiều |
|---|---|---|---|---|---|
| **Soniox App** | Dictation + ghi họp + dịch | macOS, Windows, iOS, Android, Web | Không | Free 10 credit/tuần; Pro $19.99/tháng hoặc $120/năm | Có |
| Wispr Flow | Dictation | macOS, Windows, iOS, Android | Không | Free 2.000 từ/tuần trên Mac/Windows (1.000 từ/tuần trên iPhone, theo efficient.app); Pro $15/người/tháng ($12 nếu trả năm) | Không |
| Superwhisper | Dictation (có model local) | Mac, Windows, iOS | Chưa (một nguồn nói "coming soon", chưa xác minh) | Pro $8.49/tháng, $84.99/năm, $249.99 trọn đời | Không\[43\] |
| MacWhisper | Chép lời file/dictation, chạy local | Chỉ macOS | Không | Mua một lần (giá chưa xác minh) | Không |
| Otter.ai | Ghi họp + bot | Web, iOS, Android, Mac | Không | Free 300 phút/tháng; Pro $16.99/tháng ($8.33 nếu trả năm), 1.200 phút | Không\[44\]\[45\] |
| Granola | Ghi chú họp không cần bot | Mac, Windows (chưa xác minh) | Không | Basic $0; Business $14/người/tháng; các nguồn mâu thuẫn về gói cá nhân | Không\[46\]\[47\] |
| Krisp | Lọc ồn + ghi họp | Mac, Windows, iOS, Android | Không | Core $16/tháng ($8 nếu trả năm) | Hạn chế/chưa xác minh\[48\]\[49\] |
| Notta | Ghi họp + chép lời | Web, Mac, Windows, iOS, Android | Không (web) | Free 120 phút/tháng; Pro ~$8.17–8.25/tháng (trả năm) | Chưa xác minh\[46\]\[49\] |
| Fireflies | Bot ghi họp + CRM | Web, desktop, mobile | Không (web) | Pro $18/tháng ($10 nếu trả năm) | Không\[50\] |
| MS Teams live translation | Phụ đề dịch trong Teams | Trong Teams | Teams web | Thuộc Teams Premium/Copilot (giá chưa xác minh) | Chỉ trong Teams |

**Nhận định:** Không đối thủ nào trong bảng có app Linux native. Cũng không có sản phẩm nào kết hợp được dictation, ghi họp và dịch hai chiều với BYOK. Đây là ngách thực sự còn trống.

### 8. Gap analysis và đề xuất cho bản clone (BYOK Soniox + Claude, Linux/macOS/Windows)

**Những gì Soniox Desktop đang thiếu và bản clone có thể lấp vào**
1. Hỗ trợ Linux (Ubuntu).
2. BYOK: không thu phí thuê bao, minh bạch chi phí, dữ liệu đi thẳng tới nhà cung cấp.
3. Caption overlay nổi, trong suốt, click-through cho họp và xem video.
4. Thu rõ ràng **mic + system audio** và tách kênh (mic = "Tôi", loopback = "Họ"), giúp diarization tốt hơn.
5. Export đa định dạng (Markdown, TXT, SRT/VTT, DOCX, JSON có timestamp).
6. Lưu trữ local-first, có tìm kiếm full-text.
7. Lựa chọn LLM (Claude) và prompt template do người dùng làm chủ.

**MVP (v1, khoảng 8–12 tuần với 2–3 dev)**
- **Dictation:** global hotkey (toggle và push-to-talk). Dùng manual finalization của Soniox, chèn text qua clipboard + giả lập Ctrl/Cmd+V (fallback gõ Unicode). Có từ điển/context, chọn ngôn ngữ chính và language hints.
- **Live transcript:** chọn nguồn Mic / System / Cả hai. Hiển thị token tạm thời bằng chữ xám, token chốt bằng chữ đậm. Có diarization.
- **Live translation:** one-way và two-way, cửa sổ overlay luôn nổi trên cùng.
- **Lưu phiên local:** SQLite + FTS5; tùy chọn lưu audio dạng Opus/FLAC. Export MD/TXT/SRT/VTT.
- **Quản lý key:** lưu Soniox key và Anthropic key trong keychain của hệ điều hành (macOS Keychain, Windows Credential Manager/DPAPI, Linux Secret Service/libsecret). Có nút "Test key".
- **Theo dõi chi phí:** hiển thị số giờ kết nối và ước tính $ của phiên. Tự đóng WebSocket khi im lặng quá N giây (VAD) để tránh trả tiền cho khoảng im lặng.

**Khác biệt nhờ Claude API (v1.5–v2)**
- Tóm tắt có cấu trúc theo template: biên bản họp, action items có owner và deadline, quyết định, rủi ro, câu hỏi mở. Đặc biệt có template presales: requirement, scope, estimate, câu hỏi cho khách.
- "Ask your meeting" (hỏi đáp trên transcript, kể cả nhiều cuộc họp). Glossary dự án tự sinh từ transcript và nạp ngược vào context của Soniox.
- Hậu biên tập bản dịch theo ngữ cảnh: Soniox dịch real-time, Claude "polish" lại sau cuộc họp và giữ thuật ngữ khách hàng. Restyle dictation (email trang trọng, Slack, commit message, Jira ticket).
- Copilot trong cuộc họp: gợi ý câu hỏi tiếp theo hoặc trả lời nhanh, chạy theo cửa sổ trượt khoảng 2–5 phút transcript để kiểm soát chi phí token.
- Tùy chọn TTS (Soniox tts-rt-v2) để làm speech-to-speech cho hội thoại hai chiều. Đây là tính năng Soniox App chưa có.\[51\]

**Thách thức kỹ thuật chính theo hệ điều hành**

| Vấn đề | Linux (Ubuntu) | macOS | Windows |
|---|---|---|---|
| System audio | PipeWire (Ubuntu 22.10+ mặc định): thu từ monitor source của sink qua `pipewire-rs` hoặc lớp tương thích PulseAudio (`libpulse`, "*.monitor"); cần UI chọn sink | ScreenCaptureKit (macOS 13+) cần quyền "Screen & System Audio Recording"; Core Audio process taps trên macOS mới hơn là lựa chọn thay thế | WASAPI loopback (ổn định, không cần quyền đặc biệt) |
| Microphone | cpal (ALSA/PipeWire) | cpal / AVFoundation, quyền Microphone | cpal / WASAPI |
| Global hotkey | X11 dễ; **Wayland khó**: cần XDG GlobalShortcuts portal (GNOME/KDE mới) hoặc fallback đọc evdev với quyền `input` | Cần quyền Accessibility/Input Monitoring | RegisterHotKey / low-level hook |
| Chèn text vào app khác | Wayland: `wtype`/`ydotool` (uinput) hoặc clipboard + paste; X11: xdotool/XTest | Accessibility API hoặc CGEvent paste | SendInput (Unicode) |
| Overlay luôn nổi trên cùng | Wayland hạn chế always-on-top và vị trí cửa sổ; cần test GNOME/KDE | NSPanel level cao | Topmost window |
| Đóng gói & cập nhật | AppImage + .deb (+ Flatpak sau; sandbox Flatpak làm khó việc thu audio/hotkey) | Ký + notarize (bắt buộc để quyền TCC hoạt động ổn) | Ký code (tránh SmartScreen) |

- **Khuyến nghị stack:** Tauri 2 + Rust core, gồm audio capture, resample về 16 kHz PCM s16le mono, client WebSocket và VAD trong Rust. UI dùng React hoặc Svelte. Lý do là WebKitGTK thiếu WebRTC trên Linux, nên mọi xử lý audio phải nằm ở Rust.\[41\]
- Nếu đội đã quen Node, Electron cũng khả thi, nhất quán hơn trên Linux nhưng nặng hơn.

**Lưu ý pháp lý khi thương mại hóa BYOK**
- ToS của Soniox (bản 29/06/2026) cấp quyền dùng "for your internal business or development purposes". ToS cũng cấm "sublicense, resell, lease, rent, provide service bureau access to, or redistribute the Services themselves unless Soniox expressly agrees in writing".\[36\]
- Với BYOK, mỗi người dùng cuối tự tạo tài khoản Console, tự chấp nhận ToS và tự trả tiền. App của user chỉ là phần mềm client, không bán lại dịch vụ, nên rủi ro thấp hơn nhiều so với mô hình gom key.
- Tuy vậy, ToS chưa có điều khoản nào nói rõ về BYOK. **Nên xin xác nhận bằng văn bản từ Soniox** (sales/partnership) trước khi bán. Đồng thời cần:
  - Không dùng tên hay thương hiệu "Soniox" theo cách gây hiểu nhầm là sản phẩm chính thức.
  - Không gom key hoặc proxy qua server của mình.
  - Tuân thủ điều khoản cấm "reverse engineer… replicate Soniox… features".\[36\] Clone *ý tưởng* UI là bình thường, nhưng không sao chép tài sản và không dùng output để train model.
  - Cung cấp cơ chế thông báo/consent ghi âm cho người tham gia họp. ToS đặt trách nhiệm này lên khách hàng,\[36\] và luật Việt Nam về dữ liệu cá nhân (Nghị định 13/2023) cũng yêu cầu.
- Với Anthropic, BYOK trong app desktop là mô hình phổ biến, nhưng cũng nên rà Commercial Terms và Usage Policy hiện hành (chưa kiểm tra trong nghiên cứu này).

## Recommendations

1. **Định vị:** "Soniox-grade accuracy, BYOK, chạy cả Linux". Khách hàng mục tiêu là dev/IT/presales đa ngôn ngữ (Việt–Anh–Nhật) và các công ty outsourcing làm việc với khách Nhật hoặc Âu–Mỹ. Cặp Việt↔Nhật real-time là điểm bán hàng mạnh.
2. **Thứ tự build:**
   - (1) Lõi audio + WebSocket bằng Rust, chạy trên cả ba hệ điều hành. Ưu tiên Linux PipeWire và Wayland vì đây là phần rủi ro nhất.
   - (2) Dictation.
   - (3) Overlay dịch.
   - (4) Phiên họp + Claude.
3. **Kiểm soát chi phí cho người dùng:** VAD/auto-close kết nối, đồng hồ chi phí theo phiên, cảnh báo ngân sách tháng. Đây là điểm khác biệt có giá trị vì Soniox tính phí cả thời gian im lặng.
4. **Trước khi code:** chạy pilot 1–2 tuần đo WER tiếng Việt và độ trễ của stt-rt-v6 trên audio thật, rồi gửi email xin Soniox xác nhận mô hình BYOK thương mại.
5. **Mô hình kinh doanh gợi ý:** bán license một lần hoặc theo năm cho app (theo kiểu MacWhisper/Superwhisper lifetime), cộng gói Team (cấu hình tập trung, template prompt chung). Không bán lại phút API.

## Caveats

- Các thông tin sau **chưa xác minh được**: số phiên bản và changelog desktop, yêu cầu hệ thống tối thiểu, phím tắt mặc định, có thu system audio hay không, định dạng export, quy đổi credit ra phút, hỗ trợ Apple Silicon native. Trang trợ giúp con của Help Center không truy cập được đầy đủ.
- Nhận định "app viết bằng Tauri" là suy luận từ hạ tầng phân phối (CrabNebula, NSIS), không phải công bố của Soniox.
- Giá API công bố là quy đổi "≈/giờ" từ billing theo token. Hành vi tính phí theo thời gian kết nối đến từ nguồn thứ cấp (PR của Omi, README cộng đồng), cần kiểm chứng bằng hóa đơn Console thực tế.
- Giá đối thủ lấy từ trang so sánh bên thứ ba và có chỗ mâu thuẫn (Granola, Fireflies free tier). Cần kiểm tra lại trang chính thức trước khi đưa vào PRD.
- Help Center của Soniox có nội dung mâu thuẫn và có thể đã lỗi thời. Các trang marketing landing theo ngôn ngữ mang tính SEO và quảng cáo; các tuyên bố như "native-speaker accuracy" là của nhà cung cấp, chưa có benchmark độc lập cho tiếng Việt.

## Feature matrix tóm tắt

| Tính năng | Soniox Desktop (11/2026) | Đề xuất cho bản clone MVP | Ghi chú |
|---|---|---|---|
| Nền tảng | macOS, Windows | Linux, macOS, Windows | Linux là khác biệt chính |
| Mô hình tài khoản | Tài khoản Soniox App, credit/Pro | BYOK Soniox + Anthropic | Xác nhận ToS với Soniox |
| Dictation bằng hotkey vào mọi app | Có | Có (toggle + push-to-talk) | Wayland cần portal/uinput |
| Style/restyle dictation | Có (định sẵn + tùy chỉnh) | Có (qua Claude) | |
| Từ điển/context | Có | Có + glossary tự sinh | |
| Ghi họp real-time | Có (Smart Scribe) | Có | |
| Thu system audio | Chưa xác minh | Có, tách kênh mic/system | PipeWire / ScreenCaptureKit / WASAPI |
| Speaker diarization | Có | Có | |
| Language ID / code-switching | Có | Có | |
| Dịch live one-way | Có | Có | |
| Dịch live two-way | Có | Có | |
| Caption overlay luôn nổi trên cùng | Không thấy tài liệu | Có | |
| AI tóm tắt/action items/custom prompt | Có (LLM không công bố) | Có (Claude, template presales) | |
| Hỏi đáp trên nhiều cuộc họp | Không thấy | v2 | |
| Lưu trữ | Local + sync cloud | Local-first SQLite + FTS | |
| Tìm kiếm lịch sử | Web app | Có (desktop) | |
| Export | Copy/share link; định dạng chưa rõ | MD/TXT/SRT/VTT/DOCX/JSON | |
| Offline | Ghi offline, xử lý sau | Ghi offline, xử lý async sau | Dùng stt-async-v6 |
| TTS / speech-to-speech | Không | Tùy chọn (Soniox TTS) | v2 |
| Theo dõi chi phí | Không áp dụng | Có (đồng hồ $ theo phiên) | |
| Giá người dùng cuối | Free/Pro $19.99/tháng | $0.12–0.18/giờ API + Claude + license app | |

## Sources

1. [Using the Desktop App | Soniox App Help Center",](https://app.soniox.com/help-center/desktop-app/install)
2. [Soniox](https://soniox.com/blog/2026-01-15-everything-you-do-with-voice)
3. [Soniox | The end of English-first voice AI, now on desktop](https://soniox.com/blog/soniox-desktop-end-of-english-first-voice-ai)
4. [Soniox Debuts Multilingual Desktop App to End 'English-First' Voice AI](https://www.freevoicereader.com/blog/soniox-multilingual-desktop-app-voice-ai)
5. [Soniox launches STT v6 with agent text input and echo cancellation](https://www.voiceaispace.com/articles/soniox-stt-v6-agent-text-input-echo-cancellation)
6. [Soniox Speech-to-Text for Voice Agents on Phone Calls](https://www.dograh.com/hub/blogs/soniox-speech-to-text-voice-agents)
7. [Pricing | Soniox App](https://soniox.com/soniox-app/pricing)
8. [Soniox App | Everything you do with voice in your language](https://soniox.com/soniox-app)
9. [Getting started](https://app.soniox.com/help-center/web-app/usage/getting-started)
10. [GitHub - beanow-at-crabnebula/tauri: Build smaller, faster, and more secure desktop and mobile applications with a web frontend. · GitHub](https://github.com/beanow-at-crabnebula/tauri)
11. [CrabNebula Ltd. · GitHub](https://github.com/crabnebula-dev/)
12. [Troubleshooting & FAQ](https://app.soniox.com/help-center/desktop-app/faq)
13. [Home](https://app.soniox.com/help-center)
14. [AI Actions](https://app.soniox.com/help-center/desktop-app/smartscribe/ai-actions)
15. [2026 Best Software Awards are here!See the list](https://www.g2.com/sellers/soniox)
16. [Dictionary](https://app.soniox.com/help-center/desktop-app/voicetyping/dictionary)
17. [Plans](https://app.soniox.com/help-center/pricing/plans)
18. [Meeting Transcription App](https://soniox.com/soniox-app/use-cases/meeting-transcription)
19. [Smart Scribe](https://soniox.com/soniox-app/smart-scribe)
20. [Getting started](https://app.soniox.com/help-center/mobile-app/usage/getting-started)
21. [Vietnamese Speech Translation API: Real-Time & Async](https://soniox.com/speech-translation/vietnamese)
22. [Soniox App Translator - Speak and understand across languages](https://soniox.com/soniox-app/translator)
23. [How to translate](https://app.soniox.com/help-center/mobile-app/usage/translate)
24. [Privacy & data](https://app.soniox.com/help-center/privacy-data)
25. [Real-Time Text-to-Speech (TTS) API](https://soniox.com/text-to-speech)
26. [GitHub - miken90/my-translator: Real-time speech translation — macOS & Windows, free TTS, no server, your API keys only · GitHub](https://github.com/miken90/my-translator)
27. [Earning credits](https://app.soniox.com/help-center/pricing/earning-credits)
28. [Soniox Review (2026): Pricing, Pros & Cons](https://practiceall.com/medical-software-reviews/medical-dictation-transcription/soniox)
29. [fix(stt): price Soniox live at its connection-hour basis (\$0.33/audio-h) by Git-on-my-level · Pull Request #24681 · BasedHardware/omi · GitHub](https://github.com/BasedHardware/omi/pull/24681)
30. [GitHub - fatehiman/STT: Tiny Windows tray app: press a hotkey, speak, text is typed live (Soniox real-time STT, English + Persian) · GitHub](https://github.com/fatehiman/STT)
31. [Voice dictation API for fast, accurate voice typing](https://soniox.com/speech-to-text/use-cases/voice-dictation)
32. [WebSocket API](https://soniox.com/docs/stt/api-reference/websocket-api)
33. [Real-time speech-to-text translation](https://docs.soniox.com/translation/stt-translation/rt-translation)
34. [Speech-to-Text & Speech Recognition API](https://soniox.com/speech-to-text)
35. [Soniox STT v6 Adds Agent Text Input and Echo Filtering for Voice Agents - MarkTechPost](https://www.marktechpost.com/2026/11/03/soniox-stt-v6-agent-text-input-echo-cancellation/)
36. [Terms of service | Soniox](https://soniox.com/policies/terms-of-service)
37. [Temporary API keys](https://soniox.com/docs/guides/temporary-api-keys)
38. [GitHub - phuc-nt/my-translator: Real-time speech translation — macOS & Windows, free TTS, no server, your API keys only · GitHub](https://github.com/phuc-nt/my-translator)
39. [GitHub - khoa-le/my-translator: Real-time speech translation — macOS & Windows, free TTS, no server, your API keys only · GitHub](https://github.com/khoa-le/my-translator)
40. [soniox · GitHub Topics · GitHub](https://github.com/topics/soniox)
41. [Desktop app: Electron on Linux, Tauri on Windows by Dahrken-EP · Pull Request #28 · Dahrken-EP/GestaltIRC · GitHub](https://github.com/Dahrken-EP/GestaltIRC/pull/28)
42. [Vietnamese voice bot platform](https://soniox.com/platform/vietnamese)
43. [SuperWhisper vs. Wispr Flow: The Definitive 2026 Comparison](https://lumevoice.com/blog/superwhisper-vs-wispr-flow-comparison-2026/)
44. [Wispr Flow pricing (2026)](https://fabric.so/comparison/wispr-flow-pricing)
45. [AI Dictation Pricing Models 2026: Per-Hour vs Monthly vs Lifetime](https://weesperneonflow.ai/en/blog/2026-04-04-ai-dictation-pricing-per-hour-vs-monthly-subscription-2026/)
46. [The 10 Best AI Note Takers in 2026 (Tested and Ranked)](https://meetingnotes.com/blog/best-ai-note-takers)
47. [Granola AI Pricing (2026): Basic, Business and Enterprise](https://gravity.fast/blog/granola-pricing-explained/)
48. [Granola Alternatives (2026): Krisp, Otter, Fathom and More](https://www.stork.ai/blog/granola-alternatives-2026)
49. [I Tested 9 Fireflies Alternatives. Only 2 Keep Notes on Your Computer](https://www.getvoibe.com/resources/fireflies-alternatives/)
50. [AI note taker pricing 2026: Otter vs Granola vs Fathom vs Fireflies](https://www.transcribex.io/blog/ai-note-taker-pricing/)
51. [Vietnamese Text-to-Speech (TTS) API & Voice Cloning](https://soniox.com/text-to-speech/vietnamese)
