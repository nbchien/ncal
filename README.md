# Ncal — Bộ giải hệ phương trình kỹ thuật (Beta 5.0.3 update 1)

<img width="2877" height="1718" alt="image" src="https://github.com/user-attachments/assets/76efaa0f-4aa2-4136-a576-317308318bb0" />

Ncal là công cụ giải hệ phương trình phi tuyến kỹ thuật mã nguồn mở với tính năng và cú pháp tương tự Engineering Equation Solver (EES). Ncal hỗ trợ tính toán đơn vị tự động (Pint), cấu hình hệ đơn vị linh hoạt (°C/K, bar/kPa/Pa, kJ/J), tích hợp thuộc tính nhiệt động lực học môi chất chất lưu (CoolProp) với cú pháp hàm trực quan, bộ 16 ví dụ mẫu chuẩn hóa công nghiệp và 4 dự án chu trình kỹ thuật mẫu, công cụ chèn hàm & danh mục môi chất lạnh, cấu trúc điều khiển (FOR/WHILE/IF/CASE), kiểm tra đơn vị nhất quán, đồ thị tổng quát, phân tích nhạy cảm, tìm nhiều nghiệm và cửa sổ sơ đồ kỹ thuật trực quan (Diagram Window).

---

## 1. Cài đặt và Khởi chạy

