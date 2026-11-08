# PHÂN HỆ 1: HỒ SƠ KỸ THUẬT HỆ THỐNG DI CHUYỂN ĐA HƯỚNG MECANUM
**Dự án:** Robot 1 — Lot Handler gọn 400 mm (`robo_1`)  
**Trạng thái:** Đã chốt thông số cơ khí & điều khiển (Bánh Ø60 mm · JGB37-520 530 RPM · Lái PS2 Kiểu A) · Chờ chốt sơ đồ chân Mega 2560 — **CHƯA VIẾT CODE**

---

## 1. DANH MỤC LINH KIỆN THUỘC PHÂN HỆ DI CHUYỂN (ĐÃ CHỐT THEO THỰC TẾ)

Toàn bộ hệ thống di chuyển của Robot 1 gồm **8 nhóm phần cứng** phối hợp trực tiếp với nhau:

| STT | Linh kiện | SL | Trạng thái | Thông số Kỹ thuật Thực tế đã chốt | Vị trí lắp & Lưu ý Hình học trên khung xe |
| :-: | :--- | :-: | :---: | :--- | :--- |
| 1 | **Khung nhôm định hình 2020** | 1 bộ | Đã chốt | Dài $320\text{ mm} \times$ Rộng $284\text{ mm}$, đặt ở độ cao $z = 70..90\text{ mm}$ (tâm $z = 80\text{ mm}$). Hai thanh dọc hông ($y = \pm 132\text{ mm}$) chia làm 2 đoạn: đoạn sau dài $130\text{ mm}$ ($x = -160..-30$), đoạn trước dài $140\text{ mm}$ ($x = +20..+160$), chừa khe trống $50\text{ mm}$ ở giữa ($x = -30..+20$) cho tay đỡ khay đi qua. | Giữ nguyên độ cao khung để không làm cạ gối bi T8 ($z=2\text{ mm}$) và đối trọng ($z=8\text{ mm}$) xuống sàn. |
| 2 | **Động cơ DC giảm tốc JGB37-520** | 4 cái | **Đã chốt** | Điện áp $12\text{ V}$, tốc độ không tải **$530\text{ RPM}$** (tỉ số truyền $18.8:1$, tốc độ có tải $\sim 407\text{ RPM}$). Vỏ động cơ $\varnothing 33 \times 31\text{ mm}$, hộp số đồng tâm $\varnothing 37 \times 22\text{ mm}$, trục ra $\varnothing 6\text{ mm}$ vát chữ D dài $16\text{ mm}$. | Đặt dọc thân xe (song song trục $X$), $y = \pm 127\text{ mm}$. **Hạ tâm trục xuống $z = 30\text{ mm}$** theo bán kính bánh $\varnothing 60\text{ mm}$ (mép dưới hộp số cách sàn $11.5\text{ mm}$). |
| 3 | **Bộ truyền góc $90^\circ$ (Bánh răng côn + Trục $\varnothing 8$ + Bi 608)** | 4 bộ | Đã chốt | Cặp bánh răng côn $90^\circ$ module $1$, $20\text{ răng}$, tỉ số truyền $1:1$ (1 bánh lỗ $\varnothing 6\text{ mm}$ lắp trục JGB37, 1 bánh lỗ $\varnothing 8\text{ mm}$ lắp trục bánh xe). Trục thép $\varnothing 8\text{ mm}$ dài $60\text{ mm}$ đỡ bằng 2 vòng bi $608$ ($\varnothing 8 \times \varnothing 22 \times 7\text{ mm}$) trong gá nhôm. | Góc 4 bánh tại $x = \pm 110\text{ mm}$, $y = \pm 127..\pm 173\text{ mm}$, độ cao tâm trục **$z = 30\text{ mm}$**. |
| 4 | **Bánh xe Mecanum $\varnothing 60\text{ mm}$** | 4 cái | **Đã chốt** | **Đường kính $\varnothing 60\text{ mm}$** (bán kính $R = 30\text{ mm}$), bề rộng $\sim 27\text{--}31\text{ mm}$, con lăn nghiêng $45^\circ$ (2 bánh Trái loại A, 2 bánh Phải loại B — Mục 11 Excel BOM). | Tâm bánh tại $x = \pm 110\text{ mm}$, $y = \pm 173\text{ mm}$, **$z = 30\text{ mm}$**. Nửa tổng chiều dài + vệt bánh: $L/2 + W/2 = 110 + 173 = \mathbf{283\text{ mm}}$. |
| 5 | **Driver cầu H kép XY-160D (7A)** | 2 mạch | Đã chốt | Điện áp động lực $9\text{--}24\text{ V}$, dòng liên tục $7\text{ A}$/kênh (đỉnh $16\text{ A}$), cách ly quang (Optocoupler) ngõ vào logic $3.3\text{--}5\text{ V}$. Kích thước $62 \times 55 \times 30\text{ mm}$ (có tản nhiệt nhôm dưới đáy). | Gắn đứng dưới sàn trước ($x = +140\text{ mm}, y = \pm 40\text{ mm}, z = 39.5\text{ mm}$), ngay sau thanh nhôm ngang mũi xe. |
| 6 | **Vi điều khiển Arduino Mega 2560 R3** | 1 mạch | Đã chốt | ATmega2560 ($16\text{ MHz}$), $54$ chân Digital ($15$ chân PWM), $16$ chân Analog, logic $5\text{ V}$. Kích thước $101.6 \times 53.3 \times 13.1\text{ mm}$. | Gắn đứng dưới sàn trước ($x = +109.5\text{ mm}, y = 0, z = 38.5\text{ mm}$), ngoài vùng quét của khay. |
| 7 | **Bộ thu + Tay cầm PS2 không dây & Mạch chuyển mức 5V-3.3V** | 1 bộ | **Đã chốt Kiểu A** | Sóng RF $2.4\text{ GHz}$, đầu thu dùng nguồn **$3.3\text{ V}$**, giao tiếp SPI biến thể 4 dây (`DAT`, `CMD`, `SEL/ATT`, `CLK`) qua mạch dịch mức logic $5\text{ V} \leftrightarrow 3.3\text{ V}$. | Khuyến nghị đặt đầu thu gần Arduino Mega ở mũi xe để dây SPI ngắn $< 10\text{ cm}$ chống nhiễu. |
| 8 | **Nguồn Pin LiPo 3S + Mạch Buck 5V + Nút E-Stop** | 1 bộ | Đã chốt | Pin LiPo 3S $5400\text{ mAh } 30\text{C}$ ($11.1\text{ V}$ danh định, $12.6\text{ V}$ đầy, kích thước $130 \times 45 \times 30\text{ mm}$). Mạch Buck $12\text{V} \rightarrow 5\text{V } 5\text{A}$. Nút dừng khẩn cấp $\varnothing 22\text{ mm}$ nhấn giữ. | Gắn trên tháp điện tử đuôi xe ($x = -145\text{ mm}, z = 227..245\text{ mm}$). |

