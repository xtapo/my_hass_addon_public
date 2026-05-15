# Voice Action Bridge VI Lite

Add-on nhẹ cho Home Assistant để nhập lệnh tiếng Việt tự nhiên và gọi service HA.

## Mục tiêu
- Chạy được trên máy yếu.
- Rule local trước, AI fallback tùy chọn.
- Không bắt buộc wake word/STT local.
- Có alias thiết bị, lịch sử lệnh, xác nhận lệnh nguy hiểm.

## Cách dùng nhanh
1. Cài add-on, bật Start on boot/Watchdog.
2. Mở Web UI.
3. Thêm alias, ví dụ:
   - `đèn phòng khách` -> `light.phong_khach`
   - `máy lạnh phòng ngủ` -> `climate.phong_ngu`
4. Nhập lệnh:
   - `bật đèn phòng khách`
   - `tắt đèn phòng khách`
   - `bật máy lạnh phòng ngủ 26 độ`

## Wake word / STT
Bản Lite không chạy always-listening mặc định để tiết kiệm CPU. Khuyến nghị dùng push-to-talk, Telegram voice, hoặc STT offload rồi gửi text command vào API `/api/command`.
