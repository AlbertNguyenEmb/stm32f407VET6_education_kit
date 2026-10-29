# Peripheral mapping dự kiến — STM32F407VET6 LQFP100

Đối chiếu datasheet DS8626 Rev12 và RM0090 Rev22, bảng AF và DMA. Đây là đề xuất phân bổ, chưa được áp vào schematic/CubeMX hoặc kiểm chứng trên phần cứng. Chân F103 phía debugger không nằm trong bảng này. Firmware CMSIS-DAP cụ thể chưa được xác nhận; không gọi nó là ST-LINK.

| Nhóm | Chức năng | GPIO | Chân LQFP100 | Peripheral | AF/mode | Ghi chú |
|---|---|---|---|---|---|---|
| Core | HSE 8 MHz | PH0,PH1 | 12,13 | OSC_IN,OSC_OUT | Dedicated | Tụ tải theo CL tinh thể |
| Core | LSE 32.768 kHz | PC14,PC15 | 8,9 | OSC32_IN,OSC32_OUT | Dedicated | Bổ sung tinh thể và tụ; dành riêng RTC |
| Core | Reset | NRST | 14 | NRST | Dedicated | Nút reset, pull-up, tụ theo ST |
| Core | Boot | BOOT0,PB2 | 94,37 | BOOT0,BOOT1 | GPIO | Pull-down mặc định; jumper BOOT0 lên 3V3 |
| Core | RTC backup | VBAT | 6 | VBAT | Power | Nguồn pin dự phòng không được nối thẳng với 3V3 |
| Debug | SWD | PA13,PA14 | 72,76 | SWDIO,SWCLK | AF0 | Giữ F103 PB7/PB8 qua jumper cách ly |
| Debug | SWO | PB3 | 89 | JTDO/TRACESWO | AF0 | F103 receiver tùy firmware; giữ header ngoài |
| Debug | VCP target | PB6,PB7 | 92,93 | USART1_TX,USART1_RX | AF7 | TX F407 nối RX debugger; RX F407 nối TX debugger |
| USB | USB FS device | PA11,PA12 | 70,71 | DM,DP | AF10 | Cổng target riêng debugger |
| USB | USB VBUS sense | PA9 | 68 | OTG_FS_VBUS | AF10 | Thiết kế sensing theo AN4879, kiểm tra trạng thái mất nguồn |
| Ethernet | RMII clock | PA1 | 24 | REF_CLK | AF11 | 50 MHz từ PHY/oscillator; chỉ một nguồn clock |
| Ethernet | PHY management | PA2,PC1 | 25,16 | MDIO,MDC | AF11 | Pull-up MDIO theo PHY |
| Ethernet | RMII receive | PA7,PC4,PC5 | 32,33,34 | CRS_DV,RXD0,RXD1 | AF11 | Dành cho RMII, không MII |
| Ethernet | RMII transmit | PB11,PB12,PB13 | 48,51,52 | TX_EN,TXD0,TXD1 | AF11 | Không dùng SPI2 trên PB13 |
| Ethernet | PHY control | PD10,PD11 | 57,58 | PHY_RESET_N,PHY_INT_N | GPIO/EXTI11 | Pin INT phụ thuộc mode REFCLKO/INT của PHY; có thể polling |
| Storage | SDIO data | PC8,PC9,PC10,PC11 | 65,66,78,79 | D0,D1,D2,D3 | AF12 | SDIO 4 bit; pull-up data |
| Storage | SDIO control | PC12,PD2 | 80,83 | CK,CMD | AF12 | Pull-up CMD, không pull-up CK |
| Storage | Card detect | PD7 | 88 | SD_DETECT_N | GPIO/EXTI7 | Pull-up, kiểm tra switch của socket |
| CAN1 | CAN nội | PD0,PD1 | 81,82 | CAN1_RX,CAN1_TX | AF9 | Transceiver logic 3V3; termination 120 ohm bật/tắt |
| CAN1 | Transceiver control | PE8 | 39 | CAN1_STBY | GPIO | Chọn transceiver có STBY và bias mặc định |
| SPI shared | SPI2 bus | PB10,PC2,PC3 | 47,17,18 | SCK,MISO,MOSI | AF5 | MCP2515 + ADS1220 + header; từng CS riêng |
| CAN external | MCP2515 control | PE7,PD4,PD5 | 38,85,86 | CAN_EXT_CS_N,CAN_EXT_INT_N,CAN_EXT_RESET_N | GPIO/EXTI4 | Classical CAN, thạch anh riêng; chọn clock theo VDD/datasheet |
| CAN external | Transceiver control | PE12 | 43 | CAN_EXT_STBY | GPIO | Transceiver riêng; termination riêng |
| Precision ADC | ADS1220 control | PE10,PD6 | 41,87 | ADC_EXT_CS_N,ADC_EXT_DRDY_N | GPIO/EXTI6 | SPI2; reference nội 2.048 V hoặc external qua lựa chọn; lọc đầu vào |
| Sensors | I2C1 bus | PB8,PB9 | 95,96 | SCL,SDA | AF4 | BH1750 + SSD1306 + header; một bộ pull-up hiệu dụng |
| Sensors | DHT22 | PD3 | 84 | DHT_DATA | GPIO/EXTI3 | Pull-up 3V3; đo xung bằng timer/DWT; không dùng chân này UART |
| Basic IO | LED1..4 | PE2,PE3,PE4,PE5 | 1,2,3,4 | LED1,LED2,LED3,LED4 | GPIO | Mỗi LED: jumper + điện trở riêng |
| Basic IO | Button1..3 | PE0,PE1,PE15 | 97,98,46 | BTN1_N,BTN2_N,BTN3_N | GPIO/EXTI0,1,15 | Đổi nút 3 sang PE15 để không trùng EXTI6 của ADC DRDY |
| Basic IO | Buzzer passive | PB0 | 35 | TIM3_CH3 | AF2 | Driver MOSFET/transistor; jumper |
| Analog | ADC nội header | PA0,PC0,PB1 | 23,15,36 | ADC1_IN0,ADC1_IN10,ADC1_IN9 | Analog | Chỉ dải 0..VREF+; bảo vệ/lọc, không tín hiệu 5V/12V |
| Analog | DAC header | PA4,PA5 | 29,30 | DAC_OUT1,DAC_OUT2 | Analog | Buffer ngoài nếu tải yêu cầu; không SPI1 trên PA5 |
| Timer | PWM header | PE9,PE11,PE13,PE14 | 40,42,44,45 | TIM1_CH1,CH2,CH3,CH4 | AF1 | 4 duty độc lập, chung PSC/ARR nên chung tần số nền |
| Timer | Encoder header | PD12,PD13 | 59,60 | TIM4_CH1,TIM4_CH2 | AF2 | Encoder A/B; xử lý đầu vào phù hợp mức 3V3 |
| Timer | Input capture | PA3 | 26 | TIM2_CH4 | AF1 | Timer 32 bit, mặc định IRQ |
| Expansion | SPI header CS | PA15 | 77 | SPI_EXP_CS_N | GPIO | Dùng chung SPI2; tắt full JTAG, giữ SWD+SWO |
| Expansion | UART spare | PD8,PD9 | 55,56 | USART3_TX,USART3_RX | AF7 | IRQ mặc định; DMA TX xung đột SPI2 |
| Power | Mux status | PC13 | 7 | POWER_EXT_STATUS | GPIO input | ST TPS2116 open-drain; pull-up 3V3 |
| Expansion | Spare GPIO | PE6,PB4,PB5,PB14,PB15,PC6,PC7,PA6,PA8,PA10,PD14,PD15 | 5,90,91,53,54,63,64,31,67,69,61,62 | GPIO reserve | GPIO | PB4 cần nhả JTAG; PA10 dành ID nếu sau này thêm USB OTG host |