---

## 2. GIẢI PHẪU CHI TIẾT TỪNG CỤM CƠ - ĐIỆN TỬ (VỚI BÁNH MECANUM Ø60 MM & JGB37 530 RPM)

### 2.1. Cụm Động cơ JGB37-520 530 RPM ghép Bánh Mecanum Ø60 mm — Bài toán Lực kéo & Tốc độ
1. **Tại sao JGB37-520 đặt dọc thân xe?**
   - Hộp số trụ răng thẳng của JGB37-520 có trục ra trùng tâm thân động cơ (tổng chiều dài thân + hộp số $= 31 + 22 = 53\text{ mm}$, cộng đầu trục $16\text{ mm}$ là $69\text{ mm}$).
   - Đặt dọc theo sườn xe ($y = \pm 127\text{ mm}$) và bẻ góc $90^\circ$ ra bánh xe qua cặp bánh răng côn $1:1$ giúp giải phóng hoàn toàn lòng xe ở giữa cho khay trượt ($160\text{ mm}$) và FOUP ($220\text{ mm}$) hạ sát đáy ($z = 11\text{ mm}$).
2. **Hệ quả Cực kỳ Tích cực khi chốt Bánh Mecanum Ø60 mm ($R = 3.0\text{ cm}$) thay vì Ø100 mm ($R = 5.0\text{ cm}$):**
   - Thông số động cơ **JGB37-520 12V 530 RPM** (tỉ số truyền $1:18.8$):
     - Tốc độ không tải: $530\text{ vòng/phút}$ ($\sim 8.83\text{ vòng/s}$).
     - Tốc độ có tải định mức: $\sim 407\text{ vòng/phút}$ ($\sim 6.78\text{ vòng/s}$).
     - Mô-men định mức: $\tau_{\text{đm}} \approx 0.67\text{ kg}\cdot\text{cm}$ ($\approx 0.066\text{ N}\cdot\text{m}$).
     - Mô-men kẹt cứng (Stall torque): $\tau_{\text{stall}} \approx 2.6\text{ kg}\cdot\text{cm}$ ($\approx 0.255\text{ N}\cdot\text{m}$).
   - Khi ghép với **Bánh Mecanum $\varnothing 60\text{ mm}$** (bán kính $R = 3.0\text{ cm}$), hiệu suất cặp bánh răng côn $\eta \approx 92\%$:
     - **Lực kéo mặt đất tăng $67\%$ so với bánh 100 mm:**
       - Lực kéo tiếp tuyến định mức mỗi bánh:
         $$F_{\text{đm}} = \frac{0.67 \times 0.92}{3.0\text{ cm}} \approx 0.205\text{ kgf} \approx \mathbf{2.01\text{ N/bánh}} \quad (\text{Tổng 4 bánh} \approx \mathbf{8.05\text{ N}})$$
       - Lực kéo cực đại (sát ngưỡng kẹt) mỗi bánh:
         $$F_{\text{max}} = \frac{2.6 \times 0.92}{3.0\text{ cm}} \approx 0.797\text{ kgf} \approx \mathbf{7.82\text{ N/bánh}} \quad (\text{Tổng 4 bánh} \approx \mathbf{31.3\text{ N}})$$
     - **Vận tốc dài giảm về mức vừa tầm điều khiển:**
       - Vận tốc ở 407 RPM (có tải, 100% PWM):
         $$v_{\text{tải}} = \pi \times 0.060\text{ m} \times \frac{407}{60} \approx \mathbf{1.28\text{ m/s}}$$
       - Vận tốc ở 530 RPM (không tải, 100% PWM):
         $$v_{\text{không tải}} = \pi \times 0.060\text{ m} \times \frac{530}{60} \approx \mathbf{1.66\text{ m/s}}$$
   - **Đánh giá Tải trọng Thực tế với Bánh Ø60 mm:**
     - **Khi chạy thẳng** (xe nặng $9.73\text{ kg}$ khi chở FOUP $2\text{ kg}$, hệ số cản lăn $\mu_r \approx 0.08$, tổng lực cản $\approx 7.6\text{ N}$): Mỗi bánh chỉ cần mô-men **$\approx 0.63\text{ kg}\cdot\text{cm} \le \tau_{\text{đm}} (0.67\text{ kg}\cdot\text{cm})$** $\rightarrow$ Động cơ chạy hoàn toàn trong vùng định mức mát mẻ!
     - **Khi dạt ngang Mecanum (Strafe)** chở FOUP $2\text{ kg}$: Mô-men yêu cầu mỗi bánh giảm từ $2.2\text{ kg}\cdot\text{cm}$ (ở bánh 100 mm) xuống còn **$1.1\text{--}1.35\text{ kg}\cdot\text{cm}$** ($\approx 50\%$ mô-men kẹt $2.6\text{ kg}\cdot\text{cm}$). Kết hợp giới hạn trần PWM trong code (`Cruising PWM <= 160/255`, `Mang FOUP PWM <= 80..100/255`) và dốc gia tốc mềm, bản 530 RPM hoàn toàn kéo được xe!
