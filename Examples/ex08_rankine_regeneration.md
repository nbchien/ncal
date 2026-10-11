# Hướng Dẫn Kỹ Thuật: Chu trình Rankine hồi nhiệt gia nhiệt hở nước cấp (Water)

- **Tệp dự án Ncal**: `examples/ex08_rankine_regeneration.nc`
- **Môi chất công tác**: Water
- **Hệ đơn vị tiêu chuẩn**: `°C · kPa · kg · kJ`

---

## 1. Tổng quan & Mục tiêu

Mô hình kỹ thuật `ex08_rankine_regeneration` mô phỏng chi tiết chu trình nhiệt động và các thiết bị năng lượng nhiệt tương ứng.
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
   - Mở tệp `examples/ex08_rankine_regeneration.nc` từ menu `Tập tin` $\to$ `Mở...` (hoặc phím tắt `Ctrl+O`).
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
| `m_dot` | 8.892 |
| `W_total` | 1.007e+04 |
| `P_boiler` | 7000 |
| `P_cond` | 10 |
| `T_max` | 500 |
| `P_extract` | 700 |
| `W_net_req` | 1e+04 |
| `P[1]` | 10 |
| `h[1]` | 191.8 |
| `v[1]` | 0.00101 |
| `P[2]` | 700 |
| `w_p1` | 0.6971 |
| `h[2]` | 192.5 |
| `P[3]` | 700 |
| `h[3]` | 697 |
| `v[3]` | 0.001108 |
| `P[4]` | 7000 |
| `w_p2` | 6.98 |
| `h[4]` | 704 |
| `P[5]` | 7000 |
| `T[5]` | 500 |
| `h[5]` | 3411 |
| `s[5]` | 6.8 |
| `P[6]` | 700 |
| `s[6]` | 6.8 |
| `h[6]` | 2804 |
| `P[7]` | 10 |
| `s[7]` | 6.8 |
| `h[7]` | 2154 |
| `y` | 0.1932 |
| `w_t` | 1132 |
| `w_net` | 1125 |
| `q_in` | 2707 |
| `eta_th` | 0.4154 |

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