### Sử dụng Bản phát hành Đóng gói sẵn (Windows Standalone)
1. Tải gói nén chính thức **[Ncal-windows.zip](https://github.com/nbchien/ncal/releases/download/v5.0-beta.3.1/Ncal-windows_v50_beta3.1.zip)** (~111MB). Phiên bản mới nhất là phiên bản v5.0.3 update 1.
2. Giải nén và nhấp đúp vào **`Ncal.exe`** để khởi chạy ngay lập tức.
3. **Giao diện & Taskbar Icon**: Ứng dụng đã được đăng ký mã định danh Windows `AppUserModelID` (`smartair.ncal.equation_solver.5.0.3`).
4. Tải thư viện example để xem các ví dụ mẫu.

#### Mã băm xác thực toàn vẹn (Checksums SHA-256):
- **Tệp nén `Ncal-windows.zip`**:
  `bd1bddac2c827ff59a673fde60a3cf71bb2b85ddbfe8d2d11c80d663e4d4b3b2`
- **Tệp thực thi `Ncal.exe`**:
  `fb83bf3fa559a0a231beb445dcbb9feafe0686e6700acc21a19f28a1c902c49d`

---

## 2. Cú pháp và Quy ước ngôn ngữ

### 2.1. Phân biệt Tham số (Parameter) và Ẩn số (Variable)
- **Tham số (Parameter)**: Được xác định tự động khi một dòng có dạng:
  - `name = number[unit]` (ví dụ: `T = 300[K]`, `rho = 1000[kg/m^3]`, `H = 5[m]`)
  - Biểu thức chỉ chứa các tham số đã khai báo trước đó, hằng số và hàm toán học (ví dụ: `P_calc = rho * g * H`).
  - Tham số được giải ngay theo thứ tự dòng và nạp trực tiếp giá trị vào môi trường tính toán; không đưa vào bộ biến ẩn số của solver.
  - Tham số không được gán điều kiện/cận `{name, guess, lo, hi}` (sẽ báo lỗi nếu vi phạm).
  - Phải khai báo tham số trước khi sử dụng trong biểu thức tham số khác ("used before declaration").
- **Ẩn số (Variable)**: Tất cả các đại lượng cần giải trong hệ phương trình phi tuyến.

### 2.2. Điều kiện giá trị đầu và cận biên (Guess & Bounds)
Cú pháp:
```ees
{tên_biến, giá_trị_đầu, cận_dưới, cận_trên}
```
Ví dụ:
```ees
{P, 1e5, 1e4, 2e6}
{x, 1.0, -10.0, 10.0}
```

### 2.3. Biến mảng (Array Variables)
Mỗi phần tử `P[1]`, `T[2]`, `V[i]` được quản lý như một biến độc lập. Cú pháp không bị nhầm lẫn với chú thích đơn vị:
- `P[1]`: Biến mảng phần tử số 1.
- `P[Pa]`: Biến `P` kèm chú thích đơn vị Pascal.

Các hàm hỗ trợ mảng đa tham số:
- `sum(P[1], P[2], P[3])`
- `min(P[1], P[2])`
- `max(P[1], P[2])`
- `avg(P[1], P[2], P[3])`
- `size(P[1], P[2])`

### 2.4. Ngôn ngữ điều khiển (Compiler)
Hỗ trợ các khối điều khiển không cần thụt đầu dòng (tương thích phong cách EES):
- **Vòng lặp FOR**:
  ```ees
  N = 3
  FOR i = 1, N [STEP 1] NEXT
    P[i] = 100 * i
    V[i] = P[i] * Q / 1000
  NEXT
  ```
  *(Biến vòng lặp `i` tự động mở rộng thành các phương trình và biến mảng `P[1..N]`).*
- **Vòng lặp WHILE**:
  ```ees
  WHILE x < 10 END
  ```
- **Rẽ nhánh IF-ELSE**:
  ```ees
  IF mode == 1 THEN
    coeff = 10
  ELSEIF mode == 2 THEN
    coeff = 25
  ELSE
    coeff = 50
  ENDIF
  ```
- **Lựa chọn CASE**:
  ```ees
  CASE mode
    WHEN 1
      factor = 1.2
    WHEN 2
      factor = 1.5
    ELSE
      factor = 1.0
  ENDCASE
  ```

### 2.5. Hàm nhiệt động lực học
Ncal hỗ trợ bộ hàm tra thông số nhiệt động trực quan và ngắn gọn:

#### Tra thông số bão hòa:
- **Áp suất & Nhiệt độ bão hòa**:
  - `P = P_sat(Ammonia, T)`: Áp suất bão hòa [Pa] theo nhiệt độ $T$ [K].
  - `T = T_sat(Water, P)`: Nhiệt độ bão hòa [K] theo áp suất $P$ [Pa].
- **Enthalpy bão hòa**:
  - `h_l = h_sat_liq(fluid, T)` hoặc `h_l(fluid, T)`: Enthalpy lỏng bão hòa ($Q = 0$) [J/kg].
  - `h_v = h_sat_vap(fluid, T)` hoặc `h_v(fluid, T)`: Enthalpy hơi bão hòa ($Q = 1$) [J/kg].
- **Entropy bão hòa**:
  - `s_l = s_sat_liq(fluid, T)` hoặc `s_l(fluid, T)`: Entropy lỏng bão hòa [J/(kg·K)].
  - `s_v = s_sat_vap(fluid, T)` hoặc `s_v(fluid, T)`: Entropy hơi bão hòa [J/(kg·K)].
- **Thể tích riêng & Khối lượng riêng bão hòa**:
  - `v_v = v_sat_vap(fluid, T)` hoặc `v_v(fluid, T)`: Thể tích riêng hơi bão hòa [m³/kg].
  - `v_l = v_sat_liq(fluid, T)` hoặc `v_l(fluid, T)`: Thể tích riêng lỏng bão hòa [m³/kg].
  - `rho_l = rho_sat_liq(fluid, T)`, `rho_v = rho_sat_vap(fluid, T)`: Khối lượng riêng [kg/m³].
- **Nội năng riêng bão hòa**:
  - `u_l = u_sat_liq(fluid, T)`, `u_v = u_sat_vap(fluid, T)` [J/kg].

#### Tra thông số trạng thái bất kỳ (2 thông số độc lập):
- `h = enthalpy(fluid, s=s_val, P=P_val)` (hoặc `h(fluid, s=s_val, P=P_val)`)
- `T = temperature(fluid, s=s_val, P=P_val)` (hoặc `T(fluid, s=s_val, P=P_val)`)
- `s = entropy(fluid, h=h_val, P=P_val)` (hoặc `s(fluid, h=h_val, P=P_val)`)
- `P = pressure(fluid, h=h_val, s=s_val)` (hoặc `P(fluid, h=h_val, s=s_val)`)
- `x = quality(fluid, h=h_val, P=P_val)`: Độ khô $x \in [0, 1]$ (hoặc `q(...)`)
- `v = volume(fluid, T=T_val, P=P_val)`: Thể tích riêng $v = 1/\rho$ [m³/kg]
- `rho = density(fluid, T=T_val, P=P_val)`: Khối lượng riêng [kg/m³]
- `u = internal_energy(fluid, T=T_val, P=P_val)`: Nội năng riêng [J/kg]
- `cp = cp(fluid, T, P)`, `cv = cv(fluid, T, P)`: Nhiệt dung riêng [J/(kg·K)]
- `mu = viscosity(fluid, T, P)` [Pa·s], `k = conductivity(fluid, T, P)` [W/(m·K)], `w = speed_of_sound(fluid, T, P)` [m/s]

#### Thông số điểm tới hạn & Điểm ba thể:
- `p_crit(fluid)`, `t_crit(fluid)`, `rho_crit(fluid)`, `molar_mass(fluid)`, `t_triple(fluid)`, `p_triple(fluid)`

#### Tên môi chất tự nhiên (Không cần đặt trong nháy):
Có thể dùng trực tiếp tên các môi chất phổ biến như các hằng số:
`Ammonia`, `Water`, `Steam`, `Air`, `Nitrogen`, `Oxygen`, `CarbonDioxide` (hoặc `CO2`), `Argon`, `Hydrogen`, `Helium`, `R134a`, `R32`, `R1234yf`, `R410A`, `R404A`, `R407C`, `R507A`, `Propane` (hoặc `R290`), `IsoButane` (hoặc `R600a`), `Methane`, `R22`, v.v.

### 2.6. Chú thích trong mã (Comments)
- **Chú thích nguyên dòng**: Bắt đầu bằng `<< ... >>`, `// ...` hoặc `* ...`
- **Chú thích nội dòng (Inline comment)**: Đặt ở cuối bất kỳ dòng lệnh hoặc phương trình nào:
  ```ees
  T_H = 320[K]                  << Nhiệt độ nguồn nóng >>
  eta_vol = 0.8                 // Hiệu suất thể tích máy nén
  V_disp = 0.02[m^3/s]          << Lưu lượng quét >>
  ```

### 2.7. Câu lệnh PRINT và Tab Kết quả
- In ra tab "Kết quả" sau khi giải xong:
  ```ees
  PRINT P, rho, T
  PRINT fluid$
  ```
- In kèm định dạng số (hỗ trợ cả định dạng printf và chuẩn số thập phân EES):
  ```ees
  PRINT FORMAT %.4f P, rho
  PRINT FORMAT T = 3          << Quy định 3 chữ số thập phân giống EES >>
  PRINT FORMAT P = %.2f
  ```
- In ghi trực tiếp ra tệp văn bản:
  ```ees
  PRINT TO FILE "output.txt" P, rho
  ```

### 2.8. Biến chuỗi ký tự (String Variables)
Hỗ trợ khai báo biến chuỗi cho tên môi chất nhiệt động lực học theo cú pháp chuẩn EES:
- Cú pháp với hậu tố `$`: `fluid$ = 'R134a'`
- Cú pháp với dấu bao `#`: `#ref# = 'Water'`

Biến chuỗi được tự động thay thế bằng giá trị thực tế trong:
- Các hàm tra cứu CoolProp / AirH2O: `h = enthalpy(fluid$, T=300[K], P=101325[Pa])`
- Câu lệnh in kết quả: `PRINT fluid$`
- Hệ thống tự động nhận diện và không coi tên biến chuỗi là ẩn số cần giải trong hệ phương trình.

### 2.9. Hằng số và Hàm chuyển đổi đơn vị
#### 2.9.1. Hằng số định nghĩa sẵn:
  - `g = 9.80665 m/s^2` (gia tốc trọng trường)
  - `R = 8.314462618 J/(mol*K)` (hằng số khí lý tưởng)
  - `T0 = 273.15 K`, `P0 = 101325 Pa`
  - `pi`, `e`
- Hàm chuyển đổi đơn vị trong phương trình:
  - `to(P, 'bar')`
 
#### 2.9.2. Hàm chuyển đổi đơn vị tổng quát:
Cú pháp:
```ees
factor = convert('đơn_vị_gốc', 'đơn_vị_đích')
// hoặc viết hoa CONVERT('from_unit', 'to_unit')
```
Hàm trả về hệ số nhân chuyển đổi giữa hai đơn vị cùng thứ nguyên. Hỗ trợ đầy đủ các hệ đơn vị kỹ thuật (SI, US Customary / Imperial):
- **Áp suất (Pressure)**: `Pa`, `kPa`, `MPa`, `bar`, `psi`, `atm`, `torr`, `mmHg`, `mbar`...
  - `P_Pa = P_bar * convert('bar', 'Pa')`  << 1 bar = 100,000 Pa >>
  - `P_psi = P_bar * convert('bar', 'psi')`  << 1 bar = 14.5038 psi >>
  - `P_bar = P_kPa * convert('kPa', 'bar')`  << 100 kPa = 1 bar >>
- **Công suất & Năng suất lạnh (Power & Cooling Capacity)**: `W`, `kW`, `MW`, `hp` (mã lực), `ton` hoặc `TR`/`RT` (ton lạnh), `Btu/h`...
  - `Q_ton = Q_kW * convert('kW', 'ton')`  << 1 ton lạnh = 3.51685 kW >>
  - `W_hp = W_kW * convert('kW', 'hp')`  << 1 hp = 0.7457 kW >>
  - `Q_btuh = Q_kW * convert('kW', 'Btu/h')`
- **Lưu lượng thể tích (Volumetric Flow Rate)**: `m^3/s`, `m^3/h`, `L/s`, `L/min`, `cfm` (ft³/min)...
  - `V_cfm = V_dot * convert('m^3/s', 'cfm')`  << 1 m³/s = 2118.88 cfm >>
  - `V_m3h = V_dot * convert('m^3/s', 'm^3/h')`  << 1 m³/s = 3600 m³/h >>
- **Năng lượng & Nhiệt lượng (Energy & Heat)**: `J`, `kJ`, `MJ`, `kWh`, `cal`, `kcal`, `Btu`...
  - `E_kWh = E_kJ * convert('kJ', 'kWh')`
- **Khối lượng & Chiều dài (Mass & Length)**: `kg`, `g`, `lb`, `ton`, `m`, `mm`, `cm`, `inch`, `ft`...
  - `m_lb = m_kg * convert('kg', 'lb')`
  - `L_in = L_m * convert('m', 'inch')`

#### 2.9.3. Hàm chuyển đổi thang nhiệt độ:
Cú pháp:
```ees
T_out = convert_temp('thang_gốc', 'thang_đích', giá_trị)
// hoặc viết hoa CONVERT_TEMP('from_scale', 'to_scale', val)
```
Các thang đo hỗ trợ: `'C'` (Celsius), `'K'` (Kelvin), `'F'` (Fahrenheit), `'R'` (Rankine).
- Đổi từ °C sang Kelvin:
  `T_K = convert_temp('C', 'K', T_C)` (ví dụ: `convert_temp('C', 'K', 40) = 313.15 K`)
- Đổi từ Kelvin sang °C:
  `T_C = convert_temp('K', 'C', T_K)` (ví dụ: `convert_temp('K', 'C', 300) = 26.85 °C`)
- Đổi từ °C sang Fahrenheit:
  `T_F = convert_temp('C', 'F', 25)`  << Trả về 77 °F >>
- Đổi từ Fahrenheit sang Rankine:
  `T_R = convert_temp('F', 'R', 77)`  << Trả về 536.67 °R >>

### 2.10. Thư viện 101 Hàm Toán học & Hằng số Kỹ thuật
Ncal tích hợp bộ thư viện 101 hàm toán học và hằng số kỹ thuật toàn diện. Các hàm tự động tương thích với thứ nguyên (bảo toàn đơn vị hoặc tự quy đổi góc), được phân thành 10 nhóm chính:

1. **Lượng giác thuận**: `sin`, `cos`, `tan`, `cot`, `sec`, `csc`.
   - Tuân thủ chế độ góc hệ thống (`UnitsSystem.angle`: degree hoặc radian) và tự nhận diện đơn vị Pint (ví dụ `sin(30[deg])` hoặc `sin(pi/6[rad])`).
   - Cung cấp hàm tường minh độ: `sind`, `cosd`, `tand`, `cotd`, `secd`, `cscd` (luôn dùng độ).
   - Cung cấp hàm tường minh radian: `sinr`, `cosr`, `tanr`, `cotr`, `secr`, `cscr` (luôn dùng radian).
2. **Lượng giác ngược**: `asin`, `acos`, `atan`, `atan2`, `acot`, `asec`, `acsc`.
   - Kết quả trả về tự động theo chế độ góc đang cấu hình.
   - Cung cấp hàm tường minh độ: `asind`, `acosd`, `atand`, `atan2d`, `acotd`, `asecd`, `acscd` (luôn trả về độ).
   - Cung cấp hàm tường minh radian: `asinr`, `acosr`, `atanr`, `atan2r`, `acotr`, `asecr`, `acscr` (luôn trả về radian).
3. **Hyperbolic & Hyperbolic ngược**:
   - `sinh`, `cosh`, `tanh`, `coth`, `sech`, `csch`.
   - `asinh`, `acosh`, `atanh`, `acoth`, `asech`, `acsch`.
4. **Mũ & Logarit**:
   - `exp(x)` ($e^x$), `expm1(x)` ($e^x - 1$).
   - `ln(x)` (logarit tự nhiên cơ số $e$).
   - `log(x)` (chuẩn EES: mặc định cơ số 10) hoặc `log(x, base)` khi chỉ định cơ số.
   - `log10(x)`, `log2(x)`, `log1p(x)`.
5. **Lũy thừa, Căn thức & Khoảng cách**:
   - `sqrt(x)`: Bảo toàn đơn vị Pint (ví dụ: `sqrt(16[m^2]) = 4[m]`).
   - `cbrt(x)`: Căn bậc ba số thực engineering, hỗ trợ chính xác cả số âm thực ($\sqrt[3]{-27} = -3$) và bảo toàn đơn vị Pint (`cbrt(-8[m^3]) = -2[m]`).
   - `root(x, n)` / `nthroot`: Căn bậc $n$ của $x$.
   - `hypot(x, y, ...)`: Khoảng cách Euclidean $\sqrt{x^2 + y^2}$, hỗ trợ bảo toàn đơn vị Pint.
   - `sqr(x)` / `square`: Bình phương $x^2$ (hàm SQR chuẩn EES).
   - `quadsum(x, y, ...)`: Tổng bình phương các phần tử.
6. **Làm tròn, Dấu & Số học nguyên**:
   - `abs(x)` / `safe_abs`: Giá trị tuyệt đối, bảo toàn đơn vị Pint (ví dụ: `abs(-5[kW]) = 5[kW]`).
   - `sign(x)` / `sgn`: Hàm dấu (trả về 1, -1 hoặc 0).
   - `round(x, n)` / `safe_round`: Làm tròn đến $n$ chữ số, bảo toàn đơn vị Pint.
   - `floor(x)`, `ceil(x)`, `trunc(x)`, `frac(x)` (phần phân số $x - \text{trunc}(x)$).
   - `mod(x, y)`, `rem(x, y)`, `gcd(a, b)`, `lcm(a, b)`.
7. **Bước nhảy & Kỹ thuật điều khiển**:
   - `step(x)`: Hàm bước Heaviside ($1$ nếu $x \ge 0$, ngược lại $0$).
   - `ramp(x)`: Hàm dốc ($\max(0, x)$).
   - `clamp(x, lo, hi)` / `clip`: Giới hạn $x$ trong đoạn $[\text{lo}, \text{hi}]$, bảo toàn đơn vị Pint.
   - `smooth_step(x, edge0, edge1)`: Hàm bước chuyển tiếp mượt mà bậc 3 (Hermite cubic).
   - `if_then(cond, true_val, false_val)` / `ifelse`: Hàm điều kiện nội tuyến 3 tham số.
8. **Giải tích đặc biệt & Tổ hợp**:
   - `factorial(n)` / `fact`: Giai thừa $n!$.
   - `gamma(x)`, `lgamma(x)`: Hàm Gamma và Log-gamma.
   - `erf(x)`, `erfc(x)`: Hàm sai số Gauss và sai số bù.
   - `comb(n, k)`, `perm(n, k)`: Tổ hợp và chỉnh hợp.
   - `bessel_j0(x)`, `bessel_j1(x)`, `bessel_y0(x)`, `bessel_y1(x)`: Hàm Bessel loại 1 và loại 2 cấp 0 & 1.
9. **Thống kê & Mảng số liệu**:
   - `sum(...)`, `min(...)`, `max(...)`, `avg(...)` / `mean`, `size(...)`.
   - `std(...)` / `stdev`: Độ lệch chuẩn mẫu.
   - `var(...)` / `variance`: Phương sai mẫu.
   - `median(...)`: Giá trị trung vị.
   - `polyval(coeffs, x)`: Tính giá trị đa thức $c_0 x^n + \dots + c_n$ tại $x$.
   - `interp(x, x_pts, y_pts)`: Nội suy tuyến tính 1D giá trị $y$ tại $x$.
10. **Đổi góc & Hằng số vật lý**:
    - `degrees(rad)` / `deg`, `radians(deg)` / `rad`.
    - `pi` ($3.14159265\dots$), `e` ($2.7182818\dots$).
    - `g` ($9.80665\text{ m/s}^2$), `R` ($8.3144626\text{ J/(mol}\cdot\text{K)}$).
    - `T0` ($273.15\text{ K}$), `P0` ($101325.0\text{ Pa}$).
---

## 3. Các tính năng chính

### 3.1. Tạo file mới với cơ chế hỏi lưu tự động (New File / Ctrl+N)
- Phím tắt: `Ctrl+N` hoặc menu **Tệp > Tạo file mới** (icon New trên Toolbar).
- Kiểm tra trạng thái chỉnh sửa (`dirty flag`):
  - Nếu file hiện tại đã có thay đổi chưa được lưu, Ncal sẽ hiển thị hộp thoại xác nhận:
    - **Lưu (Save)**: Lưu các thay đổi vào file hiện tại trước khi tạo file mới.
    - **Không lưu (Discard)**: Xóa thay đổi và mở trang soạn thảo trắng.
    - **Hủy (Cancel)**: Hủy bỏ thao tác tạo mới, giữ nguyên hiện trạng làm việc.
  - Khi tạo mới: Reset toàn bộ phương trình, tham số, bảng biến, bảng nghiệm, kết quả PRINT và đặt lại tiêu đề cửa sổ.

### 3.2. Công cụ Chèn hàm thuộc tính CoolProp & Danh mục môi chất (Ctrl+Shift+P)
- Menu: **Chèn > Chèn hàm thuộc tính CoolProp...** hoặc phím tắt `Ctrl+Shift+P`.
- Gồm 3 tab chuyên dụng:
  1. **Chèn hàm (Insert Function)**:
     - Phân loại trực quan: Áp suất & Nhiệt độ bão hòa, Enthalpy bão hòa, Entropy bão hòa, Thể tích & Khối lượng riêng, Trạng thái 2 thông số độc lập, Thông số vận chuyển & Tới hạn.
     - Chọn hàm và chọn môi chất từ danh sách dropdown hoặc gõ tìm kiếm nhanh.
     - Nhập giá trị các thông số đầu vào với gợi ý thứ nguyên đơn vị SI.
     - Xem trước cú pháp thời gian thực (`P = P_sat(Ammonia, T)`) và 1-click chèn vào vị trí con trỏ soạn thảo.
  2. **Bảng thông số tra cứu (Property Reference)**:
     - Tra cứu toàn diện tất cả các hàm thuộc tính kèm ký hiệu, đơn vị đo chuẩn SI, cú pháp mẫu và mô tả chi tiết.
     - Nút "Sử dụng hàm này" tự động chuyển sang tab chèn hàm với hàm đã chọn.
  3. **Danh mục môi chất lạnh (Refrigerants Catalog)**:
     - Tra cứu thông số hơn 30 môi chất lạnh thông dụng: Nước & hơi nước, Khí công nghiệp, HFC, HFO thế hệ mới, Hydrocarbon tự nhiên, Hỗn hợp R400/R500.
     - Cung cấp: Công thức hóa học, nhóm môi chất, phân loại an toàn ASHRAE, nhiệt độ sôi chuẩn $T_{\text{boil}}$, nhiệt độ tới hạn $T_{\text{crit}}$, áp suất tới hạn $P_{\text{crit}}$, khối lượng mol và mô tả ứng dụng thực tế.
     - Nút "Chọn môi chất này" tự động nạp môi chất vào công cụ tạo hàm.

### 3.3. Kiểm tra, Kế thừa và Hiển thị Thứ nguyên Kỹ thuật Chuẩn (Unit Propagation & Symbols)
- **Chuẩn hóa ký hiệu thứ nguyên kỹ thuật**:
  - Chuyển đổi tên đơn vị dài dòng sang dạng ký hiệu kỹ thuật quen thuộc theo chuẩn quốc tế:
    - `kelvin` $\rightarrow$ `K`, `celsius` / `degree_Celsius` $\rightarrow$ `°C`
    - `pascal` $\rightarrow$ `Pa`, `kilopascal` $\rightarrow$ `kPa`, `megapascal` $\rightarrow$ `MPa`, `bar` $\rightarrow$ `bar`
    - `joule / kilogram` $\rightarrow$ `J/kg`, `kilojoule / kilogram` $\rightarrow$ `kJ/kg`
    - `joule / kelvin / kilogram` $\rightarrow$ `J/(kg·K)`
    - `meter ** 3 / kilogram` $\rightarrow$ `m³/kg`, `kilogram / meter ** 3` $\rightarrow$ `kg/m³`
    - `kilogram / second` $\rightarrow$ `kg/s`, `meter ** 3 / second` $\rightarrow$ `m³/s`
    - `watt` $\rightarrow$ `W`, `kilowatt` $\rightarrow$ `kW`
  - **Ký hiệu `[-]` cho đại lượng không thứ nguyên**: Mọi đại lượng không thứ nguyên (`dimensionless` như `COP`, `EER`, hiệu suất $\eta$, độ khô $x$) đều được hiển thị thống nhất là `[-]` (chuẩn EES và bài báo kỹ thuật).
  - **Áp dụng đồng bộ trên toàn bộ giao diện và xuất báo cáo**:
    - **Tab Biến & Tab Nghiệm**: Cột Đơn vị hiển thị gọn gàng (`K`, `Pa`, `J/kg`, `[-]`).
    - **Tab Bảng mảng (Arrays Table)**: Header cột mảng hiển thị trực quan `T [K]`, `P [Pa]`, `h [J/kg]`, `s [J/(kg·K)]`, `v [m³/kg]`, `x [-]`.
    - **Tab Kết quả & PRINT**: `COP = 3.3711 [-]`, `m_dot = 0.027415 [kg/s]`.
    - **Xuất file CSV / Excel (.xlsx) / ODT / LaTeX (.tex)**: Giữ nguyên ký hiệu kỹ thuật chuẩn và `[-]`.
- **Kế thừa thứ nguyên khi gán biến**:
  - Khi một biến/tham số đã có thứ nguyên và biến khác được gán bằng biến đó (ví dụ: `T = 300[K]`, `T1 = T`), biến được gán sẽ tự động kế thừa cùng giá trị và thứ nguyên (`T1` nhận `K`).
- **Tự động gán thứ nguyên từ hàm CoolProp**:
  - Các biến tính từ hàm CoolProp tự động nhận thứ nguyên chuẩn SI tương ứng mà không cần gõ chú thích thủ công:
    - `P_sat`, `pressure`, `p_crit` $\rightarrow$ `Pa`
    - `T_sat`, `temperature`, `t_crit` $\rightarrow$ `K`
    - `h_sat_liq`, `h_sat_vap`, `enthalpy`, `internal_energy` $\rightarrow$ `J/kg`
    - `s_sat_liq`, `s_sat_vap`, `entropy`, `cp`, `cv` $\rightarrow$ `J/(kg·K)`
    - `v_sat_vap`, `v_sat_liq`, `volume` $\rightarrow$ `m³/kg`
    - `rho_sat_liq`, `rho_sat_vap`, `density` $\rightarrow$ `kg/m³`
    - `viscosity` $\rightarrow$ `Pa·s`, `conductivity` $\rightarrow$ `W/(m·K)`, `quality` $\rightarrow$ `[-]`
- **Lan truyền thứ nguyên theo quy tắc số học ($+, -, *, /, **$)**:
  - Khi các biến tham gia phép toán có thứ nguyên, kết quả sẽ tự động tính thứ nguyên và rút gọn thành đơn vị kỹ thuật chuẩn (`simplify_unit`):
    - Áp suất thủy tĩnh: $P = \rho \cdot g \cdot H \implies \text{Pa}$
    - Hiệu enthalpy: $q_{\text{cond}} = h_3 - h_4 \implies \text{J/kg}$
    - Lưu lượng khối lượng: $\dot{m} = \eta_{\text{vol}} \cdot \dot{V}_{\text{disp}} / v_2 \implies \text{kg/s}$
    - Công suất / Nhiệt lượng: $\dot{Q} = q \cdot \dot{m} \implies \text{W}$
    - Tỷ số năng lượng / COP: $\text{COP} = \dot{Q} / \dot{W} \implies \text{[-] }$
  - Suy luận ngược cho ẩn số trong hệ phương trình:
    - $W = F \cdot d \implies F$ nhận đơn vị $\text{N}$
    - $\dot{m} = \dot{V} / v \implies v$ nhận đơn vị $\text{m³/kg}$
    - $E_k = 0.5 \cdot m \cdot v^2 \implies v$ nhận đơn vị $\text{m/s}$
- **Kiểm tra nhất quán và Báo lỗi chi tiết**:
  - Báo lỗi chi tiết theo dòng: `"dòng %d: sai khớp đơn vị: lhs=%s, rhs=%s"`.
  - Mặc định **chặn giải** khi có sai lệch đơn vị (có thể bật "Bỏ qua kiểm tra đơn vị" trong Cài đặt).
  - Menu: **Công cụ > Kiểm tra đơn vị...** hiển thị danh sách lỗi và hỗ trợ click đúp để nhảy ngay tới dòng vi phạm trong editor.

### 3.4. Bảng mảng chuyên dụng 
- Tự động nhận diện và trích xuất tất cả các biến mảng và tham số mảng có dạng `name[index]` (ví dụ: `P[1..4]`, `h[1..4]`, `s[1..4]`, `T[1..4]`, `v[2]`, `x[1]`).
- Hiển thị tab chuyên dụng **"Bảng mảng" (Arrays Table)** ngay cạnh tab Nghiệm:
  - Cột đầu tiên: Chỉ số trạng thái mảng `[1]`, `[2]`, `[3]`, `[4]`.
  - Các cột tiếp theo: Gom nhóm các thông số trạng thái kèm ký hiệu thứ nguyên kỹ thuật chuẩn (ví dụ: `T [K]`, `P [Pa]`, `h [J/kg]`, `s [J/(kg·K)]`, `v [m³/kg]`, `x [-]`).
  - Hàng tương ứng hiển thị trực quan các giá trị đã tính toán/giải của từng điểm trạng thái chu trình.
- **Bảo toàn hiển thị đầy đủ trên mọi tab**:
  - Tab **"Biến"**: Liệt kê cả Biến phi tuyến (Variables) và Tham số đầu vào (Parameters, đánh dấu `[Tham số]`).
  - Tab **"Nghiệm"**: Liệt kê đầy đủ giá trị cuối cùng của tất cả các biến và tham số sau khi giải kèm ký hiệu đơn vị hoặc `[-]`.

### 3.5. Import file EES đầy đủ
- Tự động nhận diện và chuyển đổi hàm nhiệt động lực học dạng vị trí EES:
  - `D(T, P, Water)` $\rightarrow$ `prop('D', 'T', T, 'P', P, 'Water')`
  - `H(T, P, Water)`, `S(...)`, `Cp(...)`, `Cv(...)`, `U(...)`, `Z(...)`
  - `V(T, P, Water)` $\rightarrow$ thể tích riêng $v = 1/\rho$ (`prop('VOLUME', ...)`).
- Chuyển đổi và mở rộng thật các khối `FOR / WHILE / CASE / IF-ELSE`.
- Nhận biết chỉ thị hệ đơn vị: `$UNITSYSTEM SI` hoặc cảnh báo với `$UNITSYSTEM US`.

### 3.6. Đồ thị kỹ thuật (GraphDialog: logP-h, T-s, y=f(x))
- **Hộp thoại Đồ thị (Phím tắt Ctrl+Shift+P / Toolbar "Đồ thị")**:
  - Hỗ trợ 3 tab đồ thị chuyên dụng: **logP-h**, **T-s**, và **Đồ thị chung $y = f(x)$**.
  - Tiêu đề cửa sổ popup và thanh Taskbar hiển thị ngắn gọn, nhất quán: **"Đồ thị"** (tiếng Anh: **"Plot"**).
- **Biểu đồ logP-h**:
  - Tự động nhận diện môi chất nhiệt động lực học đang sử dụng trong bài toán (ví dụ: `Ammonia`, `R134a`, `Water`...).
  - Tự động thu thập các điểm mảng `P` và `h` (kể cả khi ở dạng tham số hoặc biến) để vẽ lên biểu đồ.
  - Tự động vẽ đường bão hòa lỏng / hơi (CoolProp) cho môi chất tương ứng.
  - Tùy chọn đơn vị áp suất: **Mặc định hiển thị theo `bar`** (tùy chọn chuyển đổi sang `Pa`), enthalpy theo kJ/kg hoặc J/kg, đồng bộ chuẩn xác với cột `P (bar)` trong bảng thông số điểm chu trình.
  - Tùy chọn nối chu trình khép kín ($1 \to 2 \to 3 \to 4 \to 1$) và hiển thị nhãn số thứ tự điểm trạng thái.
  - **Tính năng vẽ chu trình theo thứ tự tùy chọn (Custom connection path)**: Cho phép kết nối các điểm trạng thái theo thứ tự bất kỳ, hỗ trợ nhiều đường/nhánh (multiline). Mỗi dòng định nghĩa một chuỗi điểm cách nhau bằng dấu phẩy, kèm tùy chọn màu sắc và kiểu nét linh hoạt qua dấu `:` (ví dụ: `1,2,3,5:blue:line`, `3,5,6,1:red:dash`, hoặc tiếng Việt `1,2,3,5:xanh nước biển:nét liền`, `3,5,6,1:nét đứt:đỏ`, mã hex `#00aa55:dot`).
  - Tích hợp bảng tóm tắt thông số các điểm chu trình (P, h, T, s) ngay trong giao diện đồ thị.
  - Xuất đồ thị sang file ảnh vector **SVG** hoặc file **PNG** chất lượng cao (300 DPI).
- **Biểu đồ T-s (Temperature - Entropy)**:
  - Tự động nhận diện biến mảng nhiệt độ `T` và entropy `s`.
  - Tùy chọn hiển thị nhiệt độ theo °C hoặc K, entropy theo kJ/(kg·K) hoặc J/(kg·K).
  - Tự động vẽ đường cong bão hòa lỏng/hơi (Saturation dome) cho môi chất trên hệ tọa độ T-s.
  - Tương tự biểu đồ logP-h, hỗ trợ nối chu trình mặc định hoặc vẽ theo thứ tự tùy chọn bất kỳ với màu sắc và kiểu nét tùy biến.
  - Tự động đồng bộ hóa thông minh tên môi chất và nội dung chuỗi nối điểm hai chiều giữa Tab logP-h và Tab T-s.
  - Tích hợp bảng tóm tắt thông số các điểm trạng thái (Point, T, s, P, h).
- **Đồ thị tổng quát $y = f(x)$**:
  - Tùy chọn biểu thức trục $x$, $y$.
  - Trục tỷ lệ Logarithmic hoặc Linear.
  - Hiển thị lưới (Grid), Chú giải (Legend), Nhãn điểm (Data labels).

### 3.7. Phân tích độ nhạy (Sensitivity Analysis)
- Menu: **Công cụ > Phân tích độ nhạy...**
- Chọn tham số cần quét (sweep parameter), dải giá trị (Start .. Stop), số bước quét (Linear hoặc Log).
- Chọn các biến mục tiêu để khảo sát.
- Bảng kết quả định dạng cột tự động được xuất vào tab **Kết quả**.

### 3.8. Tìm nhiều nghiệm (Multiple Solutions)
- Tùy chọn kích hoạt trong cài đặt hoặc khi giải hệ phi tuyến bậc cao ($x^2 = 1$).
- Thuật toán tự động tìm kiếm các nghiệm cách biệt với ngưỡng $\epsilon = 10^{-4} \cdot \max(1, |v|)$.
- Hộp thoại chọn nghiệm: Cho phép xem bảng so sánh tất cả các nghiệm và áp dụng nghiệm mong muốn làm giá trị khởi tạo.

### 3.9. Báo cáo và Xuất dữ liệu
- Xuất kết quả giải ra:
  - **CSV** (`.csv` utf-8 BOM)
  - **Excel** (`.xlsx` định dạng màu chuyên nghiệp qua openpyxl)
  - **Text** (`.txt` toàn bộ buffer tab Kết quả / PRINT)
  - **OpenDocument Text** (`.odt`)
  - **LaTeX / PDF** (`.tex`)
- Lưu/mở file dự án **.nc** (container Zip chứa `src/eqns.txt`, `vars.json`, `meta.json`, `functions.json` và `diagram.json`):
  - Hỗ trợ format version 2 lưu trữ cấu trúc tham số (parameters).
  - Tích hợp sơ đồ hệ thống kỹ thuật vào file dự án `.nc`.
  - Tương thích ngược 100% với các file format version 1 cũ.

### 3.10. Cửa sổ Sơ đồ hệ thống (System Diagram Window)
Cửa sổ sơ đồ kỹ thuật trực quan:
- **Tích hợp Dock linh hoạt**: Có thể gắn cạnh ô soạn thảo hoặc kéo ra màn hình phụ thứ hai.
- **Hai chế độ hoạt động**: Chế độ **Chạy (Run Mode)** khóa layout và mở tương tác tham số; chế độ **Thiết kế (Design Mode)** mở bảng thuộc tính và thư viện ký hiệu kéo-thả.
- **Thư viện ký hiệu kỹ thuật vector SVG**: 70 thiết bị chuẩn gồm Máy nén (compressor), Dàn ngưng (condenser), Dàn bay hơi (evaporator), Van tiết lưu (expansion valve), Bơm (pump), Tuabin (turbine), Bộ trao đổi nhiệt 4 cổng (heat exchanger), Nồi hơi (boiler), Bình chứa/tách lỏng (tank), Quạt (fan), Nút giao (junction), Nguồn/đích dòng (source/sink), Mũi tên nhiệt và công ($Q, W$).
- **Đường ống định tuyến trực giao Manhattan**: Tự động tìm đường đi vuông góc giữa các cổng thiết bị, hiển thị mũi tên dòng chảy và hỗ trợ tùy chỉnh điểm uốn thủ công (*waypoints*).
- **Ô tham số tương tác (ParamItem)**: Thanh trượt `QSlider` kết hợp ô số `QDoubleSpinBox`, liên kết 2 chiều với dòng `tên = số`, chống rung debounce 300ms, giải ngầm mượt mà và bảo toàn ngăn xếp hoàn tác `Ctrl+Z`.
- **Nhãn giá trị (TagItem) & Huy hiệu trạng thái (StateBadgeItem)**:
  - Tự động gắn kết với các biến và tham số trong mô hình Ncal (kể cả biến mảng dạng $T[n], P[n], h[n]$).
  - Tự động chuyển đổi đơn vị thông minh (ví dụ từ $Pa \to bar$, $K \to ^\circ\text{C}$).
  - Cập nhật giá trị tức thời sau khi giải.
  - Cơ chế nhận diện trạng thái cũ (*stale*) khi sửa code và đánh dấu cảnh báo nếu biến chưa tồn tại.
- **Tương thích giao diện & Đa nền tảng**: Tự động chuyển đổi màu theo Theme sáng/tối, hỗ trợ song ngữ Việt / Anh.
- **Xuất sơ đồ đa định dạng**: Xuất trực tiếp sang ảnh raster **PNG** (2x, hỗ trợ nền trong suốt), vector **SVG** độ nét vô hạn, và tài liệu in ấn **PDF**.
- **Chọn khung chữ nhật (RubberBandDrag - C1)**: Kéo chuột trên vùng trống của sơ đồ ở chế độ Thiết kế để chọn đồng thời nhiều thiết bị, nhãn và đường ống; tương thích mượt mà với Space-pan.
- **Đặt lại đường ống tự động (Reset Waypoints - C3)**: 1-click chuột phải hoặc bấm nút trên bảng thuộc tính để đưa đường ống uốn lượn về định tuyến trực giao tự động chuẩn, hỗ trợ đầy đủ Hoàn tác (`Ctrl+Z`) và Làm lại (`Ctrl+Y`).
- **Phím tắt độc lập (A2)**: Các phím tắt xoay (`R`), lật (`H`/`V`), nhân bản (`Ctrl+D`), xóa (`Delete`) hoạt động tin cậy và không xung đột khi nhập liệu ô tham số.
- **Cập nhật tham số im lặng (A4)**: Kéo thanh trượt tham số giải lại êm ái dưới nền, không bật modal thông báo gây gián đoạn trải nghiệm người dùng.
- **Hướng dẫn chi tiết**: Xem thêm tài liệu với các ví dụ chu trình hoàn chỉnh (`refrigeration_cycle.nc`, `rankine_cycle.nc`, `brayton_cycle.nc`).

### 3.11. Bộ 16 Ví dụ Mẫu Nhiệt Động Lực Học Tiêu Chuẩn & 4 Dự Án Chu Trình Mẫu (Standard Examples Suite)
Phiên bản Beta 5.0.3 trang bị bộ 16 ví dụ chuẩn hóa công nghiệp tại thư mục `examples/`, được kiểm chứng độc lập và tuân thủ hệ đơn vị chuẩn kỹ thuật: **°C · kPa · kg · kJ**:

| Ví dụ | Tiêu đề kỹ thuật | Môi chất | Tệp dự án | Tài liệu kỹ thuật |
|:---|:---|:---:|:---:|:---:|
| **ex01** | Chu trình máy lạnh nén hơi 1 cấp tiêu chuẩn | R134a | [ex01.nc](examples/ex01_refrigeration_1stage.nc) | [ex01.md](examples/ex01_refrigeration_1stage.md) |
| **ex02** | Chu trình máy lạnh có quá lạnh lỏng và quá nhiệt hơi | R134a | [ex02.nc](examples/ex02_refrigeration_1stage_subcooler.nc) | [ex02.md](examples/ex02_refrigeration_1stage_subcooler.md) |
| **ex03** | Chu trình 2 cấp nén nối tiếp làm mát trung gian | R134a | [ex03.nc](examples/ex03_refrigeration_2stage_series.nc) | [ex03.md](examples/ex03_refrigeration_2stage_series.md) |
| **ex04** | Chu trình 2 cấp có bình tách flash gas trung gian | R134a | [ex04.nc](examples/ex04_refrigeration_2stage_parallel.nc) | [ex04.md](examples/ex04_refrigeration_2stage_parallel.md) |
| **ex05** | Chu trình máy lạnh hấp thụ Ammonia | Ammonia | [ex05.nc](examples/ex05_absorption_refrigerator.nc) | [ex05.md](examples/ex05_absorption_refrigerator.md) |
| **ex06** | Chu trình Rankine cơ bản hơi bão hòa | Water | [ex06.nc](examples/ex06_rankine_basic.nc) | [ex06.md](examples/ex06_rankine_basic.md) |
| **ex07** | Chu trình Rankine hơi quá nhiệt 7 MPa / 500 °C | Water | [ex07.nc](examples/ex07_rankine_superheated.nc) | [ex07.md](examples/ex07_rankine_superheated.md) |
| **ex08** | Chu trình Rankine hồi nhiệt gia nhiệt hở OFWH | Water | [ex08.nc](examples/ex08_rankine_regeneration.nc) | [ex08.md](examples/ex08_rankine_regeneration.md) |
| **ex09** | Thiết kế bộ trao đổi nhiệt ngược chiều theo LMTD | Water | [ex09.nc](examples/ex09_heat_exchanger_lmtd.nc) | [ex09.md](examples/ex09_heat_exchanger_lmtd.md) |
| **ex10** | Ảnh hưởng nhiệt trở bám cặn Fouling lên diện tích | Water | [ex10.nc](examples/ex10_heat_exchanger_fouling.nc) | [ex10.md](examples/ex10_heat_exchanger_fouling.md) |
| **ex11** | Tính toán phụ tải nhiệt tổng hợp cho kho lạnh | Không khí + SP | [ex11.nc](examples/ex11_cold_storage_load.nc) | [ex11.md](examples/ex11_cold_storage_load.md) |
| **ex12** | Cân bằng nhiệt dàn bay hơi và chu kỳ xả đá định kỳ | R134a | [ex12.nc](examples/ex12_cold_storage_evaporator.nc) | [ex12.md](examples/ex12_cold_storage_evaporator.md) |
| **ex13** | Chu trình tuabin khí Brayton có hiệu suất nén/tuabin | Air | [ex13.nc](examples/ex13_brayton_cycle.nc) | [ex13.md](examples/ex13_brayton_cycle.md) |
| **ex14** | So sánh chu trình Carnot lý tưởng và Brayton thực tế | Air | [ex14.nc](examples/ex14_carnot_ideal.nc) | [ex14.md](examples/ex14_carnot_ideal.md) |
| **ex15** | Chu trình hỗn hợp Khí - Hơi CCGT (Brayton + Rankine) | Air + Water | [ex15.nc](examples/ex15_combined_cycle.nc) | [ex15.md](examples/ex15_combined_cycle.md) |
| **ex16** | Hệ thống điều hòa không khí (Air Conditioning System) với AirH2O | Air + Water | [ex16.nc](examples/ex16_air_conditioning.nc) | [ex16.md](examples/ex16_air_conditioning.md) |

#### 4 Dự án chu trình nhiệt động tích hợp sơ đồ kỹ thuật (Diagram):
Bên cạnh các ví dụ từ `ex01` đến `ex16`, Ncal đi kèm 4 tệp dự án chu trình hoàn chỉnh tích hợp sẵn giao diện sơ đồ thiết bị tương tác:

| Dự án | Tiêu đề chu trình | Môi chất | Tệp dự án .nc | Mô tả |
|:---|:---|:---:|:---:|:---|
| **refrigeration_cycle** | Chu trình máy lạnh nén hơi hoàn chỉnh | R134a | [refrigeration_cycle.nc](examples/refrigeration_cycle.nc) | Sơ đồ đầy đủ máy nén, dàn ngưng, van tiết lưu, bay hơi kèm Tag và Slider tương tác. |
| **rankine_cycle** | Chu trình nhiệt điện Rankine hơi nước | Water | [rankine_cycle.nc](examples/rankine_cycle.nc) | Sơ đồ lò hơi, tuabin hơi, bình ngưng, bơm cấp nước có liên kết tham số trực quan. |
| **brayton_cycle** | Chu trình tuabin khí Brayton hở | Air | [brayton_cycle.nc](examples/brayton_cycle.nc) | Sơ đồ máy nén khí, buồng đốt, tuabin khí và phụ tải điện. |
| **helloNcal** | Chu trình lạnh nén hơi Amoniac tiêu chuẩn | Ammonia | [helloNcal.nc](helloNcal.nc) | Tệp dự án xây dựng từ ví dụ giáo trình Klein & Nellis ([Hello_Ncal.txt](Hello_Ncal.txt)). |

---

## 4. An toàn môi trường thực thi (Security Sandbox)
Mọi biểu thức toán học và hàm người dùng được thực thi trong môi trường sandbox cô lập:
- `__builtins__` được hạn chế nghiêm ngặt chỉ gồm các hàm an toàn: `float, int, abs, max, min, pow, len, round, True, False, None`.
- Bộ lọc `_DUNDER_GUARD_RE` chặn đứng mọi nỗ lực truy cập dunder attribute (`__class__`, `__bases__`, `__subclasses__`, `__globals__`, `__code__`, `__import__`).
- Đã được kiểm chứng qua bộ test `tests/test_security.py`.

---

## 5. Giấy phép
Bản quyền © 2026 Nguyễn Bá Chiến. Phát hành miễn phí theo Giấy phép Người dùng Cuối (EULA - Freeware) phục vụ cá nhân, giáo dục, nghiên cứu và sản xuất nội bộ doanh nghiệp. Chi tiết xem tại [LICENSE.txt](LICENSE.txt).
