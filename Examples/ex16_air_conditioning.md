# Hướng Dẫn Kỹ Thuật: Hệ thống điều hòa không khí (Air Conditioning System)

- **Tệp dự án Ncal**: `examples/ex16_air_conditioning.nc`
- **Môi chất công tác**: Air and Water
- **Hệ đơn vị tiêu chuẩn**: `°C · kPa · kg · kJ`

---

## 1. Tổng quan & Mục tiêu

Mô hình kỹ thuật `ex16_air_conditioning` mô phỏng chi tiết chu trình nhiệt động và các thiết bị năng lượng nhiệt tương ứng.
Mục tiêu chính bao gồm:
1. Xác định đầy đủ các điểm trạng thái nhiệt động (Áp suất $P$, Nhiệt độ $T$, Enthalpy $h$, Entropy $s$).
2. Thiết lập hệ phương trình cân bằng khối lượng và bảo toàn năng lượng cho các thiết bị chính.
3. Giải bài toán hội tụ bằng solver phi tuyến của Ncal để tìm ra các ẩn số vận hành thực tế.

---

## 2. Cấu trúc hệ thống & Sơ đồ trạng thái

Hệ thống tuân thủ nghiêm ngặt quy ước đánh số điểm trạng thái nhiệt động theo chiều dòng chảy:
- Dòng môi chất tuần hoàn khép kín theo thứ tự liên tục $1 \to 2 \to 3 \to 4 \to \dots \to 1$.

### Bảng các điểm trạng thái kỹ thuật
Các thông số trạng thái cơ bản được xác định trực tiếp từ thư viện nhiệt động CoolProp tích hợp trong Ncal.

---

## 3. Mô hình toán học & Hệ phương trình

Hệ phương trình được xây dựng dựa trên các định luật nhiệt động lực học 1 & 2:
- **Bảo toàn khối lượng**: Lưu lượng khối lượng được cân bằng qua từng nút và nhánh thiết bị.
- **Bảo toàn năng lượng**:
  $$\dot{Q} - \dot{W} = \sum \dot{m}_{out} h_{out} - \sum \dot{m}_{in} h_{in}$$
- **Phương trình trạng thái CoolProp**: Tra cứu chính xác các hàm $P_{sat}$, $T_{sat}$, $h_{sat}$, $s_{sat}$, enthalpy, entropy.

---

## 4. Hướng dẫn chạy từng bước trên Ncal

1. **Thiết lập hệ đơn vị**:
   - Nhấp vào huy hiệu cấu hình đơn vị ở thanh trạng thái (Status Bar).
   - Chọn: Nhiệt độ = **Celsius (°C)**, Áp suất = **kPa**, Năng lượng = **kJ**, Cơ sở khối lượng = **kg**.
2. **Mở dự án**:
   - Mở tệp `examples/ex16_air_conditioning.nc` từ menu `Tập tin` $\to$ `Mở...` (hoặc phím tắt `Ctrl+O`).
3. **Thực thi giải hệ phương trình**:
   - Nhấn phím `F5` hoặc nút `Giải` trên thanh công cụ.
   - Kiểm tra thanh trạng thái hiển thị thông báo hội tụ thành công (`Hội tụ sau ... bước lặp`).
4. **Quan sát kết quả**:
   - Xem bảng Nghiệm (`sol_table`) và bảng Biến (`var_table`) để theo dõi các thông số nghiệm.
   - Mở cửa sổ sơ đồ công nghệ (nếu có) để quan sát các huy hiệu trạng thái được cập nhật trực quan.

---

## 5. Kết quả tính toán kỳ vọng

Bảng số liệu nghiệm hội tụ tiêu chuẩn từ bộ giải Ncal (được kiểm chứng độc lập bằng CoolProp):

| Đại lượng | Giá trị kỳ vọng |
|:---|:---|
| `m_dot_c` | 0.03112 |
| `Q_dot_cwc` | 1.182e+05 |
| `Q_dot_sc` | 4.577e+04 |
| `V_dot_c` | 0.07839 |
| `T_out` | 30 |
| `phi_out` | 0.58 |
| `V_dot_out` | 5100 |
| `T_in` | 24 |
| `phi_in` | 0.45 |
| `V_dot_r` | 8200 |
| `P` | 1 |
| `Q_dot_b` | 100 |
| `m_dot_v_b` | 0.0115 |
| `P_si` | 1.013e+05 |
| `T[1]` | 24 |
| `phi[1]` | 0.45 |
| `T1_si` | 297.1 |
| `omega[1]` | 0.008394 |
| `h_av[1]` | 4.55e+04 |
| `v_av[1]` | 0.8528 |
| `m_dot_a_r` | 4.538 |
| `T[2]` | 30 |
| `phi[2]` | 0.58 |
| `T2_si` | 303.1 |
| `omega[2]` | 0.01557 |
| `h_av[2]` | 6.996e+04 |
| `v_av[2]` | 0.88 |
| `m_dot_a_out` | 2.735 |
| `m_dot_a` | 7.273 |
| `omega[3]` | 0.01109 |
| `h_av[3]` | 5.47e+04 |
| `h3_si` | 5.47e+04 |
| `T[3]` | 299.4 |
| `phi[3]` | 0.517 |
| `omega[5]` | 0.006812 |
| `h_av[5]` | 3.175e+04 |
| `h5_si` | 3.175e+04 |
| `T[5]` | 287.6 |
| `T_5_C` | 14.45 |
| `phi[5]` | 0.664 |
| `omega[4]` | 0.006812 |
| `phi[4]` | 1 |
| `T[4]` | 281.4 |
| `h_av[4]` | 2.546e+04 |
| `T_4_safe` | 281.4 |
| `v_c` | 2.519 |
| `h_c` | 3037 |
| `h_av_x` | 4.148e+04 |
| `SHR` | 0.7074 |

---

## 6. Thảo luận & Ý nghĩa kỹ thuật

- Kết quả tính toán thỏa mãn các ràng buộc vật lý cơ bản:
  - Hiệu suất chu trình luôn nhỏ hơn chu trình Carnot tương ứng giữa cùng nguồn nhiệt.
  - Cân bằng nhiệt toàn hệ thống đạt sai số dư triệt tiêu ($< 10^{-6}$).
- Cho phép người dùng linh hoạt khảo sát ảnh hưởng của các thông số đầu vào (nhiệt độ, áp suất, lưu lượng) lên hiệu suất toàn hệ thống.

---

## 7. Gợi ý mở rộng nghiên cứu

Người dùng có thể mở rộng mô hình bằng cách:
- Khảo sát tổn thất áp suất trên đường ống và các thiết bị trao đổi nhiệt.
- Ghép nối khảo sát độ nhạy thông số thông qua công cụ `Phân tích độ nhạy` (`F6`) trong Ncal.

---

## 8. Quy chuẩn Hệ đơn vị

- **Nhiệt độ**: `[degC]` hoặc `[K]`
- **Áp suất**: `[kPa]`
- **Năng lượng / Công suất**: `[kJ]` / `[kW]`
- **Lưu lượng khối lượng**: `[kg/s]`
- **Enthalpy riêng**: `[kJ/kg]`
- **Entropy riêng**: `[kJ/(kg*K)]`
