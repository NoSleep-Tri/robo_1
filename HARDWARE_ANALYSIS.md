# BÁO CÁO NGHIÊN CỨU CHUYÊN SÂU LINH KIỆN & GIẢI PHẪU KỸ THUẬT ROBOT 1 (LOT HANDLER 400)

Tài liệu này đối chiếu chéo 3 nguồn dữ liệu gốc của dự án:
1. Bảng kê mua hàng thực tế: `Linh_kien_gop_Robot1_Robot2 (1) (1).xlsx`
2. Mô hình CAD 3D & bảng kiểm tra kỹ thuật: `Lot Handler gọn 400.html`
3. Dữ liệu vật lý & firmware nháp: `robot1_physics_data.json`, `firmware_robot1_mega.ino`

---

## PHẦN 1: 6 MÂU THUẪN & ĐIỂM NGHẼN SỐNG CÒN PHÁT HIỆN KHI ĐỐI CHIẾU LINH KIỆN

### 1.1. Mâu thuẫn cơ học nghiêm trọng tại Bộ Kẹp Chung: Servo MG996R + Vít me M5
* **Thiết kế trong mô hình:** Dùng 1 Servo MG996R quay cây vít me M5 ren trái/ren phải để đưa 2 cọc kẹp đóng/mở từ **150 mm** (kẹp lô WIP, $y = \pm 75\text{ mm}$) ra **244 mm** (mở lấy FOUP, $y = \pm 122\text{ mm}$).
* **Tính toán vật lý thực tế:**
  - Hành trình dịch chuyển của mỗi bên cọc kẹp: $\Delta y = 122 - 75 = \mathbf{47\text{ mm}}$.
  - Vít me M5 tiêu chuẩn có bước ren (pitch) = **0.8 mm/vòng**.
  - Số vòng quay cần thiết để đi hết 47 mm:
    $$N = \frac{47\text{ mm}}{0.8\text{ mm/vòng}} = \mathbf{58.75\text{ vòng}}$$
  - Ngay cả khi chỉ tính khoảng dịch chuyển từ lúc kẹp lô WIP (150 mm) sang lúc mở tránh FOUP (240 mm) là $\Delta y = 45\text{ mm}$ mỗi bên $\rightarrow$ cần **56.25 vòng**.
* **Hệ quả kỹ thuật:**
  1. **Nếu dùng MG996R loại góc chuẩn $180^\circ$ (0.5 vòng):** Trục vít M5 chỉ quay được 0.5 vòng $\rightarrow$ mỗi cọc chỉ nhích được **0.4 mm** (hoàn toàn không thể đóng/mở 47 mm như code nháp `CLAMP_OPEN_ANGLE = 40`, `CLAMP_WIP_ANGLE = 115`).
  2. **Nếu dùng MG996R loại quay liên tục $360^\circ$ (Continuous Rotation):**
     - Tốc độ MG996R ở 6V là $\sim 0.14\text{ s}/60^\circ \approx 100\text{ RPM}$ ($\sim 1.67\text{ vòng/giây}$).
     - Để quay 56.25 vòng trên vít M5 bước 0.8 mm, thời gian đóng/mở một lần mất tới **$\sim 33.7\text{ giây}$** (quá chậm khi thi đấu)!
     - Ngoài ra, MG996R $360^\circ$ đã bị cắt bỏ hồi tiếp chiết áp (potentiometer), nên lệnh `Servo.write()` điều khiển **vận tốc và chiều quay** (90 = dừng, 0 = quay thuận max, 180 = quay ngược max) chứ **không điều khiển được góc vị trí**!
* **Giải pháp phần cứng & điều khiển cần chốt:**
  - **Phương án cơ khí A:** Đổi trục vít kẹp từ M5 (bước 0.8 mm) sang **trục vít T8 bước 8 mm (Lead 8 mm) ren trái/phải** (hoặc truyền bánh răng tăng tốc / thanh răng). Với T8 Lead 8 mm, đi 45 mm chỉ cần $45 / 8 = \mathbf{5.6\text{ vòng}}$ (mất $\sim 3.3\text{ giây}$), và đi từ vị trí đỗ 194 mm ($y=97$) vào kẹp WIP 150 mm ($y=75$, $\Delta y = 22\text{ mm}$) chỉ cần **2.75 vòng ($\sim 1.6\text{ giây}$)**.
  - **Phương án điều khiển:** Nếu dùng MG996R $360^\circ$, bắt buộc phải điều khiển theo thời gian kết hợp công tắc hành trình/cữ chặn, không dùng góc độ tuyệt đối.

