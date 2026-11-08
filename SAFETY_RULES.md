# QUY TẮC AN TOÀN MÃ NGUỒN & VẬN HÀNH ROBOT 1 (LOT HANDLER 400)
**Phiên bản:** 1.1 (đã rà soát lại theo BOM và quy trình trong `Lot Handler gọn 400.html`)
**Phần cứng:** Arduino Mega 2560 · 2× XY-160D (4× JGB37-520 qua bánh răng côn 90°) · 2× NEMA 17 + 2× A4988 (T8, MGN12) · 3× N20 1:150 + 2× TB6612 · 3× MG996R (nguồn riêng XL4015 6 V) · PS2 không dây · LiPo 3S 5400 mAh

> [!IMPORTANT]
> Các giá trị đánh dấu **[CẦN ĐO]** là ước tính, chưa đo trên xe thật. Phải đo rồi ghi vào `config_robot1.h` (Điều 9), không dùng số trong tài liệu này làm hằng số cuối.

---

## ĐIỀU 1: DỪNG KHẨN CẤP & MẤT TÍN HIỆU
1. **E-stop vật lý cắt bằng phần cứng.** Nút E-stop (có trong BOM) mắc nối tiếp trên đường 12 V cấp cho XY-160D, A4988, TB6612 và buck servo. Mega và logic vẫn có điện để báo lỗi. Phần mềm chỉ đọc trạng thái nút để vào `STATE_ESTOP_LOCKED`, không được là lớp bảo vệ duy nhất.
2. **Deadman PS2 & Chống "Phóng Xe Điên" khi rớt Analog Mode:**
   - Gói tin PS2 chỉ hợp lệ khi tay cầm đang ở **Analog Red Mode (ID = `0x73` hoặc `0x79`)** và không phải gói `0xFF` toàn phần (đứt dây `DAT`). Nếu tay cầm tự ngủ hoặc rớt về Digital Mode (`0x41`), phải khóa vận tốc bánh về `0` ngay lập tức (vì ở mode `0x41`, cần gạt trả về `0` thay vì `128`, nếu trừ `128` xe sẽ vọt hết ga).
   - Quá **100 ms** không có gói hợp lệ thì: PWM 4 bánh = 0 (`ENA/ENB = 0`), `STBY` của 2 mạch TB6612 = `LOW` (dừng 3 N20), dừng phát xung `STEP` nhưng **GIỮ NGUYÊN `STEP_EN = LOW` cho 2 A4988 nếu `Z > 11 mm`** (vì vít me T8 bước 8 mm 4 đầu mối không tự hãm khi treo tải nặng — nếu thả `EN`, khay và FOUP 2 kg sẽ tự trôi sập xuống đáy!), vào `STATE_FAILSAFE`. Servo giữ nguyên trạng thái.
   - Nếu 100 ms gây dừng giả khi thử, nới thành 150–200 ms và ghi vào config.
3. **Watchdog ATmega2560:** bật WDT 1 s, `wdt_reset()` cuối `loop()`. Bootloader Mega cũ có thể kẹt vòng reset khi bật WDT, nên thử reset do WDT trên bàn trước khi tin vào nó.
4. **Phục hồi:** nhả E-stop vật lý, giữ `START + SELECT` 2 s, rồi bắt buộc chạy lại Homing giá nâng. Nút/tay cầm không được tự khôi phục chuỗi đang dở.

---