3. **3 Điểm Cơ Khí Bắt Buộc Kiểm Tra Khi Lắp Bánh Mecanum Ø60 mm:**
   - **Điểm 1 — Độ cao lắp cụm Động cơ & Trục bánh ($z = 30\text{ mm}$):**
     - Bản vẽ 3D cũ đặt tâm trục bánh ở $z = 50\text{ mm}$ (theo bánh Ø100 mm). Khi dùng bánh **$\varnothing 60\text{ mm}$** (bán kính $30\text{ mm}$), tâm trục bánh $\varnothing 8\text{ mm}$ và tâm trục JGB37-520 phải hạ xuống độ cao **$z = 30\text{ mm}$** so với mặt sàn (bắt gá treo dưới đáy thanh nhôm 2020) để giữ nguyên độ cao khung xe. Khi tâm động cơ ở $z = 30\text{ mm}$, mép dưới vỏ hộp số $\varnothing 37\text{ mm}$ nằm ở $z = 30 - 18.5 = \mathbf{11.5\text{ mm}}$ (cách sàn $11.5\text{ mm}$, an toàn).
     - *Lưu ý:* Không được hạ thấp toàn bộ khung xe xuống $20\text{ mm}$ vì sẽ làm gối bi dưới của trục vít T8 ($z = 2\text{ mm}$ trên 3D) và đối trọng thép ($z = 8\text{ mm}$) chạm sàn!
   - **Điểm 2 — Khớp nối giữa Trục $\varnothing 8\text{ mm}$ và Bánh Mecanum Ø60 mm:**
     - Trục bánh xe chạy qua 2 vòng bi 608 là **trục thép $\varnothing 8\text{ mm}$**, trong khi lỗ moay-ơ của bánh Mecanum 60 mm phổ thông thường là lỗ $\varnothing 6\text{ mm}$ hoặc lục giác. Cần kiểm tra bộ *Khớp nối trục nhôm (Mục 12 BOM)* xem đã đúng lỗ $\varnothing 8\text{ mm}$ ăn khớp với bánh 60 mm chưa; nếu khớp nối đi kèm là lỗ $6\text{ mm}$ thì cần khoan/doa đồng tâm lên $\varnothing 8\text{ mm}$.
   - **Điểm 3 — Khe hở cơ khí:**
     - Bánh Mecanum $\varnothing 60\text{ mm}$ có bề rộng mỏng hơn ($\sim 27\text{--}31\text{ mm}$ so với $50\text{ mm}$ của bánh 100 mm), nên khe hở từ mặt trong bánh xe tới vỏ hộp số $\varnothing 37\text{ mm}$ tăng từ $2.5\text{ mm}$ lên **$> 12\text{ mm}$** (hết nguy cơ cạ má trong bánh xe vào hộp số).
     - Khe hở giữa **mép trong vỏ hộp số $\varnothing 37\text{ mm}$ ($|y| = 108.5\text{ mm}$) và ray khay ($|y| \approx 106\text{ mm}$)** vẫn là **$2.5\text{ mm}$** — khi bắt gá động cơ tuyệt đối không để thân động cơ lệch vào trong lòng xe.