---

### 1.2. Đặc tính Tự Hãm (Back-driving) của Vít me Nâng Z (T8 Bước 8 mm)
* **Linh kiện trong BOM (Mục 17):** `Trục vít T8 bước 8 mm dài 300 mm + đai ốc + khớp nối` (2 bộ).
* **Bản chất cơ học:**
  - Trục vít T8 bước 8 mm trên thị trường là loại **Pitch 2 mm, 4 đầu mối (4-start), Lead 8 mm/vòng**.
  - Góc nâng ren (Lead angle) lớn nên ma sát tĩnh thấp, **KHÔNG có khả năng tự hãm hoàn toàn** khi chịu tải dọc trục lớn kèm rung động.
  - Tổng khối lượng treo trên 2 đai ốc T8: Giá nâng ($0.27\text{ kg}$) + Khay ($0.384\text{ kg}$) + Bộ kẹp ($0.445\text{ kg}$) = **$1.10\text{ kg}$ (không tải)**, và lên tới **$3.10\text{ kg}$ khi chở FOUP 2 kg**.
* **Hệ quả lên Quy tắc An toàn & Code:**
  - **Nghiêm cấm tắt chân `EN` (`STEP_EN = HIGH`) của 2 driver A4988 khi khay đang ở trên cao ($Z > 11\text{ mm}$)!** Nếu ngắt dòng giữ (holding current) của NEMA 17, sức nặng 3.1 kg sẽ ép đai ốc làm quay ngược trục vít T8, khiến cả khay và FOUP tụt rơi xuống đáy.
  - Khi dừng khẩn cấp hoặc mất sóng PS2 (Failsafe): Phải **giữ nguyên `STEP_EN = LOW`** (chỉ ngắt xung `STEP`) để NEMA 17 khóa cứng vị trí Z hiện tại, chống rơi tải!

---

### 1.3. Mâu thuẫn Kích thước Bánh Mecanum: Excel (60 mm) vs Thiết kế 3D (100 mm)
* **Trong Excel (Mục 7, dòng 11):** Ghi `Bánh Mecanum 60 mm | SL: 1 | 231.000 đ`.
* **Trong Thiết kế 3D HTML:** Toàn bộ khung gầm, vị trí hộp số JGB37-520 ($z = 50\text{ mm}$), gối đỡ vòng bi T8 ($z = 8\text{ mm}$) và đối trọng thép ($z = 8..68\text{ mm}$) được thiết kế cho **Bánh Mecanum Ø100 × 50 mm** (bán kính 50 mm).
* **Hệ quả:**
  - Nếu lắp bánh Ø60 mm (bán kính 30 mm) vào trục ở độ cao $z = 50\text{ mm}$, mặt dưới bánh xe chỉ tới $z = 20\text{ mm}$, tức là **bánh xe bị treo lơ lửng trên không 20 mm**, còn gối đỡ trục vít và đối trọng cạ thẳng xuống đất!
  - Hơn nữa, bánh Mecanum 60 mm vỏ nhựa không chịu nổi trọng lượng xe 9.7 kg. Bắt buộc phải xác nhận ngoài đời thực bạn đã mua **bánh Ø100 mm** hay **bánh Ø60 mm**.

---

### 1.4. Sai khác giữa 3 Động cơ N20 và 2 Mạch TB6612FNG
* **Cấu hình phần cứng thực tế:**
  - **3 động cơ N20 12V 1:150** (không có encoder):
    1. `N20_TRAY` (1 cái): Kéo khay trượt ra vào ($tx$: $0 \rightarrow 140\text{ mm}$).
    2. `N20_FORK_L` & `N20_FORK_R` (2 cái): Đặt ở 2 bên ray dưới gầm khay ($y = \pm 89\text{ mm}$), cùng kéo thanh ngang bộ kẹp chạy dọc hành trình $fe$: từ $-150\text{ mm}$ đến $+84\text{ mm}$ (tổng hành trình **234 mm**).
  - **2 mạch TB6612FNG** (tổng cộng 4 kênh cầu H: 1A, 1B, 2A, 2B).
