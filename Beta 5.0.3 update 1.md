## Phiên bản Beta 5.0.3 update 1
### Mã băm xác thực toàn vẹn (Checksums SHA-256):
- **Tệp nén `Ncal-windows.zip`**:
  `bd1bddac2c827ff59a673fde60a3cf71bb2b85ddbfe8d2d11c80d663e4d4b3b2`
- **Tệp thực thi `Ncal.exe`**:
  `fb83bf3fa559a0a231beb445dcbb9feafe0686e6700acc21a19f28a1c902c49d`

### Hàm Chuyển đổi Đơn vị (convert, convert_temp) & Cảnh báo Dòng Không Hội tụ
- **Triển khai hàm chuyển đổi hệ đơn vị `convert`**:
  - Cú pháp `convert('from_unit', 'to_unit')` (hoặc `CONVERT`, `convert_units`, `CONVERT_UNITS`).
  - Hỗ trợ quy đổi trực tiếp và chính xác giữa các đơn vị kỹ thuật: áp suất (`bar`, `Pa`, `kPa`, `MPa`, `psi`, `atm`), công suất & tấn lạnh (`W`, `kW`, `MW`, `hp`, `ton`/`TR`/`RT`, `Btu/h`), lưu lượng (`m^3/s`, `cfm`, `m^3/h`), năng lượng, v.v.
- **Triển khai hàm chuyển đổi thang nhiệt độ `convert_temp`**:
  - Cú pháp `convert_temp('from_scale', 'to_scale', val)` (hoặc `CONVERT_TEMP`).
  - Hỗ trợ đầy đủ 4 thang đo nhiệt độ: `'C'` (Celsius), `'K'` (Kelvin), `'F'` (Fahrenheit), `'R'` (Rankine).
  - Tự động nhận diện và xử lý cả giá trị số thuần túy và đối tượng `Quantity` mang đơn vị nhiệt độ Pint.
  - Tích hợp vào danh mục hàm `MATH_FUNCTIONS_CATALOG`, môi trường `make_eval_env` của bộ giải, và `_build_unit_math_env` của công cụ kiểm tra đơn vị.
- **Tính năng Chẩn đoán & Cảnh báo Vị trí Không Hội tụ **:
  - Khi solver không thể hội tụ bộ giải tự động phân tích vector sai lệch và xác định chỉ số dòng trong mã nguồn.
  - Hiển thị thông báo rõ ràng cho người dùng: `"Hãy kiểm tra dòng xx."` (tiếng Việt) hoặc `"Please check line xx."` (tiếng Anh).
  - Cập nhật GUI: Tự động điều hướng con trỏ soạn thảo và bôi sáng chính xác dòng phương trình bị nghi vấn ngay khi xuất hiện hộp thoại cảnh báo.

### Thư viện 101 Hàm Toán học 
- **Triển khai 101 hàm toán học và hằng số vật lý**: Hỗ trợ toàn diện 10 nhóm phân loại chuẩn hóa:
  1. *Lượng giác chuẩn, độ & radian*: `sin`, `cos`, `tan`, `cot`, `sind`, `cosd`, `tand`, `cotd`, `sinr`, `cosr`, `tanr`, `cotr`, `sinc`.
  2. *Lượng giác ngược*: `asin`, `acos`, `atan`, `atan2`, `acot`, `asind`, `acosd`, `atand`, `atan2d`, `acotd`, `asinr`, `acosr`, `atanr`, `atan2r`, `acotr`.
  3. *Hyperbolic*: `sinh`, `cosh`, `tanh`, `coth`, `sech`, `csch`, `asinh`, `acosh`, `atanh`, `acoth`.
  4. *Mũ & Logarit*: `exp`, `exp2`, `expm1`, `ln`, `log`, `log10`, `log2`, `log1p`, `logistic`.
  5. *Lũy thừa, Căn & Khoảng cách*: `sqrt`, `cbrt`, `hypot`, `pow`, `sqr`, `root`, `dist`.
  6. *Làm tròn, Dấu & Số học nguyên*: `abs`, `safe_abs`, `sign`, `signum`, `round`, `safe_round`, `floor`, `ceil`, `trunc`, `frac`, `rem`, `mod`, `gcd`, `lcm`.
  7. *Kỹ thuật bước nhảy & Từng đoạn*: `step`, `ramp`, `pulse`, `rect`, `tri`, `heaviside`, `clamp`, `lerp`, `smoothstep`.
  8. *Hàm đặc biệt & Tổ hợp*: `erf`, `erfc`, `gamma`, `lgamma`, `factorial`, `comb`, `perm`.
  9. *Thống kê & Mảng*: `arr_min`, `arr_max`, `arr_sum`, `mean`, `average`, `median`, `std`, `var`, `rms`, `size`, `poly`.
  10. *Chuyển đổi góc & Hằng số*: `deg2rad`, `rad2deg`, `pi`, `e`, `tau`, `inf`, `nan`, `R_u`, `g_acc`, `k_B`, `N_A`, `sigma_sb`.
