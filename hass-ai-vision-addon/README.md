# 📷 AI Vision Camera Add-on — Hướng dẫn sử dụng từ A-Z

> Add-on Home Assistant giúp lấy ảnh từ camera/entity, gửi sang AI Vision model qua AI Proxy Router, lưu lịch sử mô tả, gửi cảnh báo Telegram/MQTT, và xem lại trên giao diện web/mobile.

---

## 1) Add-on này dùng để làm gì?

**AI Vision Entity Describer** dùng để tự động phân tích hình ảnh từ camera Home Assistant.

Add-on có thể:
- Lấy ảnh từ `camera.*` hoặc entity có `entity_picture`
- Gửi ảnh sang AI Proxy Router / OpenAI-compatible Vision endpoint
- Nhận mô tả bằng tiếng Việt
- Lưu lịch sử ảnh + mô tả AI
- Hiển thị giao diện dễ xem trên điện thoại
- Gửi kết quả ra MQTT để Home Assistant tạo sensor
- Gửi Telegram khi có cảnh báo quan trọng
- Lọc bớt sự kiện nhiễu bằng keyword
- Xem live snapshot camera trong tab **Camera Live**

Phù hợp cho:
- Camera cổng
- Camera sân
- Camera cửa hàng
- Camera kho
- Camera phòng khám/văn phòng
- Theo dõi người/xe/vật thể bất thường

---

## 2) Yêu cầu trước khi dùng

Cần có:
- Home Assistant có Supervisor
- Add-on **AI Proxy Router** đã chạy hoặc endpoint AI Vision tương thích OpenAI
- Ít nhất 1 model có hỗ trợ ảnh/vision
- Camera entity trong Home Assistant, ví dụ:
  - `camera.cong`
  - `camera.san_truoc`
  - `camera.gara`
- Nếu muốn tạo sensor MQTT:
  - Mosquitto broker đã cài
  - MQTT integration đã bật trong Home Assistant
- Nếu muốn nhận Telegram:
  - Telegram bot token
  - Telegram chat ID

---

## 3) Cài đặt add-on

1. Vào **Settings → Add-ons → Add-on Store**
2. Thêm repository public nếu chưa thêm:

```text
https://github.com/trankhanhduy2929-beep/my_hass_addon_public
```

3. Tìm **AI Vision Entity Describer**
4. Nhấn **Install**
5. Bật:
   - **Start on boot**
   - **Watchdog**
6. Nhấn **Start**
7. Nhấn **Open Web UI** hoặc mở panel **AI Vision**

---

## 4) Truy cập giao diện

Sau khi add-on chạy:
- Mở **Open Web UI** trong trang add-on
- Hoặc vào sidebar Home Assistant → **AI Vision**

Giao diện có các tab/chức năng chính:
- **Events / Lịch sử**: xem ảnh đã phân tích + mô tả AI
- **Camera Live**: xem snapshot camera dạng gallery
- **Account / Cấu hình**: nhập cấu hình HA, AI Proxy, MQTT, Telegram, bộ lọc

---

## 5) Cấu hình cơ bản

Vào tab cấu hình trong Web UI.

### 5.1 Home Assistant

Khuyên dùng:

```text
HA URL: http://supervisor/core
HA Token: để trống
```

Nếu add-on không lấy được ảnh, dùng Long-Lived Access Token:
1. Vào Home Assistant → Profile
2. Kéo xuống **Long-Lived Access Tokens**
3. Tạo token mới
4. Dán vào ô `ha_token`

---

### 5.2 Camera entities

Nhập danh sách camera/entity cần phân tích, mỗi dòng 1 entity:

```text
camera.cong
camera.san_truoc
camera.gara
```

Lưu ý:
- Ưu tiên entity dạng `camera.*`
- Entity có `entity_picture` cũng có thể dùng
- Tên entity phải đúng như trong Home Assistant

---

### 5.3 AI Proxy / AI Vision endpoint

Nếu dùng AI Proxy Router trong cùng Home Assistant:

```text
AI Proxy Base URL: http://homeassistant.local:1236/v1
AI API Key: sk-proxy-...
AI Model: auto-ai
```

Hoặc dùng model vision cụ thể, ví dụ:

```text
meta-llama/llama-4-scout-17b-16e-instruct
```

Ghi chú:
- Base URL có thể nhập dạng `/v1`, add-on sẽ tự gọi `/chat/completions`
- Model phải hỗ trợ ảnh
- Nếu báo model unavailable, kiểm tra model vision trong AI Proxy Router

---

## 6) Cấu hình thời gian hoạt động