---

### 2.2. Cụm Truyền động Cặp Bánh Răng Côn $90^\circ$ & Trục Bánh $\varnothing 8\text{ mm}$
1. **Cấu tạo:**
   - Bánh răng côn chủ động (Module 1, 20 răng, lỗ $\varnothing 6\text{ mm}$) khóa vào trục vát chữ D của JGB37-520 bằng vít trí (set screw) M3/M4.
   - Bánh răng côn bị động (Module 1, 20 răng, lỗ $\varnothing 8\text{ mm}$) khóa vào trục thép $\varnothing 8\text{ mm}$ dài $60\text{ mm}$.
   - Trục thép $\varnothing 8\text{ mm}$ đi xuyên qua 2 vòng bi $608$ đặt trong gá nhôm bắt vào khung, đầu ngoài gắn moay-ơ bánh Mecanum $\varnothing 60\text{ mm}$.
2. **Chiều quay Cơ học của 4 Bánh (Điểm cực kỳ dễ nhầm khi đấu dây):**
   - Trong thiết kế khung (`Lot Handler gọn 400.html` dòng 233):
     - Hai động cơ **Trước** ($sx = +1, x = +110$): Thân động cơ hướng về phía sau ($d = -1$), **đầu trục động cơ hướng về phía mũi xe ($+X$)**.
     - Hai động cơ **Sau** ($sx = -1, x = -110$): Thân động cơ hướng về phía trước ($d = +1$), **đầu trục động cơ hướng về phía đuôi xe ($-X$)**.
   - Vì đầu trục động cơ Trước và Sau hướng **ngược chiều nhau** ($+X$ so với $-X$), trong khi bánh răng côn bị động đều nằm ở phía ngoài ($+Y$ cho bên trái, $-Y$ cho bên phải):
     - Nếu cấp cùng cực tính điện áp $(+ / -)$ vào 2 động cơ cùng một bên (ví dụ Trước-Trái và Sau-Trái), **hai bánh xe sẽ quay NGƯỢC CHIỀU NHAU!**
     - Do đó, trong `config_robot1.h`, mỗi bánh (`FL, FR, RL, RR`) sẽ có một cờ đảo chiều độc lập `WHEEL_DIR_INVERT[4] = {false, true, true, false}` (hoặc chỉnh trực tiếp sau khi kê gầm test từng bánh ở 20% PWM).
   - **Chống trượt vít trí trên trục $\varnothing 8\text{ mm}$:** Bắt buộc **mài phẳng một mặt chữ D (flat spot)** trên trục thép $\varnothing 8\text{ mm}$ tại đúng vị trí vít trí của bánh răng côn và khớp nối bánh Mecanum, kèm keo khóa ren.