## Nguồn và chân chuyên dụng
VDD: 11,19,28,50,75,100; VSS: 10,27,74,99; VSSA:20; VREF+:21; VDDA:22. VCAP1:49, VCAP2:73, mỗi chân dùng tụ đúng hướng dẫn ST, không cấp ngoại vi. VBAT:6.

## DMA dự kiến không trùng stream cho các luồng chính
| Chức năng | DMA / stream / channel |
|---|---|
| ADC1 scan 3 kênh | DMA2 / S0 / C0 |
| SDIO RX hoặc TX | DMA2 / S3 / C4 |
| USART1 RX / TX | DMA2 / S2 / C4; DMA2 / S7 / C4 |
| SPI2 RX / TX | DMA1 / S3 / C0; DMA1 / S4 / C0 |
| I2C1 RX / TX (nếu cần) | DMA1 / S0 / C1; DMA1 / S7 / C1 |
| DAC1 / DAC2 | DMA1 / S5 / C7; DMA1 / S6 / C7 |
| Ethernet | DMA tích hợp MAC, không chiếm DMA1/2 |

TIM6 dành trigger DAC, TIM7 có thể làm HAL timebase; SysTick cho FreeRTOS. Buzzer/PWM/encoder/input capture dùng hardware timer và IRQ trong cấu hình nền, không tự thêm DMA. SDIO RX/TX dùng luân phiên một stream. SPI2 chia sẻ tuần tự, CS riêng và mutex FreeRTOS; đổi mode/baud trước giao dịch. USART3 TX có các lựa chọn DMA trùng S3/S4 của SPI2, dùng IRQ hoặc lập lại kế hoạch. Buffer DMA phải ở SRAM DMA truy cập được, không CCM; chia SRAM cho Ethernet descriptors, network buffers và SDIO.

EXTI chủ động: buttons 0/1/15, DHT 3, MCP2515 4, ADC 6, SD detect 7, PHY 11. Chưa xung đột EXTI. Chân spare khi thêm ngắt phải kiểm tra lại.

## Các lựa chọn ngoại vi
- ADS1220: ADC SPI độ phân giải 24 bit; độ chính xác thực phụ thuộc reference, front-end, nhiễu và calibration. Không gán hiệu năng chính xác chỉ theo số bit.
- BH1750: cảm biến ánh sáng I2C; quang trở thường phải dùng ADC.
- SSD1306: OLED I2C, không phải LED bảy đoạn.
- Hai cổng CAN: CAN1 nội + MCP2515 SPI, hai transceiver riêng; không dùng CAN2 nội thay yêu cầu controller ngoài.
- Ethernet RMII: chốt PHY, clock 50 MHz và strap trước khi nối. Nếu chọn LAN8742A REFCLKO dùng chung chân nINT thì không dùng đồng thời IRQ trên chân đó.
- Hỗ trợ USB target ở device mode. Host/OTG cần VBUS switch/current-limit và bổ sung phần cứng.

## Nguồn tham khảo
https://www.st.com/resource/en/datasheet/stm32f407ve.pdf
https://www.st.com/resource/en/reference_manual/dm00031020-stm32f405415-stm32f407417-stm32f427437-and-stm32f429439-advanced-armbased-32bit-mcus-stmicroelectronics.pdf