## ĐIỀU 2: HAI NEMA 17 NÂNG Z
1. **Chung `STEP` và `DIR`, tách `EN` cho từng driver.** Chung STEP/DIR để không lệch xung. Mỗi A4988 có chân `EN` riêng để homing dừng từng bên. (Bản 1.0 chỉ nói chung chân, mâu thuẫn với squaring.)
2. **Squaring khi homing:** hạ chậm ~1 mm/s (400 step/s với 400 step/mm). Bên nào chạm công tắc trước thì tắt `EN` bên đó, bên kia chạy tiếp đến khi chạm. Chênh số bước giữa hai bên vượt ngưỡng **[CẦN ĐO]** (đề xuất ≤ 1 mm = 400 bước) thì báo lỗi lệch khung, không chạy tiếp.
3. **Soft-limit theo hành trình thật** trong HTML: tâm khay từ **11 đến 297 mm**. Bản 1.0 ghi 0–300 mm là sai. Quan hệ giữa vị trí công tắc và Z = 11 mm phải đo lại.
4. **Tốc độ Z** lấy từ giới hạn tần số xung của Mega (có ramp) và mô-men thật ở 1/16 vi bước. Đừng giả định vài chục mm/s: ở 400 step/mm, 10 mm/s đã là 4000 step/s.

---

## ĐIỀU 3: KHÓA LIÊN ĐỘNG THEO CHUỖI TÁC VỤ
Quy trình thật có lúc phải hạ Z khi khay đang ra (đặt FOUP xuống đáy ô, hạ 40 mm) và thả lô ở Z = 11 mm khi khay đang ra. Vì vậy **không dùng luật cấm cứng "hạ Z khi khay đang ra"** như bản 1.0. Khóa theo pha của chuỗi:

| Tình huống | Quy tắc |
| :--- | :--- |
| Khay đang ra (`tx > 0`) | Nút Z thủ công bị khóa. Z chỉ đổi bởi bước đã khai báo trong chuỗi (đặt FOUP: hạ 40 mm, thả lô: Z = 11 mm). |
| Thu khay lấy lô (WIP) | Bước hạ khay đứng riêng sau khi thu khay xong. HTML ghi hạ cùng lúc thu thì lô đập vào xe. |
| Thò khay | Chỉ khi Z đã tới cao độ tầng mục tiêu, sai số trong ngưỡng config. |
| Mở sàn khay | Chỉ khi xe đã dừng, khay đã ra tới ô, và bộ kẹp đã nhả hoặc đang không giữ tải. |
| Mang FOUP (Z > 100 mm) | Trần tốc độ xe 30% (PWM ≤ 75/255). |
| Chuỗi chưa homing xong | Mọi lệnh cơ cấu bị khóa, chỉ lái xe ở docking mode. |

Mọi khóa liên động nằm trong một hàm kiểm tra duy nhất gọi trước mỗi lệnh chấp hành, cả khi lệnh đến từ tay cầm lẫn từ chuỗi.

---

## ĐIỀU 4: 3 ĐỘNG CƠ N20 (KHAY, 2 RAY BỘ KẸP)
1. **Timeout không dùng số cố định 1,8 s.** N20 1:150 ở 12 V quay vài chục vòng/phút, chạy 140 mm có thể mất vài giây, nên 1,8 s có thể cắt oan. Cách làm: đo thời gian hết hành trình (khay 140 mm; bộ kẹp +20, −150, −70 mm) **[CẦN ĐO]**, đặt timeout = 1,5 × thời gian đo, ghi trong config.
2. Chạm công tắc hành trình thì ngắt PWM ngay (polling < 5 ms hoặc interrupt), không dùng `delay()`.
3. Không có encoder nên vị trí giữa hành trình chỉ là ước lượng theo thời gian. Mỗi lần chạm công tắc ở đầu ray thì cập nhật lại mốc 0.

---

## ĐIỀU 5: 3 SERVO MG996R & NGUỒN 6 V
1. Không khởi động 3 servo trong cùng một chu kỳ, cách nhau tối thiểu 150 ms.
2. **Chỉ `detach()` servo kẹp** sau khi siết xong 0,5 s, và chỉ khi đã thử trên bàn là vít me M5 (bước 0,8) không bị kéo ngược với lực kẹp ~100–150 N. Nếu bị kéo ngược thì giữ xung.
3. **Không `detach()` hai servo sàn khay khi đang có tải trên sàn.** Sàn đang đỡ lô/FOUP, mất xung thì tấm sàn có thể bị đẩy mở tùy cơ cấu cần gạt.
4. Mega và servo dùng chung GND, nhưng 6 V servo đi từ buck XL4015 riêng, không lấy từ buck 5 V của Mega.