---

### 2.3. Cụm Bánh Mecanum Ø60 mm — Quy tắc Lắp Con Lăn (Type A & Type B)
Bộ 4 bánh Mecanum Ø60 mm gồm **2 bánh loại A** và **2 bánh loại B**.
- Quy chuẩn bắt buộc (nhìn từ **trên đỉnh robot xuống** mặt lưng 4 bánh): Đường trục của các con lăn trên mặt đỉnh bánh phải hướng vào tâm xe tạo thành **hình chữ X** (mặt con lăn tiếp đất tạo thành **hình chữ O / hình thoi**):
  - **Trước-Trái (`FL`):** Bánh **A** (con lăn mặt trên hướng vào sau-phải $\searrow$).
  - **Trước-Phải (`FR`):** Bánh **B** (con lăn mặt trên hướng vào sau-trái $\swarrow$).
  - **Sau-Trái (`RL`):** Bánh **B** (con lăn mặt trên hướng vào trước-phải $\nearrow$).
  - **Sau-Phải (`RR`):** Bánh **A** (con lăn mặt trên hướng vào trước-trái $\nwarrow$).
- Nếu lắp ngược A và B, xe sẽ không thể dạt ngang (strafe) mà chỉ đứng tại chỗ tự vặn xoắn khung.
- Kiểm tra tải trên bánh 60 mm: Không để xe đứng yên một chỗ chịu tải FOUP quá lâu làm bẹp cục bộ con lăn cao su; khi cất xe sau buổi tập nên kê gầm để 4 bánh 60 mm lơ lửng.

---

### 2.4. Cụm Driver Cầu H Kép XY-160D (7A)
1. **Sơ đồ chân trên mỗi module XY-160D:**
   - **Cầu đấu công suất (Domino xanh lá):**
     - `PGND`: Mass nguồn động lực (Nối âm Pin LiPo 3S VÀ nối chung GND với Arduino Mega).
     - `+12V / +24V`: Dương nguồn động lực (Đi từ cực dương Pin LiPo 3S $\rightarrow$ Cầu chì $\rightarrow$ Công tắc nguồn $\rightarrow$ **Qua tiếp điểm thường đóng NC của nút E-Stop** $\rightarrow$ vào chân `+12V`).
     - `OUT1, OUT2`: Ra động cơ kênh A.
     - `OUT3, OUT4`: Ra động cơ kênh B.
   - **Hàng rào chân điều khiển (Header cách ly quang):**
     - `+5V`: Chân cấp nguồn nuôi phía phát của Optocoupler trên mạch XY-160D (Nối vào chân `5V` của Arduino Mega).
     - `GND`: Mass tín hiệu.
     - `ENA`: Nhận xung **PWM** cho động cơ A.
     - `IN1, IN2`: Nhận mức logic (`HIGH`/`LOW`) chọn chiều quay động cơ A.
     - `ENB`: Nhận xung **PWM** cho động cơ B.
     - `IN3, IN4`: Nhận mức logic (`HIGH`/`LOW`) chọn chiều quay động cơ B.
2. **Bảng Trạng thái Điều khiển Chuẩn cho XY-160D:**

| `IN1` | `IN2` | `ENA` (PWM) | Trạng thái Động cơ | Khi nào sử dụng |
| :---: | :---: | :---: | :--- | :--- |
| `HIGH` | `LOW` | `0..255` | Quay thuận với tốc độ PWM | Chạy tiến / dạt |
| `LOW` | `HIGH` | `0..255` | Quay nghịch với tốc độ PWM | Chạy lùi / dạt |
| `LOW` | `LOW` | `HIGH (255)` | **Phanh hãm động năng (Dynamic Brake)** | Khi cần dừng đứng chính xác trước kệ Stocker / Port |
| `LOW` | `LOW` | `LOW (0)` | Thả trôi tự do (Coast) | Khi xe đang đứng yên nghỉ hoặc vào `STATE_FAILSAFE` |