Add-on hỗ trợ khung giờ hoạt động.

Ví dụ:

```text
Active start: 06:00
Active end: 22:00
```

Ý nghĩa:
- Trong khung giờ: camera có thay đổi ảnh thì phân tích
- Ngoài khung giờ: không chạy AI
- Khung giờ chỉ là cổng bật/tắt, không ép chạy cố định liên tục

---

## 7) Cơ chế trigger camera

Logic khuyên dùng:
- Add-on theo dõi thay đổi ảnh từ camera/entity
- Khi ảnh thay đổi trong khung giờ hoạt động → gửi AI phân tích
- Không cần chạy AI liên tục theo interval nếu ảnh không đổi

Lợi ích:
- Giảm tốn API
- Giảm spam lịch sử
- Nhẹ hơn cho máy Home Assistant

---

## 8) Cấu hình MQTT sensor

Nếu MQTT bật, add-on publish kết quả để Home Assistant tự tạo sensor.

Ví dụ camera:

```text
camera.cong
```

Với prefix:

```text
ai_vision
```

Topic mẫu:

```text
ai_vision/camera_cong/state
ai_vision/camera_cong/attributes
homeassistant/sensor/ai_vision_camera_cong/config
```

Sau khi chạy, Home Assistant có thể thấy sensor dạng:

```text
sensor.ai_vision_camera_cong
```

Sensor này chứa:
- Mô tả AI mới nhất
- Camera/entity nguồn
- Thời gian phân tích
- Trạng thái cảnh báo/lọc

---

## 9) Cấu hình Telegram cảnh báo

Nếu muốn add-on gửi Telegram:

Cần nhập:
- `telegram_bot_token`
- `telegram_chat_id`

Có nút **Test Telegram** trong UI để kiểm tra.

Khuyên bật:

```text
Chỉ gửi Telegram khi có cảnh báo quan trọng
```

Như vậy sự kiện bình thường vẫn lưu lịch sử, nhưng không spam Telegram.

---

## 10) Bộ lọc cảnh báo thông minh

Add-on có bộ lọc keyword để giảm nhiễu.

### Alert keywords

Nếu mô tả AI có từ khóa này → đánh dấu cảnh báo.

Ví dụ:

```text
người lạ
leo rào
đột nhập
cháy
khói
xe lạ
mở cổng
té ngã
```

### Ignore keywords

Nếu mô tả AI có từ khóa này → đánh dấu đã lọc, hạn chế gửi cảnh báo.

Ví dụ:

```text
không có gì bất thường
sân trống
không phát hiện người
ánh sáng thay đổi
mưa
```

### Similar alert cooldown

Dùng để chống spam cảnh báo giống nhau.

Ví dụ:

```text
300
```

Nghĩa là cảnh báo tương tự trong 300 giây sẽ bị hạn chế gửi lại.

---

## 11) Xem lịch sử AI

Tab lịch sử hiển thị:
- Ảnh camera
- Mô tả AI
- Thời gian
- Entity nguồn
- Badge trạng thái:
  - **Cảnh báo**
  - **Đã lọc**
- Nút copy mô tả
- Nút xem thêm/thu gọn mô tả dài

Có thống kê nhanh 24h:
- Tổng sự kiện
- Số cảnh báo
- Số đã lọc

---

## 12) Camera Live

Tab **Camera Live** dùng để xem nhanh snapshot các camera đã cấu hình.

Dùng khi cần:
- Kiểm tra camera có ảnh không
- Xem camera nào đang lỗi
- Xem nhanh nhiều camera trên điện thoại

Lưu ý:
- Đây là snapshot/gallery nhẹ
- Không thay thế dashboard camera streaming đầy đủ của Home Assistant

---

## 13) Retention — lưu lịch sử bao lâu?

Có thể cấu hình:

```text
history_retention_days: 7
history_max_rows: 300
```

Ý nghĩa:
- Xoá lịch sử cũ hơn số ngày cấu hình
- Giữ tối đa số dòng lịch sử
- Giúp database nhẹ, tránh đầy bộ nhớ

---

## 14) Prompt gợi ý cho AI Vision

Nên dùng prompt ngắn, rõ, tiếng Việt.

Ví dụ:

```text
Hãy mô tả ngắn gọn ảnh camera này bằng tiếng Việt. Nếu có người, xe, vật lạ, hành vi bất thường hoặc nguy cơ an ninh thì nói rõ. Nếu bình thường, trả lời ngắn gọn là không có gì bất thường.
```

Prompt cho camera cổng:

```text
Phân tích ảnh camera cổng. Chú ý người lạ, xe lạ, mở cổng, leo rào, tụ tập, cháy khói. Trả lời tiếng Việt ngắn gọn, ưu tiên cảnh báo nếu có bất thường.
```

Prompt cho phòng khám/cửa hàng:

```text
Phân tích ảnh khu vực cửa ra vào. Ghi nhận người ra vào, đông khách, vật cản, té ngã, khói/cháy hoặc tình huống bất thường. Trả lời tiếng Việt dễ hiểu.
```

---

## 15) Ví dụ cấu hình nhanh

### Camera cổng nhà

```text
camera_entities:
camera.cong

active_start: 06:00
active_end: 23:00

alert_keywords:
người lạ
leo rào
xe lạ
mở cổng

ignore_keywords:
không có gì bất thường
sân trống
ánh sáng thay đổi

telegram_only_on_alert: bật
similar_alert_cooldown_seconds: 300
```

### Camera cửa hàng

```text
camera_entities:
camera.cua_hang
camera.quay_thu_ngan

alert_keywords:
té ngã
cháy
khói
ẩu đả
vật cản

ignore_keywords:
khách bình thường
không có gì bất thường
```

---

## 16) Troubleshooting nhanh

### Không thấy ảnh

Kiểm tra:
- Entity camera đúng tên chưa
- Camera có snapshot không
- HA URL đúng chưa
- Token có quyền đọc camera chưa

Thử:

```text
HA URL: http://supervisor/core
HA Token: để trống
```

Nếu vẫn lỗi, dùng Long-Lived Access Token.

---

### AI báo lỗi model unavailable

Nguyên nhân thường gặp:
- Model không hỗ trợ vision
- Model không tồn tại trên provider
- AI Proxy Router chưa cấu hình route vision

Cách xử lý:
- Đổi sang model vision
- Test provider/model trong AI Proxy Router
- Kiểm tra API key còn hạn/quota

---

### Telegram không gửi

Kiểm tra:
- Bot token đúng chưa
- Chat ID đúng chưa
- Bot đã được nhắn `/start` chưa
- Nếu gửi vào group, bot đã được add vào group chưa
- Nút Test Telegram trả lỗi gì

---

### MQTT không thấy sensor

Kiểm tra:
- Mosquitto broker đã chạy
- MQTT integration đã bật
- Discovery MQTT đang bật
- Topic prefix đúng
- Restart Home Assistant nếu cần

---

### Lịch sử quá nhiều

Giảm:

```text
history_retention_days
history_max_rows
```

Ví dụ nhẹ:

```text
history_retention_days: 3
history_max_rows: 100
```

---

## 17) Best practice

- Chỉ phân tích camera thật sự cần AI
- Dùng khung giờ hoạt động để giảm tốn API
- Bật Telegram only on alert để tránh spam
- Viết prompt ngắn, rõ, đúng mục tiêu camera
- Tách alert keywords và ignore keywords rõ ràng
- Test từng camera trước, sau đó mới thêm nhiều camera
- Dùng AI Proxy Router có nhiều provider/key để fallback khi 1 nguồn lỗi/quota

---

## 18) Bảo mật

- Không chia sẻ ảnh camera/log công khai nếu có người thật
- Không gửi API key/token cho người khác
- Nếu lộ token/key: thu hồi và tạo key mới ngay
- Không mở port add-on ra internet nếu không cần
- Ưu tiên dùng Ingress nội bộ Home Assistant

---

## 19) Thông tin kỹ thuật

- **Slug:** `ai_vision_entity_describer`
- **Ingress port:** `1237`
- **Panel:** `AI Vision`
- **Kiến trúc:** `amd64`, `aarch64`, `armv7`
- **Config/data:** lưu trong vùng add-on config/data để hạn chế mất cấu hình khi update
- **History DB:** SQLite
- **AI endpoint:** OpenAI-compatible `/chat/completions`

---

## 20) Quy trình dùng khuyên nghị

1. Cài AI Proxy Router
2. Thêm provider/model vision vào AI Proxy Router
3. Cài AI Vision Entity Describer
4. Mở Web UI AI Vision
5. Nhập camera entity
6. Nhập AI Proxy Base URL + Proxy API Key + model
7. Test ảnh/camera
8. Bật MQTT nếu cần sensor
9. Bật Telegram nếu cần cảnh báo
10. Cấu hình alert/ignore keyword
11. Theo dõi lịch sử 1 ngày rồi tinh chỉnh

---

Chúc triển khai mượt, cảnh báo ít nhiễu, xem camera thông minh hơn 🦞