* **Tính toán vận tốc thật của N20 12V 1:150 + Bánh răng Module 0.5:**
  - Tốc độ quay đầu ra của N20 12V 1:150 dưới tải trung bình: $\sim 90\text{--}120\text{ RPM}$ ($1.5\text{--}2.0\text{ vòng/giây}$).
  - Với bánh răng module 0.5 (giả sử $Z = 14\text{ răng} \rightarrow d = 7\text{ mm}$, chu vi $\pi d \approx 22\text{ mm/vòng}$):
    $$v_{N20} \approx 22 \times (1.5 \sim 2.0) = \mathbf{33 \sim 44\text{ mm/s}}$$
  - Thời gian thực tế để đi hết hành trình:
    - Khay trượt 140 mm: mất **$3.2 \sim 4.2\text{ giây}$**.
    - Bộ kẹp kéo FOUP 205 mm (từ $+67$ về $-138\text{ mm}$): mất **$4.6 \sim 6.2\text{ giây}$**.
* **Lưu ý đấu nối 2 động cơ N20 của bộ kẹp:**
  - Vì 2 động cơ N20 đặt đối xứng 2 bên trái/phải ăn khớp vào 2 thanh răng: Nếu đấu chung vào 1 kênh TB6612FNG (dòng tổng $2 \times 0.3\text{ A} = 0.6\text{ A} < 1.2\text{ A}$ định mức của TB6612), phải **đảo ngược dây nguồn (+/-) của 1 trong 2 động cơ** để chúng cùng đẩy về một hướng.
  - Tuy nhiên, vì đã có sẵn **2 mạch TB6612FNG (4 kênh)** cho **3 động cơ N20**, tốt nhất là **tách mỗi động cơ N20 vào 1 kênh cầu H riêng** (dùng 3/4 kênh của 2 mạch TB6612). Khi đó phần mềm có thể vi chỉnh PWM riêng cho N20 Trái và N20 Phải nếu một bên chạy nhanh hơn bên kia làm xiên ray!

---

### 1.5. Bản chất Điều khiển của Driver XY-160D (4 Động cơ bánh JGB37-520)
* **Linh kiện (Mục 3):** 2 × `Module điều khiển 2 động cơ DC 7A XY-160D`.
* **Đặc tả chân tín hiệu của XY-160D:**
  - Mỗi kênh động cơ có **3 chân điều khiển cách ly quang (Opto-isolated)**:
    - `ENA` (hoặc `ENB`): Chân nhận xung **PWM** điều khiển tốc độ ($0\text{--}255$).
    - `IN1, IN2` (hoặc `IN3, IN4`): 2 chân chọn chiều quay và chế độ phanh:
      | IN1 | IN2 | ENA (PWM) | Trạng thái Động cơ |
      | :-: | :-: | :-: | :--- |
      | `HIGH` | `LOW` | `PWM (0..255)` | Quay thuận với tốc độ PWM |
      | `LOW` | `HIGH` | `PWM (0..255)` | Quay nghịch với tốc độ PWM |
      | `LOW` | `LOW` | `HIGH (255)` | **Phanh hãm cứng (Dynamic Brake)** |
      | `LOW` | `LOW` | `LOW (0)` | Thả trôi tự do (Coast / Stop) |
  - **Lỗi trong code nháp cũ (`firmware_robot1_mega.ino`):** Chỉ dùng 2 chân `IN1, IN2` để băm PWM mà bỏ trống chân `ENA/ENB`. Trên mạch XY-160D thật, băm PWM trực tiếp vào `IN1/IN2` khi đóng jumper `ENA` sẽ làm mạch cầu H đóng-cắt phanh liên tục ở mỗi chu kỳ xung, gây nóng MOSFET và kêu rít động cơ.
  - **Thiết kế chuẩn cho 4 bánh trên Arduino Mega:** Dùng **4 chân PWM** (`ENA1, ENB1, ENA2, ENB2`) + **8 chân Digital thường** (`IN1..IN4` của 2 mạch) = **12 chân**.

---

