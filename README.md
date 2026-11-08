# Robot 1 — Lot Handler 400 (`robo_1`)

Kho lưu trữ hồ sơ kỹ thuật, phân tích phần cứng, quy tắc an toàn và mã nguồn điều khiển cho **Robot 1 (Lot Handler gọn 400 mm)** sử dụng vi điều khiển **Arduino Mega 2560**.

> **Nguyên tắc phát triển:** Mọi thông số cơ khí, điện tử, hành trình và sơ đồ chân đều phải được đối chiếu chéo từ BOM thực tế và mô hình 3D, ghi rõ thành tài liệu kỹ thuật (`.md`) và xác nhận đầy đủ trước khi viết mã nguồn.

---

## 📂 Danh Mục Tài Liệu Kỹ Thuật Hiện Có

1. **[`HARDWARE_ANALYSIS.md`](./HARDWARE_ANALYSIS.md) — Giải Phẫu Tổng Thể Linh Kiện & 6 Điểm Nghẽn Phần Cứng**
   - Đối chiếu chéo giữa file Excel BOM (`Linh_kien_gop_Robot1_Robot2.xlsx`), mô hình CAD 3D (`Lot Handler gọn 400.html`) và dữ liệu vật lý.
   - Phân tích 6 vấn đề kỹ thuật cốt lõi:
     1. Tương thích giữa Servo MG996R ($180^\circ$ vs $360^\circ$) và bước ren vít me M5 ($0.8\text{ mm}$) trên bộ kẹp chung.
     2. Hiện tượng trượt ngược không tự hãm (back-driving) của trục vít nâng Z (T8 bước $8\text{ mm}$, 4 đầu mối) khi treo tải FOUP $2\text{ kg}$.
     3. Sự khác biệt kích thước bánh Mecanum giữa Excel ($60\text{ mm}$) và thiết kế 3D ($100\text{ mm}$).
     4. Vận tốc thực tế của 3 động cơ N20 $12\text{V } 1:150$ kéo thanh răng module $0.5$ ($\sim 30\text{--}45\text{ mm/s}$) và cách chia 4 kênh trên 2 mạch TB6612FNG.
     5. Giao thức điều khiển 3 chân cách ly quang (`ENA/ENB`, `IN1/IN2`) của 2 module cầu H XY-160D.
     6. Cơ chế khóa chế độ Analog Red (`0x73`) chống lỗi vọt ga của tay cầm PS2 không dây.

2. **[`DOC_01_DI_CHUYEN_MECANUM.md`](./DOC_01_DI_CHUYEN_MECANUM.md) — Nghiên Cứu Chuyên Sâu Phân Hệ 1: Hệ Thống Di Chuyển Đa Hướng**
   - Giải phẫu chi tiết 8 nhóm linh kiện của hệ thống di chuyển: Khung nhôm 2020, 4 động cơ JGB37-520 530 RPM, 4 bộ truyền bánh răng côn $90^\circ$ (1:1) + trục $\varnothing 8\text{ mm}$ + vòng bi 608, 4 bánh Mecanum (loại A/B), 2 mạch XY-160D, Arduino Mega 2560, bộ thu PS2 + mạch dịch mức $5\text{V}\leftrightarrow 3.3\text{V}$, và nguồn LiPo 3S.
   - Phân tích khe hở lắp ráp cơ khí ($1.5\text{--}2.5\text{ mm}$), bài toán mô-men kéo khi chở FOUP nặng $9.73\text{ kg}$, và chiều quay cơ học của cặp bánh răng côn Trước/Sau.
   - **Checklist 5 câu hỏi thực tế cần chốt trước khi viết code Phân hệ Di chuyển.**

3. **[`SAFETY_RULES.md`](./SAFETY_RULES.md) — Bộ Quy Tắc An Toàn Mã Nguồn & Vận Hành (v1.1)**
   - 10 điều khoản an toàn bắt buộc: E-stop cắt nguồn động lực 12V phần cứng, Deadman PS2 $100\text{ ms}$, Watchdog Timer $1\text{ s}$, giữ dòng `STEP_EN = LOW` chống sập trục Z, khóa liên động theo pha tác vụ, dốc gia tốc mềm $250\text{ ms}$, và bảo vệ điện áp thấp cho Pin LiPo 3S.

---

## 🗺️ Lộ Trình Nghiên Cứu & Triển Khai Từng Phân Hệ

| Giai đoạn | Phân hệ | Tài liệu Thiết kế | Trạng thái |
| :---: | :--- | :--- | :---: |
| **0** | Giải phẫu tổng thể BOM & Quy tắc an toàn chung | `HARDWARE_ANALYSIS.md`, `SAFETY_RULES.md` | ✅ Đã lên tài liệu |
| **1** | **Hệ thống Di chuyển Đa hướng (4× JGB37-520 + Côn $90^\circ$ + Mecanum + 2× XY-160D + PS2)** | `DOC_01_DI_CHUYEN_MECANUM.md` | 🔄 **Đang chờ chốt 5 câu hỏi thực tế** |
| **2** | Hệ thống Nâng Hạ Trục Z (2× NEMA 17 + 2× A4988 + Vít T8 + Ray MGN12 + 2× Công tắc hành trình) | `DOC_02_TRUC_NANG_Z.md` *(Sắp tới)* | ⏳ Chờ sau Phân hệ 1 |
| **3** | Hệ thống Khay Trượt & Bộ Kẹp Chung (3× N20 + 2× TB6612FNG + 3× Servo MG996R + Nguồn 6V) | `DOC_03_KHAY_VA_BO_KEP.md` *(Sắp tới)* | ⏳ Chờ sau Phân hệ 2 |
| **4** | Chuỗi Tự Động Hóa Thi Đấu (Lấy Lô WIP 3 tầng, Kéo/Đẩy FOUP 2kg, Thả Lô) & Tích hợp Tổng thể | `DOC_04_CHUOI_TU_DONG_HOA.md` *(Sắp tới)* | ⏳ Chờ sau Phân hệ 3 |
