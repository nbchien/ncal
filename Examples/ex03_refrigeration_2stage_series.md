# Hướng Dẫn Kỹ Thuật: Chu trình máy lạnh 2 cấp nén nối tiếp có làm mát trung gian (R134a)

- **Tệp dự án Ncal**: `examples/ex03_refrigeration_2stage_series.nc`
- **Môi chất công tác**: R134a
- **Hệ đơn vị tiêu chuẩn**: `°C · kPa · kg · kJ`

---

## 1. Tổng quan & Mục tiêu

Mô hình kỹ thuật `ex03_refrigeration_2stage_series` mô phỏng chi tiết chu trình nhiệt động và các thiết bị năng lượng nhiệt tương ứng.
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
   - Mở tệp `examples/ex03_refrigeration_2stage_series.nc` từ menu `Tập tin` $\to$ `Mở...` (hoặc phím tắt `Ctrl+O`).
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
| `m_dot` | 0.0807 |
| `W_1` | 2.021 |
| `W_2` | 2.088 |
| `W_c` | 4.109 |
| `COP` | 2.434 |
| `T_low` | -30 |
| `T_high` | 40 |
| `Q_evap_req` | 10 |
| `P_low` | 84.38 |
| `P_high` | 1017 |
| `P_mid` | 292.9 |
| `P[1]` | 84.38 |
| `h[1]` | 380.3 |
| `s[1]` | 1.751 |
| `P[2]` | 292.9 |
| `s[2]` | 1.751 |
| `h[2]` | 405.4 |
| `P[3]` | 292.9 |
| `h[3]` | 398.6 |
| `s[3]` | 1.727 |
| `P[4]` | 1017 |
| `s[4]` | 1.727 |
| `h[4]` | 424.5 |
| `P[5]` | 1017 |
| `h[5]` | 256.4 |
| `P[6]` | 84.38 |
| `h[6]` | 256.4 |

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