### 1.6. Rủi ro "Phóng Xe Điên" của Tay cầm PS2 Không Dây + Mạch Chuyển Mức
* **Linh kiện (Mục 4, 5, 33):** Bộ thu + tay cầm PS2 không dây 2.4GHz + Mạch chuyển mức 5V ↔ 3.3V.
* **Cơ chế vật lý & giao thức:**
  - Đầu thu PS2 dùng nguồn **3.3V** (lấy qua mạch chuyển mức; cấp nhầm 5V trực tiếp vào chân VCC của đầu thu sẽ làm chết IC thu RF).
  - Tay cầm PS2 có 2 chế độ hoạt động:
    1. **Digital Mode (Mã ID = `0x41`, đèn MODE tắt):** Khi đó 2 cần gạt Analog (Left/Right Stick) không hoạt động hoặc trả về giá trị mặc định lệch (`0x00` hoặc `0xFF`), trong khi điểm đứng yên chuẩn là `0x80` (`128`).
    2. **Analog Red Mode (Mã ID = `0x73`, đèn MODE sáng đỏ):** Khi đó 2 cần Analog mới trả về tọa độ chuẩn `0..255` (giữa = `128`).
  - **Hiện tượng nguy hiểm:** Khi tay cầm PS2 yếu pin, mất kết nối tạm thời hoặc tự ngủ (auto-sleep sau 60s không chạm), nó sẽ **tự động rớt về Digital Mode (`0x41`)**, hoặc khi đứt dây `DAT` thì byte đọc về toàn `0xFF` (`255`).
  - Nếu code chỉ lấy `LX - 128`, `LY - 128`: Giá trị `0 - 128 = -128` hoặc `255 - 128 = +127` sẽ biến thành lệnh **phóng xe 100% tốc độ**!
  - **Quy tắc bắt buộc khi lập trình đọc PS2:**
    1. Khi khởi tạo `config_gamepad()`, phải gọi `en_Rumble = false, en_Pressures = false` và ép khóa chế độ Analog (`AnalogMode` lock).
    2. Ở mỗi chu kỳ đọc, phải kiểm tra `ps2x.Analog(PSS_LX)` chỉ được chấp nhận khi **ID mode == `0x73` (hoặc `0x79`)** VÀ không phải gói tin rác (`LX == 255 && LY == 255 && RX == 255 && RY == 255` $\rightarrow$ loại bỏ ngay lập tức, khóa xe về 0!).

---

## PHẦN 2: BẢNG TỔNG HỢP THÔNG SỐ KỸ THUẬT CHI TIẾT TỪNG LINH KIỆN ROBOT 1