---

## ĐIỀU 6: MECANUM & BÁNH RĂNG CÔN
1. Dốc gia tốc mềm: PWM đổi tối đa 15 đơn vị / 20 ms (~250 ms từ 0 tới ga tối đa). Khi đảo chiều, qua 0 rồi mới tăng ngược.
2. Chuẩn hóa vector 4 bánh: nếu `w_max > 255` thì chia đồng dạng cả bốn, giữ nguyên hướng đi. `KINEMATIC_SUM = 283 mm` (110 + 173).
3. Hai chế độ tốc độ: Docking (giữ `L1`) trần PWM 80/255, Cruising trần 160/255. Đây là **[CẦN ĐO]**: tốc độ thật phụ thuộc điện áp pin, tải và trượt bánh. Mức 2,1 m/s trong HTML là ước tính tại vòng tua có tải.
4. JGB37-520 530 rpm yếu mô-men (HTML: gần mức kẹt khi chạy ngang hoặc tăng tốc). Nếu thấy motor nóng, giảm trần PWM hoặc đổi loại ~200 rpm theo khuyến nghị trong HTML.

---

## ĐIỀU 7: GIÁM SÁT PIN LIPO 3S
1. Đo qua cầu phân áp vào A0, chọn R1/R2 sao cho 12,6 V (đầy) cho dưới 5 V tại chân ADC (ví dụ 20 kΩ/10 kΩ cho ~4,2 V).
2. Cảnh báo ở < 10,2 V, khóa động lực ở < 9,6 V. Điện áp sụt khi tải lớn nên phải lọc trung bình và chỉ kích hoạt khi dưới ngưỡng liên tục ≥ 2 s, có độ trễ (hysteresis) khi hết cảnh báo, để tránh cắt oan khi xe tăng tốc.

---

## ĐIỀU 8: KIỂM THỬ TRƯỚC KHI HẠ SÀN *(đề xuất mặc định, bạn chưa xác nhận)*
1. Bench: kê xe để 4 bánh và khay lơ lửng.
2. Nhấn tay từng công tắc hành trình, xem trạng thái trên Mega.
3. Chạy bánh 20%, kiểm tra chiều quay tiến/lùi/dạt.
4. Hạ sàn, thử tải tăng dần: không tải, 1 WIP (0,38 kg), FOUP (2 kg).
5. Kiểm tra E-stop vật lý cắt được động lực, và deadman, WDT hoạt động đúng, trước khi chạy có tải.

---

## ĐIỀU 9: CẤU TRÚC CẤU HÌNH `config_robot1.h` *(bạn đã chọn)*
- Mọi chân PIN, `STEPS_PER_MM`, giới hạn Z/khay/bộ kẹp, góc servo, timeout, ngưỡng pin, ngưỡng deadman nằm trong `config_robot1.h`, mỗi giá trị có chú thích đơn vị và nguồn (đo hay ước tính).
- Dùng `static_assert` của C++11 (không phải `#static_assert`) để kiểm tra lúc biên dịch: Z_MIN < Z_MAX, không trùng chân, timeout > 0, ngưỡng pin hợp lý.
- Các giá trị **[CẦN ĐO]** để ở một khối riêng đầu file, dễ rà trước khi nạp.

---

## ĐIỀU 10: MÔ PHỎNG GODOT KHÔNG PHẢI SỐ ĐO THẬT
Tốc độ cơ cấu trong `robot1_controller.gd` (Z 90 mm/s, khay/bộ kẹp 140 mm/s, kẹp 60 mm/s) là giả định của tôi, chưa đối chiếu với giới hạn xung của Mega hay N20 1:150 thật. Dùng mô phỏng để kiểm tra hình học và trình tự. Không dùng nó để chọn timeout hay tốc độ firmware.