- **Tương thích hoàn hảo với đơn vị đo**:
  - Bảo toàn và lan truyền thứ nguyên vật lý đối với các hàm có đơn vị (`sqrt(16 m^2) = 4 m`, `cbrt(-27 m^3) = -3 m`, `abs(-5 kW) = 5 kW`, `clamp(15 m, 0 m, 10 m) = 10 m`, `hypot(3 m, 4 m) = 5 m`).
  - Tôn trọng cấu hình hệ góc `UnitsSystem.angle` (degree/radian) cho các hàm lượng giác chuẩn (`sin`, `cos`, `tan`, `asin`,...), cùng các biến thể tường minh độ (`sind`, `cosd`,...) và radian (`sinr`, `cosr`,...).
  - Tránh đệ quy lồng nhau bằng cách gọi tường minh qua thư viện chuẩn `builtins` (`builtins.sum`, `builtins.abs`, `builtins.round`, `builtins.min`, `builtins.max`).
- **Tích hợp sâu vào Core Engine**:
  - Bổ sung toàn bộ 101 hàm vào môi trường đánh giá, bọc an toàn trong `_RESTRICTED_BUILTINS` (`safe_abs`, `safe_round`, `arr_max`, `arr_min`, `arr_sum`).
- **Hỗ trợ tên hàm nhiệt động viết hoa / viết thường chữ cái đầu (`Enthalpy` & `enthalpy`)**:
  - Cập nhật toàn bộ các hàm nhiệt động chấp nhận linh hoạt cả chữ cái đầu viết hoa và viết thường (`Enthalpy`/`enthalpy`, `Entropy`/`entropy`, `Density`/`density`, `Volume`/`volume`, `Quality`/`quality`, `Pressure`/`pressure`, `Temperature`/`temperature`, `P_sat`/`p_sat`, `T_sat`/`t_sat`, `Cp`/`cp`/`CP`, `Cv`/`cv`/`CV`, `Wetbulb`/`wetbulb`, `Dewpoint`/`dewpoint`,...).
  - Hoạt động đồng bộ trên bộ giải hệ phương trình, nhận diện tham số hằng số, kiểm tra đơn vị và vòng lặp.

### Xử lý Số học Đơn vị Nhiệt độ Chênh lệch & Bản quyền Giấy phép
- **Xử lý triệt để bài toán độ chênh lệch nhiệt độ ($\Delta T$) và số học đơn vị offset Pint**:
  - Nhận diện chính xác các biến độ chênh lệch nhiệt độ (`deltaT`, `Delta_T_subcool`, `dT`, `T_diff`, `_superheat`, `_subcool`, v.v.)
  - Khắc phục triệt để lỗi khi thực hiện phép cộng nhiệt độ.
  - Khắc phục lỗi tính sai mốc nhiệt độ khi thực hiện phép trừ nhiệt độ.
  - Khắc phục lỗi `Cannot convert from 'delta_degree_Celsius' to 'degree_Celsius'` khi gán chú thích đơn vị vế trái $T_2\text{ [C]} = T_1 - \Delta T$ hoặc $\Delta T\text{ [C]} = 5\text{ [K]}$.
  - Viết lại cây cú pháp AST trong quá trình đánh giá tham số, quy đổi toán hạng chênh lệch nhiệt độ hoặc số literal sang `delta_degC` (hoặc `delta_degF`), tự động quy đổi kết quả tính toán về đơn vị nhiệt độ tuyệt đối của hệ (`degree_Celsius`) khi biến đích là mốc nhiệt độ. Sửa biểu thức regex nhận diện đơn vị ở cuối chuỗi tránh ghi đè đơn vị của toàn bộ biểu thức.
  - Tự động tiêm đơn vị delta (`delta_degC` / `delta_degF`) cho các biến chênh lệch nhiệt độ trong `inject_units`, giúp bộ giải SciPy hội tụ chính xác mà không vấp phải lỗi offset unit calculus.
  - Nạp đơn vị delta vào môi trường kiểm tra đơn vị và lan truyền thứ nguyên chính xác qua AST cho cả biến ẩn và tham số.
- **Chuẩn hóa thông tin bản quyền và Giấy phép (`LICENSE.txt`, `gui.py`)**:

### Tính năng Vẽ Chu trình theo Thứ tự Tùy chọn trên Đồ thị logP-h (`gui.py`, `tests/test_graph_custom_order.py`)
- **Cho phép kết nối các điểm trạng thái theo thứ tự bất kỳ trên đồ thị logP-h (`GraphDialog`)**:
  - Bổ sung hộp kiểm *"Thứ tự tùy chọn"* (`chk_custom_order`) và ô nhập đa dòng (`txt_custom_order`) hỗ trợ nhập các chuỗi điểm tùy ý, tách biệt theo từng dòng.
  - Mỗi dòng định nghĩa một nhánh đường nối (ví dụ: dòng 1: `1,2,3,5` nối 1 $\to$ 2 $\to$ 3 $\to$ 5; dòng 2: `3,5,6,1` nối 3 $\to$ 5 $\to$ 6 $\to$ 1).
  - Hỗ trợ cú pháp gán màu sắc và kiểu nét linh hoạt qua dấu `:` ở cuối dòng (ví dụ: `1,2,3,5:blue:line`, `3,5,6,1:red:dash`).
  - Hỗ trợ song ngữ và linh hoạt thứ tự: nhận diện tên màu tiếng Việt (`xanh nước biển`, `xanh dương`, `đỏ`, `xanh lá`, `đen`, `cam`, `vàng`, `tím`, `xám`, `nâu`, `hồng`), tên tiếng Anh (`blue`, `red`, `green`, `black`, `orange`, `purple`, `cyan`, `magenta`, v.v.), mã hex `#RRGGBB`, và kiểu nét (`line`/`solid`/`nét liền`, `dash`/`dashed`/`nét đứt`, `dot`/`dotted`/`chấm`, `dashdot`/`chấm gạch`).
  - Cơ chế tự động cập nhật mượt mà (debounce 200ms) khi người dùng gõ phím.
  - Tự động fallback về chu trình nối mặc định khi ô nhập rỗng hoặc khi hủy chọn tính năng.
  - Bổ sung nạp giá trị nghiệm từ `main.sol_res.values` vào `_plot_logph` và `_populate_var_combos`, đảm bảo hiển thị tức thì sau khi giải.
  - **Rút gọn nhãn nút/menu Đồ thị**: Rút gọn chuỗi hiển thị nút toolbar và menu từ `"Đồ thị logP-h / Đồ thị chung"` thành **`"Đồ thị"`** (tiếng Anh: `"Plot"` thay vì `"logP-h / General Plot"`), giúp thanh công cụ Toolbar nhỏ gọn, cân đối và thẩm mỹ hơn.
- **Kiểm thử tự động**:
  - Bổ sung bộ kiểm thử `tests/test_graph_custom_order.py` (2 tests) kiểm tra toàn diện hàm phân tích cú pháp `parse_custom_cycle_line` và quá trình dựng đồ thị `GraphDialog` đa đường với thuộc tính màu/nét tùy biến.

### Bổ sung Đồ thị T-s
- **Tích hợp Đồ thị Nhiệt động T-s (Temperature - Entropy) vào `GraphDialog`**:
  - Bổ sung Tab 2 mang tên **"🌡️ T-s"** với đầy đủ chức năng tương tự đồ thị logP-h:
    - Trục tung ($y$): Nhiệt độ $T$ (°C hoặc K theo hộp kiểm `degC`).
    - Trục hoành ($x$): Entropy riêng $s$ (kJ/(kg·K) hoặc J/(kg·K) theo hộp kiểm `kJkgK`).
    - Tự động nhận diện biến mảng `T[i]`, `s[i]` và điểm đơn lẻ `T`, `s`.
    - Tính toán đường cong bão hòa lỏng/hơi (Saturation dome) trên đồ thị T-s (`DomeWorker` hỗ trợ xuất đồng thời $T_{sat}$, $s_{liq}$, $s_{vap}$) mượt mà trên `QThread` không làm đơ giao diện.
    - Hỗ trợ nối chu trình mặc định ("Cycle") và vẽ theo **thứ tự tùy chọn bất kỳ** (`txt_custom_order_ts`) với định dạng đa dòng, tùy biến màu sắc và kiểu nét (`:blue:line`, `:red:dash`, v.v.).
    - Tự động đồng bộ hóa thông minh tên môi chất (`le_fluid`) và nội dung chuỗi nối điểm (`txt_custom_order`) giữa Tab logP-h và Tab T-s.
    - Bảng thông số điểm chu trình hiển thị chi tiết (Điểm, T, s, P, h) cho tất cả các trạng thái.