| Nhóm | Linh kiện | SL | Thông số Kỹ thuật Thực tế | Vị trí lắp trên Robot 1 | Lưu ý Cơ - Điện tử |
| :--- | :--- | :-: | :--- | :--- | :--- |
| **Bộ não** | Arduino Mega 2560 R3 | 1 | ATmega2560, 16MHz, 54 Digital (15 PWM), 16 Analog, 4 UART, 5V logic | Gắn đứng dưới sàn trước ($x=109.5, y=0, z=38.5\text{ mm}$) | Cách xa tháp điện tử đuôi xe $\sim 240\text{ mm}$; cần đi dây vòng qua 2 thanh hông $y=\pm 132\text{ mm}$. |
| **Giao tiếp** | Tay cầm PS2 không dây + Mạch chuyển mức 5V-3.3V | 1 | SPI biến thể (DAT, CMD, SEL/ATT, CLK), nguồn thu 3.3V | Bộ thu gắn ở tháp đuôi ($x=-128, y=60, z=192\text{ mm}$) | Dây SPI từ đuôi về mũi xe dài $\sim 300\text{ mm}$, nên giảm tốc độ xung CLK hoặc dời bộ thu lại gần Mega. |
| **Di chuyển** | Động cơ JGB37-520 | 4 | 12V DC, tỉ số 18.8:1, không tải 530 RPM, có tải 407 RPM, $M_{đm}=0.67\text{ kg}\cdot\text{cm}$, $M_{kẹt}=2.6\text{ kg}\cdot\text{cm}$, trục D $\varnothing 6\text{ mm}$ | Đặt dọc thân xe ($x=\pm 110, y=\pm 127, z=50\text{ mm}$) | Tốc độ rất cao nhưng mô-men thấp; chạy ngang Mecanum khi chở FOUP (9.7kg) ăn dòng sát mức kẹt ($\sim 1.5\text{--}2.5\text{ A}$/motor). |
| **Truyền động** | Cặp bánh răng côn $90^\circ$ + Trục $\varnothing 8$ + Bi 608 | 4 | Module 1, 20 răng, tỉ số 1:1, lỗ $\varnothing 6$ (motor) và $\varnothing 8$ (bánh) | Tại 4 góc ($x=\pm 110, y=\pm 127..\pm 173, z=50\text{ mm}$) | Vòng bi 608 đỡ toàn bộ tải trọng xe; trục motor không chịu lực uốn. Cần siết chặt vít trí trên vát chữ D trục $\varnothing 6$. |
| **Bánh xe** | Bánh Mecanum | 4 | Thiết kế 3D: $\varnothing 100 \times 50\text{ mm}$ (9 con lăn $45^\circ$). *Excel ghi 60 mm (cần xác nhận)* | Tâm bánh tại $x=\pm 110, y=\pm 173, z=50\text{ mm}$ | Nửa tổng chiều dài cơ sở + vệt bánh: $L/2 + W/2 = 110 + 173 = \mathbf{283\text{ mm}}$. |
| **Driver bánh** | Module XY-160D | 2 | Cầu H kép 7A/kênh, áp 9–24V, cách ly quang Opto, điều khiển `ENA, IN1, IN2` | Gắn đứng dưới sàn trước ($x=140, y=\pm 40, z=39.5\text{ mm}$) | Không cấp ngược cực nguồn 12V; tách chân PWM (`ENA/ENB`) riêng khỏi chân chiều quay (`IN1/IN2`). |
| **Trục nâng Z** | Động cơ bước NEMA 17 + Vít me T8 + Ray MGN12 | 2 | NEMA 17 ($42\times 42\times 40\text{ mm}$, $1.8^\circ$/bước), T8 dài 300mm (bước 8mm), MGN12 dài 300–320mm | 2 cột hông ($x=-26, y=\pm 168\text{ mm}$), đỉnh NEMA ở $z=395\text{ mm}$ | Hành trình mặt khay $Z \in [11, 297]\text{ mm}$. **Không tự hãm khi có tải**, bắt buộc giữ `EN=LOW` khi $Z > 11\text{ mm}$. |
| **Driver Z** | Module A4988 + Tản nhiệt | 2 | 12V VMOT, 5V VDD, dòng định mức $\sim 1.0\text{--}1.2\text{ A}$, vi bước 1/1 đến 1/16 | Gắn trên tháp đuôi ($x=-129, y=-60\text{ & }-30, z=192\text{ mm}$) | Bắt buộc có tụ lọc $100\mu\text{F}$ trên chân VMOT-GND; chỉnh $V_{ref}$ đúng dòng tránh ngắt nhiệt lệch 1 bên cột. |
| **Khay & Kẹp** | Động cơ N20 12V 1:150 + Thanh răng Mod 0.5 | 3 | 12V DC, $\sim 100\text{ RPM}$, dòng tải $0.25\text{ A}$, dòng kẹt $0.8\text{ A}$, kích thước $25\times 12\times 10\text{ mm}$ | 1 kéo khay ($tx: 0..140\text{ mm}$); 2 kéo bộ kẹp ($fe: -150..+84\text{ mm}$) | Vận tốc thật khoảng $30\text{--}45\text{ mm/s}$. Không có encoder, cần công tắc hành trình + timeout thực đo ($\sim 4\text{--}6\text{ s}$). |
| **Driver N20** | Module TB6612FNG | 2 | Cầu H kép 1.2A liên tục / 3.2A đỉnh mỗi kênh; có chân `STBY` | Gắn trên tháp đuôi ($x=-129, y=\pm 40, z=130\text{ mm}$) | Chân `STBY` phải kéo lên `HIGH` (5V) mới chạy. Có 4 kênh cho 3 động cơ N20 (mỗi N20 dùng 1 kênh riêng). |
| **Kẹp & Sàn** | Servo MG996R | 3 | Áp 4.8–6.6V, mô-men $\sim 9\text{--}11\text{ kg}\cdot\text{cm}$ ở 6V, dòng kẹt $\sim 2.2\text{--}2.5\text{ A}$/con, kích thước $40.7\times 19.7\times 42.9\text{ mm}$ | 1 trên thanh kẹp ($y=0$), 2 mở sàn khay ($y=\pm 85\text{ mm}$) | Khối giữ chỗ servo mở sàn trên mô hình 3D ($30\times 10\times 20$) nhỏ hơn MG996R thật; cần kiểm tra cấn cơ khí khi lắp thật. |
| **Nguồn & Hạ áp** | Pin LiPo 3S 5400mAh 30C + 2 Mạch Buck | 1+2 | LiPo $11.1\text{V}$ ($12.6\text{V}$ đầy). 1 Buck 5V 5A (nuôi Mega + logic), 1 Buck XH-M404 / XL4015 chỉnh **6.0V** (nuôi 3 Servo) | Gắn trên tháp đuôi ($x=-129..-145\text{ mm}$) | Tuyệt đối dùng đồng hồ VOM đo và chỉnh áp đầu ra của mạch Buck XH-M404 về đúng **6.0V** trước khi cắm Servo! |
| **An toàn & Phụ trợ** | Nút E-Stop $\varnothing 22$, Công tắc hành trình, Đối trọng thép | 1+4+2 | E-Stop nhấn giữ tự khóa; 4 microswitch hành trình; 2 khối thép đối trọng $0.5\text{ kg}$/khối | E-Stop ở góc trên đuôi ($x=-145, y=-85, z=245$); đối trọng ở gầm đuôi | 2 khối đối trọng $1.0\text{ kg}$ nâng hệ số chống lật khi vươn FOUP 2kg từ $1.48$ lên $2.05$. |

