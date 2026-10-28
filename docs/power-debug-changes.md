# Sửa nguồn và debugger — 2026-10-28

## Đã sửa trong schematic
- Thay đường jack -> MOSFET -> AMS1117 của target bằng F1, D1 SS34 chống đảo cực, D2 SMBJ15A và AP63205WU-7 tạo 5 V.
- AP63203WU-7 tạo 3,3 V cho target. L1=4,7 uH, L2=3,9 uH; bootstrap 100 nF; mỗi đầu ra có 2 tụ 22 uF. Tham khảo datasheet Diodes Figure 21, Tables 2/3.
- D3/D4 ghép hai nguồn, chống cấp ngược DC/USB; không ưu tiên cố định một nguồn. JP1 USB_TARGET mặc định tháo. Đây là nguồn bench 12 V, không phải nguồn nối trực tiếp xe.
- Thêm TP1–TP5 cho VIN_PROT, DC_5V, SYS_5V, 3,3 V và GND.
- Giữ nguồn 3,3 V debugger riêng; không nối hai đầu ra regulator với nhau.
- Thêm JP2/JP3/JP4 cách ly F103 PB7/PB8/PB9 khỏi target PA13/PA14/NRST.
- J3 giữ nguyên header debug target. J4 là header nạp firmware cho F103: 1=3V3_DBG (VTref), 2=PA13/SWDIO, 3=GND, 4=PA14/SWCLK, 5=NRST_DBG, 6=NC.
- J5 đưa ra target: 1=3V3 target (VTref), 2=PB3/SWO, 3=GND, 4=PD8/USART3_TX, 5=PD9/USART3_RX, 6=NRST target.
- Thêm R8 10 kohm, C36 100 nF và SW3 reset target.
- Rút gọn các dây USB thừa sau label; giữ kết nối cả hai cặp A6/B6 và A7/B7 qua USBLC6.

## Cách sử dụng
- Đầu vào DC: adapter ổn áp 12 V, đúng cực giữa dương. Chưa chế tạo/đo thực tế.
- USB target: chỉ lắp JP1 sau khi xác định tổng dòng debugger + target phù hợp host, dây USB và cầu chì. USB enumeration/current negotiation chưa được xác nhận. Cầu chì không thay thế điều khiển ngân sách dòng USB.
- Debug onboard: lắp JP2–4 khi cả hai miền nguồn đã có điện. Tháo cả ba khi dùng probe ngoài hoặc khi một miền mất nguồn; mạch chưa có cách ly tự động/chống back-power cho tín hiệu.
- J3/J4/J5 có chân VTref, không dùng chân này để nối thêm một nguồn 3,3 V đang hoạt động.

## Chưa hoàn tất
- Firmware CMSIS-DAP cụ thể chưa chọn/build/kiểm chứng. Pinout yêu cầu giữ PB7=SWDIO, PB8=SWCLK, PB9=NRST. Firmware phải xử lý R7 USB pull-up qua PA8 và HSE 8 MHz đúng mạch.
- SWO/VCP onboard chưa được xác nhận; hiện các tín hiệu target chỉ đưa ra J5. Chưa thể tuyên bố nạp/debug qua USB đã hoạt động.
- Chốt MPN và đối chiếu footprint/định mức cho cuộn cảm, cầu chì, jack, tụ; kiểm tra dung lượng hữu dụng khi phân cực DC. Thông số Isat/Irms và nhiệt cần kiểm tra theo linh kiện thực mua.
- Layout PCB, mô phỏng tải, đo ripple, khả năng khởi động và kiểm tra nhiệt chưa thực hiện. Không gửi sản xuất chỉ dựa vào ERC.

## Kết quả kiểm tra
- KiCad 10.0.7 đọc project và xuất PDF/netlist được.
- Kiểm tra 20 nhóm net quan trọng: nguồn, diode/TVS, bootstrap, feedback, reset, header F103 và target: đạt.
- Kiểm tra USB D+/D− từ hai mặt connector tới ESD và F103: đạt.
- ERC toàn project trả mã 5: 100 lỗi pin_not_connected (73 F407, 27 debugger), 2 cảnh báo isolated_pin_label của USB target.
- Sheet Power_Management: 0 lỗi/cảnh báo. Không thay rule severity hay tắt kiểm tra để đạt kết quả này.
- PDF: power-debug-revision.pdf. Báo cáo ERC: power-debug-erc.json.

Tài liệu hardware-review.md ghi hiện trạng TRƯỚC lần sửa này; dùng tài liệu này cho trạng thái mới.
