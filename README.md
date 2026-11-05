# BÀI 5:
## 1. Sơ đồ luồng dữ liệu 3 lớp bảo vệ phối hợp (Data Flow Diagram)

Dưới đây là sơ đồ luồng dữ liệu dạng văn bản (text-based) mô tả cách phối hợp 3 lớp bảo mật để ngăn chặn hacker ngay cả khi chúng có mật khẩu:

```
[ Người dùng nhập: Username + Password ]
│
▼
┌───────────────────────┐
│  LỚP 1: BIOMETRIC     │ Thất bại -> Khóa tài khoản
│  (FaceID / Vân tay)   │
└───────────┬───────────┘
│Thành công
▼
┌───────────────────────┐
│  LỚP 2: THUẬT TOÁN    │ Nhập sai -> Từ chối truy cập
│  TOTP (Authenticator) │
└───────────┬───────────┘
│Trùng khớp mã OTP 6 số
▼
┌───────────────────────┐
│  LỚP 3: CHỮ KÝ SỐ     │ Thiết bị lạ -> Không có Private Key
│  (Hardware / Enclave) │
└───────────┬───────────┘
│Ký số thành công từ thiết bị hợp lệ
▼
[ ĐĂNG NHẬP VÀO VÍ THÀNH CÔNG ]
```

### Giải thích sự phối hợp ngăn chặn hacker:
*   **Khi hacker có Mật khẩu:** Chúng vẫn không thể vượt qua **Lớp 1** vì lớp này yêu cầu đặc điểm sinh trắc học trực tiếp của chủ ví trên thiết bị.
*   **Khi hacker dùng máy ảo / vượt qua sinh trắc học:** Chúng sẽ bị chặn lại ở **Lớp 2** bởi thuật toán **TOTP**. Mã xác thực này thay đổi liên tục mỗi 30 giây dựa trên thời gian thực và khóa bí mật lưu trong app authenticator của riêng nạn nhân.
*   **Khi hacker tấn công từ xa/chiếm phiên đăng nhập:** **Lớp 3 (Hardware Token/Secure Enclave)** sẽ yêu cầu thiết bị phải thực hiện ký giao dịch bằng Private Key được bảo vệ phần cứng. Nếu không có thiết bị vật lý gốc, hacker hoàn toàn bất lực.

---

## 2. Xử lý bẫy (Edge Case): Đăng nhập trên máy bay (Không có mạng/sóng)

*   **Giải pháp:** Người dùng **hoàn toàn vẫn lấy được mã OTP bình thường** để thực hiện giao dịch nhờ vào nguyên lý hoạt động của thuật toán **TOTP (Time-Based One-Time Password)**.
*   **Giải thích chi tiết:**
    *   **Không cần Internet:** Thuật toán TOTP sinh mã dựa trên 2 yếu tố cố định: **Khóa bí mật (Secret Key)** đã được lưu vào app từ trước và **Thời gian hiện tại (Unix Timestamp)** của điện thoại.
    *   **Đồng bộ thời gian:** Đồng hồ bên trong điện thoại (ngay cả khi bật chế độ máy bay) vẫn tự chạy chuẩn theo giây nhờ chip thạch anh của phần cứng. 
    *   Do cả ứng dụng ví (ở Local/Client) và ứng dụng mã hóa đều tự tính toán độc lập dựa trên bộ đếm thời gian này, mã OTP tạo ra trên điện thoại vẫn hoàn toàn chính xác mà không cần bất kỳ kết nối mạng nào. Khi máy bay hạ cánh hoặc ví lưu nháp giao dịch để gửi đi sau, mã vẫn hợp lệ với hệ thống máy chủ.