3. **Đặc tính điện của Optocoupler trên XY-160D:**
   - Giữ nguyên tần số PWM mặc định của Arduino Mega 2560 (**$490\text{ Hz}$** hoặc $980\text{ Hz}$). Tuyệt đối không nâng tần số PWM lên $> 20\text{ kHz}$ vì optocoupler trên XY-160D sẽ bị méo xung làm nóng MOSFET.

---

### 2.5. Cụm Tay cầm PS2 Không Dây (Đã chốt Kiểu A) & Chống "Phóng Xe Điên"
1. **Cách đấu nối điện qua Mạch Chuyển Mức 5V ↔ 3.3V:**
   - Đầu thu PS2 không dây bắt buộc cấp nguồn **$3.3\text{ V}$** (tuyệt đối không cắm $5\text{ V}$ trực tiếp vào chân `VCC` của đầu thu).
   - 4 đường tín hiệu SPI (`DAT`, `CMD`, `SEL/ATT`, `CLK`) đi qua mạch chuyển mức $5\text{V} \leftrightarrow 3.3\text{V}$, trong đó chân `DAT` có trở kéo lên (pull-up) $1\text{k}\Omega\text{--}4.7\text{k}\Omega$.
   - Khuyến nghị đặt **Bộ thu PS2 + Mạch chuyển mức** ngay cạnh **Arduino Mega ở mũi xe** để dây SPI ngắn $< 10\text{ cm}$, tránh nhiễu từ dây động cơ 12V.
2. **Quy ước Lái xe Tay cầm PS2 — ĐÃ CHỐT KIỂU A:**
   - **Cần Analog Trái (`LY`):** Tiến (`LY < 116` $\rightarrow +V_x$) / Lùi (`LY > 140` $\rightarrow -V_x$).
   - **Cần Analog Trái (`LX`):** Dạt Ngang Trái (`LX < 116` $\rightarrow -V_y$) / Dạt Ngang Phải (`LX > 140` $\rightarrow +V_y$).
   - **Cần Analog Phải (`RX`):** Xoay tại chỗ Trái (`RX < 116` $\rightarrow -\omega_z$) / Xoay tại chỗ Phải (`RX > 140` $\rightarrow +\omega_z$).
   - **Vùng chết (Deadzone):** `|stick - 128| <= 12` (tức giá trị `116..140` quy về `0`, chống trôi xe khi thả tay).
   - **3 Chế độ Tốc độ (với Bánh Ø60 mm + JGB37 530 RPM):**
     1. **Docking Mode (Giữ nút `L1`):** Trần `PWM_MAX = 80 / 255` ($\sim 31\%$, vận tốc $\approx \mathbf{0.38\text{ m/s}}$) — dùng khi tiến vào gắp lô WIP hoặc căn ô đặt FOUP.
     2. **Cruising Mode (Mặc định khi không giữ nút):** Trần `PWM_MAX = 160 / 255` ($\sim 63\%$, vận tốc $\approx \mathbf{0.80\text{ m/s}}$) — di chuyển tiêu chuẩn trên sân.
     3. **Fast Mode (Giữ nút `R1`, chỉ mở khi không chở FOUP trên cao):** Trần `PWM_MAX = 220 / 255` ($\sim 86\%$, vận tốc $\approx \mathbf{1.10\text{ m/s}}$).
     *(Lưu ý Điều 3 An toàn: Khi đang mang FOUP ở độ cao $Z > 100\text{ mm}$, phần mềm tự động ép trần tốc độ về `PWM <= 75/255` bất kể người lái có giữ `R1` hay không).*
