# PicoClaw Home Assistant Add-on

Chạy **PicoClaw Launcher** bên trong Home Assistant Add-on.

## Tính năng
- Web UI PicoClaw qua Ingress Home Assistant
- Lưu dữ liệu bền vững trong `/data/.picoclaw`
- Hỗ trợ `amd64` và `aarch64`
- **Kiểm tra bản PicoClaw mới trên GitHub (nhẹ máy)**
- **Thông báo trong Home Assistant khi có bản mới** (không tự update)

## Cài đặt
1. Vào **Settings → Add-ons → Add-on Store**
2. Thêm repo add-on này
3. Cài add-on **PicoClaw**
4. Start add-on
5. Mở tab **Open Web UI**

## Tùy chọn
- `launcher_token`: token cố định cho launcher
- `public_mode`: bật lắng nghe public trong container
- `timezone`: ví dụ `Asia/Ho_Chi_Minh`
- `update_check_enabled`: bật/tắt kiểm tra bản mới
- `update_check_interval_hours`: chu kỳ kiểm tra (1-168 giờ)
- `notify_ha_on_new_version`: gửi thông báo vào Home Assistant khi có bản mới

## Luồng cập nhật khuyến nghị
- Add-on chỉ **báo có bản mới**
- Bạn chủ động bấm **Update** trong Home Assistant khi sẵn sàng
- Tránh auto-update bên trong container để đảm bảo ổn định

## Ghi chú
- Lần đầu cần vào Web UI để cấu hình provider/model
- Gateway/launcher của PicoClaw sẽ chạy trong add-on
- Nếu muốn truy cập ngoài ingress, có thể dùng port 18800
- Trạng thái check update lưu ở: `/data/picoclaw-update-check.json`
- Log check update lưu ở: `/data/picoclaw-update-check.log`
