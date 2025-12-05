# BlueControl.java - Màn hình Điều khiển Bluetooth

## 1. Giới thiệu
File `BlueControl.java` là Activity chịu trách nhiệm chính trong việc điều khiển các thiết bị ngoại vi thông qua kết nối Bluetooth. Màn hình này được mở ra sau khi người dùng chọn một thiết bị từ danh sách hoặc chọn chế độ mô phỏng.

- **Layout XML:** `activity_blue.xml`
- **Chức năng chính:** Kết nối socket Bluetooth, gửi tín hiệu điều khiển, cập nhật giao diện trạng thái.

## 2. Các chế độ hoạt động
Activity này hoạt động ở hai chế độ dựa trên dữ liệu `Intent` nhận được:

### A. Chế độ Thực ("REAL")
- **Kết nối:** Sử dụng `AsyncTask` để thiết lập kết nối Socket Bluetooth tới địa chỉ MAC được truyền vào.
- **Giao thức:** Sử dụng UUID chuẩn cho Serial Port Profile (SPP): `00001101-0000-1000-8000-00805F9B34FB`.
- **Hành động:** Khi bấm nút, ứng dụng sẽ gửi ký tự (byte) qua luồng `OutputStream` tới mạch vi điều khiển (Arduino/STM32...).

### B. Chế độ Mô phỏng ("SIMULATION")
- **Kết nối:** Không thực hiện kết nối Bluetooth.
- **Hành động:** Khi bấm nút, ứng dụng chỉ thay đổi hình ảnh và thông báo trên giao diện để người dùng xem trước chức năng mà không cần phần cứng.

## 3. Bảng tín hiệu điều khiển (Protocol)
Dưới đây là các ký tự được gửi đi khi người dùng thao tác bấm nút trong chế độ "REAL". Bên phía vi điều khiển cần lập trình để lắng nghe các ký tự này:

| Nút bấm (Button) | Trạng thái mong muốn | Ký tự gửi đi (String/Byte) |
| :--- | :--- | :--- |
| **btnTb1** (Thiết bị 1) | **BẬT** (ON) | `"1"` |
| **btnTb1** (Thiết bị 1) | **TẮT** (OFF) | `"A"` |
| **btnTb2** (Thiết bị 2) | **BẬT** (ON) | `"7"` |
| **btnTb2** (Thiết bị 2) | **TẮT** (OFF) | `"G"` |

## 4. Luồng xử lý (Logic Flow)
1. **Khởi tạo (OnCreate):**
   - Nhận địa chỉ MAC và Mode từ Intent.
   - Nếu là chế độ thật: Bắt đầu tiến trình `ConnectBT` (hiển thị dialog "Đang kết nối...").
   - Nếu là mô phỏng: Thiết lập giao diện mặc định.

2. **Xử lý sự kiện (OnClick):**
   - Kiểm tra biến cờ (`flaglamp1`, `flaglamp2`) để xác định thiết bị đang bật hay tắt.
   - Gửi tín hiệu tương ứng (Bật hoặc Tắt).
   - Cập nhật biến cờ và thay đổi hình ảnh nút bấm (`setBackgroundResource`).

3. **Ngắt kết nối:**
   - Đóng `btSocket` và kết thúc Activity khi bấm nút `btnDis`.

## 5. Yêu cầu quyền (Permissions)
Để file này hoạt động trên Android 12 trở lên, cần cấp quyền Runtime trong `AndroidManifest.xml`:
- `android.permission.BLUETOOTH_CONNECT`

<img width="692" height="522" alt="image" src="https://github.com/user-attachments/assets/c903063a-3590-42e9-b68e-57682cfed6ae" />


<img width="733" height="592" alt="image" src="https://github.com/user-attachments/assets/83b9dd96-f1fc-4732-8900-777b5a6046bb" />