3. **Thuật toán Động học Mecanum & Dốc Gia Tốc Mềm (Slew-Rate Limiter):**
   - Từ 3 thành phần vận tốc yêu cầu $(V_x, V_y, \omega_z) \in [-1.0, +1.0]$:
     $$\begin{aligned}
     u_{FL} &= V_x + V_y + \omega_z \\
     u_{FR} &= V_x - V_y - \omega_z \\
     u_{RL} &= V_x - V_y + \omega_z \\
     u_{RR} &= V_x + V_y - \omega_z
     \end{aligned}$$
   - **Chuẩn hóa đồng dạng (Vector Normalization):**
     - Gọi $u_{\max} = \max(|u_{FL}|, |u_{FR}|, |u_{RL}|, |u_{RR}|)$.
     - Nếu $u_{\max} > 1.0$, chia cả 4 giá trị $u_i$ cho $u_{\max}$ để giữ nguyên hướng di chuyển tổng hợp, sau đó nhân với `PWM_MAX` của chế độ tốc độ hiện hành.
   - **Dốc gia tốc mềm (Slew-Rate Ramp):**
     - Ở mỗi chu kỳ điều khiển $\Delta t = 20\text{ ms}$ ($50\text{ Hz}$), giá trị PWM thực xuất ra mỗi bánh chỉ được thay đổi tối đa `MAX_PWM_STEP = 15` đơn vị (mất $\sim 220\text{ ms}$ để tăng từ $0$ lên $160$).
     - Nếu bánh xe đổi chiều quay (dấu của `pwm_target` ngược với `pwm_current`), bắt buộc giảm `pwm_current` về `0` trước rồi mới tăng sang chiều ngược lại để bảo vệ bánh răng côn $90^\circ$ và cầu H XY-160D.
4. **Cơ chế Chống "Phóng Xe Điên" khi rớt Analog Mode:**
   - Chỉ chấp nhận dữ liệu cần gạt khi byte Mode của tay cầm PS2 là **`0x73` (Analog Red Mode)** hoặc **`0x79`** và gói tin không phải toàn `0xFF` (đứt dây `DAT`).
   - Ngay khi tay cầm tự ngủ hoặc rớt về Digital Mode (`0x41`), khóa ngay $V_x = V_y = \omega_z = 0$ và tự động gửi lệnh cấu hình lại Analog Lock. Quá $100\text{ ms}$ không có gói hợp lệ thì ngắt `ENA/ENB = 0` (vào `STATE_FAILSAFE`).

---

## 3. SƠ ĐỒ CHÂN ARDUINO MEGA 2560 ĐỀ XUẤT CHO PHÂN HỆ DI CHUYỂN (`[CHỜ CHỐT CHÂN THỰC TẾ]`)

Vì hiện tại bạn **chưa chốt cố định chân cắm trên Arduino Mega 2560**, dưới đây là quy hoạch chân chuẩn nhất cho riêng **Phân hệ Di chuyển** (chọn các chân PWM phần cứng $490\text{ Hz}$ liền kề và cổng Digital Port A `22..29` ngay đầu bo Mega để đi dây gọn nhất):