---

## PHẦN 3: BẢNG HÀNH TRÌNH ĐỘNG HỌC CHÍNH XÁC CỦA 5 CƠ CẤU CHẤP HÀNH

| Trục / Cơ cấu | Ký hiệu | Động cơ dẫn động | Vị trí Đỗ (Gọn 400mm) | Dải Hành Trình Thực Tế | Các Mốc Vị Trí Quan Trọng trong Thi Đấu |
| :--- | :---: | :--- | :---: | :---: | :--- |
| **1. Giá nâng dọc** | `tz` | 2× NEMA 17 (Vít T8) | **11 mm** | **11 $\rightarrow$ 297 mm** | • `11 mm`: Đỗ & Thả lô WIP xuống ô sàn<br>• `100 mm`: Ngang mặt kệ Stocker (Kéo FOUP) & Đặt FOUP xuống đáy Port<br>• `125 mm`: Lấy lô WIP Tầng 1<br>• `140 mm`: Ngang mặt gờ Carrier Return Port (Đẩy FOUP qua gờ)<br>• `211 mm`: Lấy lô WIP Tầng 2 ($125 + 86$)<br>• `297 mm`: Lấy lô WIP Tầng 3 ($125 + 2\times 86$) |
| **2. Khay trượt ngang** | `tx` | 1× N20 1:150 | **0 mm** | **0 $\rightarrow$ 140 mm** | • `0 mm`: Thu gọn trong lòng xe<br>• `80 mm`: Trượt ra đón đáy FOUP khi kéo từ kệ Stocker<br>• `140 mm`: Trượt ra tối đa khi rút lô WIP ở cả 3 tầng |
| **3. Ray trượt bộ kẹp** | `fe` | 2× N20 1:150 | **−70 mm** | **−150 $\rightarrow$ +84 mm** | • `-150 mm`: Kéo lô WIP lùi hẳn vào giữa lòng khay<br>• `-138 mm`: Kéo FOUP trượt từ kệ lên nằm gọn trên khay<br>• `-70 mm`: Vị trí đỗ chuẩn (nằm gọn trong 400mm)<br>• `+20 mm`: Đưa 2 cọc kẹp ôm vào hông lô WIP trong FOUP<br>• `+67 mm`: Đưa 2 cọc kẹp ôm vào hông FOUP sát mặt trước<br>• `+84 mm`: Đẩy FOUP vượt qua gờ 75mm vào hẳn trên ô Port |
| **4. Độ mở hai cọc kẹp** | `2 × ys` | 1× MG996R | **194 mm** ($ys = 97$) | **150 $\rightarrow$ 244 mm** ($ys: 75 \rightarrow 122$) | • `154 mm` ($ys = 77$): Kẹp chặt 2 bên hông lô WIP (rộng 150 mm, ép đệm cao su)<br>• `194 mm` ($ys = 97$): Vị trí đỗ & Nhả lô WIP trong lòng khay<br>• `224 mm` ($ys = 112$): Kẹp chặt 2 bên hông hộp FOUP (rộng 220 mm)<br>• `240 mm` ($ys = 120$): Mở rộng tối đa để luồn qua ngoài vỏ FOUP |
| **5. Cửa sàn khay đáy** | `po` | 2× MG996R | **0 %** (Đóng) | **0 $\rightarrow$ 78 mm** mỗi bên | • `0 %` ($0\text{ mm}$): 4 tấm nhôm khép kín đỡ lô WIP và FOUP<br>• `100 %` ($78\text{ mm}$ mỗi bên): Mở toạc sang 2 bên ở $tz = 11\text{ mm}$ để lô WIP rơi 11 mm xuống ô |
