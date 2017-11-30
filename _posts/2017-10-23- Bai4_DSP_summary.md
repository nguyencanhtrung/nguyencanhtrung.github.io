---
layout: post
title: FPGA architecture
author: Trung C. Nguyen
active: learning
permalink: /learning/fpga/
---
Programmable Device (PD - Thiết bị khả trình) là thiết bị đa chức năng giúp thực thi nhiều ứng dụng khác nhau.
Có hai loại cơ bản của PD nói chung đó là:
    + _Mask-Programmable Chip programmed only by the manufacturer_
    + _Field-Programmable Chip programmed by the end-user_
Phần này chúng ta sẽ thảo luận về dòng _Field-Programmable Chip_. 

Chỉ nói riêng về ***khả năng tái lập trình*** chip (programming - khả năng ghi xoá), thiết bị PD được sử dụng rộng rãi đầu tiên là PROM (Programmable Read-Only Memory). Nó là loại thiết bị hỗ trợ "tái lập trình" một lần duy nhất. Sau đó, hai loại EPRPOM (Erasable Programmable Read-Only Memory - Xoá bằng tia UV...) và EEPROM (Electrically Erasable Programmable Read-Only Memory - Xoá bằng điện) ra đời đã giúp tăng số lần "tái lập trình".

Nói về ***kiến trúc phần cứng*** được sử dụng để mô tả các hàm logic, đầu tiên có thể kể tới là PAL (Programmable Array Logic) và PLA, nó bao gồm một mảng AND gate, kết nối với một mảng OR gate, khi program thì nó sẽ kết nối các cổng sao cho hình thành SOP (Sum Of Product) or POS (Product Of Sum) tương ứng của một hàm logic. Công nghệ này thường chỉ được dùng cho khối logic nhỏ, không áp dụng được với hệ thống lớn, phức tạp.
FPGA (Field-Programmable Gate Array) là kiến trúc tiếp theo nó bao gồm một ma trận các _khối logic khả trình_ (programmable logic) để mô tả hàm logic và một _ma trận switch khả trình_ để kết nối (programmable interconnect) các khối logic. Trong FPGA, _interconnect chiếm phần lớn diện tích của chip_, làm cho nó có tính linh hoạt rất cao, nó có thể mô tả mọi loại thuật toán từ đơn giản đến phức tạp điều mà các kiến trúc trước đây chưa thể đạt được.

Chú ý: PD là tên gọi đầu tiên của các thiết bị khả trình. Sau này chúng còn được gọi là Programmable Logic Device (PLD - Thiết bị logic khả trình).

##1. Tại sao lại cần FPGA?
Với nền tảng FPGA, kỹ sư thiết kế phần cứng hiện tại có thể xây dựng và thử nghiệm hàng loạt các giải pháp trên cùng một con chip FPGA bằng cách "reprogram". Họ đã có sẵn phần cứng của hệ thống, công việc của họ chỉ là lập trình lại "phần cứng" đó để xây dựng chức năng hệ thống.

Chip FPGA có thể mô tả mọi loại thuật toán hay logic từ độ phức tạp thấp đến cao, nên nó có thể ứng dụng trong nhiều lĩnh vực. 

Rút ngắn thời gian để ra được sản phẩm hơn so với dòng ASIC (Application Specific Integrated Circuit). 

Ngoài ra, tính theo chi phí sản xuất dựa trên số lượng sản phẩm, FPGA mất ít chi phí hơn so với ASIC nếu sản xuất ở quy mô nhỏ (từ vài chục đến vài trăm đơn vị). 
[chèn ảnh Giá]

Tuy nhiên, đổi lại FPGA chip lại tiêu thụ nhiều điện năng hơn và có performance thấp hơn so với dòng ASIC cùng loại (cùng xử lý một thuật toán).

Ngày nay, FPGA được sử dụng trong nhiều lĩnh vực: protocol design, communication, finance, digital signal processing etc.

##2. FPGA in a nutshell
Hiện nay, trên thế giới chỉ có một vài công ty sản xuất chip FPGA. Nổi tiếng trong số đó là Xilinx, Altera, Atmel, Lattice Semiconductor, Actel, Quicklogic... Mỗi một công ty đều phát triển những kiến trúc FPGA riêng, phục vụ cho những lĩnh vực khác nhau.

Có 4 dòng FPGA chính xét về mặt kiến trúc (sự phân bố của các logic block và interconnect trên FPGA):
+ Symmetrical FPGA
+ Row-based FPGA
+ Sea-of-gate
+ Hierarchical PLD

![Kiến trúc của FPGA](http://home.mit.bme.hu/~szedo/FPGA/fpga0.gif)

Nếu phân loại về mặt công nghệ programming thì họ lại phân chia theo 4 loại sau:
+ SRAM cells
+ Anti-fuse (tái lập trình 1 lần duy nhất)
+ Flash-based: EPROM transistors, EEPROM transistors

Nếu ta phân loại về dạng Logic Block (cách các logic block mô tả các hàm logic) thì họ chia ra làm 3 loại:
+ Look Up Table (LUT - Xilinx)
+ Multiplexer base (Actel)
+ PLD block (trong kiến trúc Hierarchical PLD)

|   Vendor   |   Architecture   | Logic Block Type | Programming Technology |
|:----------:|:----------------:|:----------------:|:----------------------:|
|   Xilinx   |   Symmetrical    |        LUT       |          SRAM          |
|   Altera   | Hierarchical PLD |     PLD block    |          EPROM         |
|    Actel   |     Row-based    |     MUX based    |        Antifuse        |
| Quicklogic |    Symmetrical   |     MUX based    |        Anti fuse       |

Mỗi loại kiến trúc hay công nghệ "tái lập trình" đều có ưu nhược điểm, dựa vào những đặc tính này chúng được ứng dụng vào những lĩnh vực khác nhau. 
[Chèn ảnh các Programming Technology advantage]

##3. Cấu trúc của Xilinx FPGA (SRAM-based, Aymmetrical Array)
[Chen anh]
### Cấu trúc của một Look Up Table (LUT)
[Chen anh]