| Cụm Thiết Bị | Kênh / Tín hiệu | Chân trên Module | Chân Mega 2560 Đề xuất | Loại Chân | Ghi chú Kỹ thuật |
| :--- | :--- | :---: | :---: | :---: | :--- |
| **Driver XY-160D Số 1**<br>*(2 Bánh Trước: FL & FR)* | Bánh Trước-Trái (`FL`) — Xung PWM | `ENA1` | **Pin `2`** | PWM (Timer3) | Tần số chuẩn $490\text{ Hz}$ cho Opto |
| | Bánh Trước-Trái (`FL`) — Chiều quay | `IN1_1`, `IN2_1` | **Pin `22`, `23`** | Digital (PA0, PA1) | Đảo trong code nếu ngược chiều |
| | Bánh Trước-Phải (`FR`) — Xung PWM | `ENB1` | **Pin `3`** | PWM (Timer3) | Tần số chuẩn $490\text{ Hz}$ cho Opto |
| | Bánh Trước-Phải (`FR`) — Chiều quay | `IN3_1`, `IN4_1` | **Pin `24`, `25`** | Digital (PA2, PA3) | Đảo trong code nếu ngược chiều |
| **Driver XY-160D Số 2**<br>*(2 Bánh Sau: RL & RR)* | Bánh Sau-Trái (`RL`) — Xung PWM | `ENA2` | **Pin `5`** | PWM (Timer3) | *Tránh Pin 4 (980Hz) để 4 bánh cùng 490Hz* |
| | Bánh Sau-Trái (`RL`) — Chiều quay | `IN1_2`, `IN2_2` | **Pin `26`, `27`** | Digital (PA4, PA5) | Đảo trong code nếu ngược chiều |
| | Bánh Sau-Phải (`RR`) — Xung PWM | `ENB2` | **Pin `6`** | PWM (Timer4) | Tần số chuẩn $490\text{ Hz}$ cho Opto |
| | Bánh Sau-Phải (`RR`) — Chiều quay | `IN3_2`, `IN4_2` | **Pin `28`, `29`** | Digital (PA6, PA7) | Đảo trong code nếu ngược chiều |
| **Bộ thu PS2 Không Dây**<br>*(Qua mạch chuyển mức 3.3V)* | Dữ liệu về (`Data / MISO`) | `DAT` | **Pin `30`** | Digital (PC7) | Có trở kéo lên $3.3\text{V}$ trên mạch dịch mức |
| | Lệnh điều khiển (`Command / MOSI`) | `CMD` | **Pin `31`** | Digital (PC6) | Qua mạch dịch mức $5\text{V} \rightarrow 3.3\text{V}$ |
| | Chọn chip (`Attention / CS`) | `SEL` (`ATT`) | **Pin `32`** | Digital (PC5) | Qua mạch dịch mức $5\text{V} \rightarrow 3.3\text{V}$ |
| | Xung nhịp (`Clock / SCK`) | `CLK` | **Pin `33`** | Digital (PC4) | Qua mạch dịch mức $5\text{V} \rightarrow 3.3\text{V}$ |
| **Giám sát An toàn Gầm** | Đọc trạng thái Nút E-Stop | `ESTOP_SENSE` | **Pin `18`** | `INPUT_PULLUP` (INT3) | Phát hiện khi E-Stop vật lý bị nhấn |
| | Đo áp Pin LiPo 3S ($12.6\text{V}$) | Cầu phân áp | **Pin `A0`** | Analog (ADC0) | Qua cầu trở $20\text{k}\Omega / 10\text{k}\Omega$ ($\le 4.2\text{V}$) |

---

## 4. BẢNG TỔNG KẾT TRẠNG THÁI CHỐT PHÂN HỆ 1 (DI CHUYỂN)

| Hạng mục | Quyết định của bạn | Trạng thái | Hệ quả Kỹ thuật đã cập nhật vào thiết kế |
| :--- | :--- | :---: | :--- |
| **1. Bánh Mecanum** | **Bánh Mecanum $\varnothing 60\text{ mm}$** (theo Excel BOM) | **ĐÃ CHỐT** | Bán kính $R=30\text{ mm}$. Hạ cụm gá JGB37 + trục bánh xuống $z=30\text{ mm}$; lực kéo tăng $67\%$, vận tốc có tải đạt $1.28\text{ m/s}$. |
| **2. Động cơ 4 bánh** | **JGB37-520 12V 530 RPM (18.8:1)** | **ĐÃ CHỐT** | Đi kèm bánh $\varnothing 60\text{ mm}$ giúp mô-men chạy thẳng nằm trong ngưỡng định mức ($0.63 \le 0.67\text{ kg}\cdot\text{cm}$); khóa trần PWM trong code (`80 / 160 / 220`). |
| **3. Kiểu lái tay cầm PS2** | **Kiểu A:** Cần Trái (`LY` Tiến/Lùi, `LX` Dạt ngang), Cần Phải (`RX` Xoay) · `L1` Chậm, `R1` Nhanh | **ĐÃ CHỐT** | Đã định nghĩa hàm ánh xạ 3 trục $(V_x, V_y, \omega_z)$, vùng chết `±12`, khóa Analog Red Mode `0x73` và chuẩn hóa vector 4 bánh. |
| **4. Sơ đồ chân Mega 2560** | **Chưa rõ** (Dùng bảng đề xuất Mục 3 làm mẫu tham chiếu) | **CHỜ CHỐT** | Toàn bộ số chân sẽ gom vào khối `#define` đầu file `config_robot1.h` để đổi 1 dòng là khớp khi bạn cắm dây thật. |
| **5. Mã nguồn `.ino` / `.h`** | **Chưa tạo file code** (Chỉ cập nhật tài liệu `.md`) | **ĐÃ TUÂN THỦ** | Giữ sạch thư mục `d:\Desktop\robotic` chỉ gồm 4 file `.md`, không có file code, không đẩy Git. |
