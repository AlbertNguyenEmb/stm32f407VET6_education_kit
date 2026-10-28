> Review lịch sử trước khi sửa. Xem [thay đổi mới](power-debug-changes.md) cho trạng thái hiện tại.

# Review nguồn và debugger — bản 5e0d89d

## Kết luận
Chưa sẵn sàng chế tạo. Review dựa trên schematic/netlist, chưa kiểm tra phần cứng hoặc firmware debugger.

## Các điểm đã sửa đúng so với bản trước
- DBG_SWDIO nối U1 pin 72, U5 pin 43, J3 pin 2.
- DBG_SWCLK nối U1 pin 76, U5 pin 45, J3 pin 3.
- DBG_T_NRST nối U1 pin 14, U5 pin 46, J3 pin 4.
- HSE F103 nối U5 pin 5/6 tới tinh thể Y2 và tụ tải.
- USBLC6 chân nguồn nối VBUS USB; CC1/CC2 có điện trở 5.1 kohm riêng.
- VBAT F407 nối 3.3 V, có bypass xuống GND. Chưa có pin dự phòng RTC.

## Nguồn: cần sửa trước khi cấp 12 V
- J1 -> F1 -> Q1 -> U2 AMS1117-3.3. Net +5V_EXT chưa có bộ chuyển đổi 12 V -> 5 V. Không coi đây là đầu vào 12 V đã hoàn chỉnh.
- Thêm buck 12 V -> 5 V, chọn linh kiện theo dòng tải toàn kit và tính nhiệt; kiểm tra giới hạn điện áp đầu vào của đúng hãng AMS1117 được mua.
- Chưa có lựa chọn nguồn USB/DC và chống cấp ngược. +5V_DBG_USB đang chỉ cấp debugger, chưa cấp target.
- Kiểm tra VGS tối đa AO3401A kể cả quá độ, phương án clamp gate, bảo vệ đầu vào và thông số cầu chì. Chưa chốt được chỉ từ symbol.
- Kiểm tra tụ đầu ra/ESR theo datasheet đúng regulator, tính P=(Vin-3.3)*I; xem xét buck 3.3 V cho tải Ethernet và ngoại vi.
- Bổ sung test point cho input, 5V_SYS, 3V3_SYS, 3V3_DBG và GND.

## Debugger: kết nối cơ bản có tiến bộ, chức năng chưa xác nhận
- F103C8 chưa chứng minh có firmware hợp lệ hỗ trợ đầy đủ ST-LINK + SWO + VCP. Chốt giải pháp firmware/phần cứng trước khi chốt pinout.
- PA13/PA14 của chính F103 đang chỉ có label SWDIO/SWCLK, chưa có header nạp debugger. J3 hiện là header SWD target.
- Chưa có đường SWO từ PB3 của F407 và UART VCP giữa F407/debugger.
- Thêm jumper/cách ly SWDIO, SWCLK, NRST (và SWO/UART nếu có) để debugger onboard không tranh chấp với probe ngoài.
- Hai miền nguồn target/debugger độc lập: kiểm tra VTref, trạng thái mất nguồn và nguy cơ cấp ngược qua chân tín hiệu.
- USB D+ pull-up R7 nối PA8 của F103: firmware phải chủ động điều khiển đúng; không coi việc gắn R7 là bảo đảm USB enumerate.
- Xem xét reset target với nút/pull-up/tụ theo hướng dẫn ST; hiện NRST target chủ yếu nối tới debugger/header.

## Tổ chức project KiCad 10
Project dùng nhiều top-level sheets sẵn có của KiCad 10, không phải cây hierarchical sheet của KiCad cũ.
- MCU_Core: tệp STM32F407VET6_Minimum_System.kicad_sch; MCU, HSE, BOOT, VCAP, decoupling, analog supply.
- Power_Management: power_management.kicad_sch; jack, cầu chì, MOSFET và regulator target.
- Onboard_Debugger: onboard_debugger.kicad_sch; giữ nguyên mạch debugger.

Nguồn +3.3V tại đầu ra regulator được chuyển từ local label sang global label để giữ kết nối xuyên sheet. Không sửa chức năng điện trong lần tổ chức này. Netlist trước/sau được so sánh theo toàn bộ tập chân ref/pin, không dựa vào tên net vốn thay đổi theo sheet.

Khi thêm mạch, tạo các sheet Basic_IO, Analog_Sensors, CAN, Ethernet, Storage_USB và Expansion. Chưa tạo sheet trống để tránh thể hiện các chức năng chưa thiết kế như đã có.
