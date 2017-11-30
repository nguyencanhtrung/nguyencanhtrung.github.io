---
layout: post
title: FPGA traditional design flow
author: Trung C. Nguyen
active: learning
permalink: /learning/fpga/
---
Design flow - Các bước thiết kế một IP core là kiến thức vô cùng quan trọng và CẦN phải được nắm vững bởi bất kỳ một FPGA developer nào. Bài viết này sẽ lần lượt phân tích các design flow đang được sử dụng, cũng như giải thích về sự ra đời của chúng.

##1.1. Traditional design flow

Chúng ta sẽ bắt đầu từ "Traditional design flow" - chu trình thiết kế thông dụng nhất và được áp dụng phổ biến.

Ảnh 1: Traditional design flow

###*Specification*

Khi khách hàng A đặt công ty B thiết kế một IP core C, họ phải liệt kê những đặc tính của IP core đó và trao thông tin này cho công ty thiết kế. Những thông tin như vậy được gọi là "informal specification".
Hãy lấy một ví dụ: công ty A muốn đặt một IP core giúp tính toán diện tích hình tròn. IP core đó có những đặc tính sau:

IP core hỗ trỡ tính toán cho số single precision floating point
Tuân theo chuẩn làm tròn của IEEE
Sai số của phép tính không quá 0.1%
Throughput < 500 ps
Latency < 10 ns
Những gạch đầu dòng trên chính là informal specification dùng để thiết kế IP core.

###*Modeling*

Từ "Informal specification", một mô hình (model) sẽ được xây dựng nhanh nhất có thể (bằng high-level language e.g. MATLAB, python ...) để đánh giá chức năng của IP core (chỉ kiểm tra functional behaviour, không quan tâm đến dạng biểu diễn của dữ liệu, không quan tâm tới timing). Bước này được gọi là "Functional Modeling", sản phẩm của quá trình mô phỏng là "floating-point model".

Sau khi chức năng của IP core đã được kiểm tra, một model khác nên được xây dựng nhằm mục đích "kiểm tra dạng biểu diễn của dữ liệu". Nói cách khác, model này được dùng để mô phỏng dữ liệu vào-ra của IP cores (chỉ quan tâm đến dữ liệu INPUT và OUTPUT). Mục đích là tạo ra "stimulus và golden result" cho quá trình verification sau này. Sản phẩm của quá trình mô phỏng này là "Fixed-point/Bit true model". Nó cũng được viết bởi high-level language (e.g. C/C++)

###*RTL design*

Khi chức năng của IP core và dạng biểu diễn dữ liệu đã được xác nhận, thì bước tiếp theo là "Dịch high-level (e.g. bit true, floating point) model ra RTL-level model". Quá trình dịch hoàn toàn manual ở design flow này, ngôn ngữ để mô tả model này là HDL (e.g. VHDL hoặc Verilog). Bước này có nhiều tên gọi một trong số đó là "RTL design". Để xác nhận chức năng của model này có khớp với high-level model hay không, developer phải thực hiện "behaviour simulation" ở bước này (cần cung cấp VHDL-based model + testbench + stimulus). Chú ý: timing ở giai đoạn simulation này không được guarantee. Việc IP core có hoạt động thực sự hay ko cũng không được guarantee.

###*Synthesize*

Bước tiếp theo, VHDL code sẽ được compile và synthesize. Kết quả cuối cùng sẽ là gate netlist, quá trình này được làm hoàn toàn tự động bằng compiler và synthesis tools. Quá trình synthesis này có tên là RTL synthesis (or logic synthesis). Chúng ta sẽ tìm hiểu sâu về quá trình logic synthesis này ở một bài viết khác.
Sau bước này, developer có thể thực hiện "post synthesis simulation" hay còn gọi là logic synthesis, lúc này stimulus sẽ được test trên gate netlist. Nếu bước này passed thì function của IP core đã được đảm bảo.

###*Implementation stage*

####Translate: 
Sau khi gate netlist (*.NGC  - Native Generic Circuit) được tạo ra, tool sẽ tiếp tục lấy thông tin từ *.NGC file + UCF + NCF  để tạo ra một file gọi là NGD (Native Generic Database).

####Map: 
Thông tin về IP core hiện tại được lưu trữ trong database, tiếp theo nó sẽ được map xuống FPGA architecture (LUT architecture) để program GATE ARRAYs. Sản phẩm cuối cùng của bước này là NCD (Native Circuit Description)

####Place & Route: 
Bước "PLACE" và "ROUTE" sẽ sắp xếp các LUT vào các CLB ở vị trí phù hợp sao cho thoả mãn "Timing constraints".

####Note:
Post translate simulation: do static timing analysis with estimated gate delay
Post Place & Route simulation: do static timing analysis with exact delay
Static timing analysis chỉ quan tâm đến timing ko quan tâm đến functional

###*Generate BITSTREAM file (\*.BIT)* 
Routed NCD sẽ được translate sang mã nhị phân bitstream file, để program xuống FPGA board

###*In-circuit simulation*
Testing trực tiếp trên hardware đã được program. Sử dụng ILA core để thu nhận physical signal trên board, và chip scope để hiển thị waveform lên host computer.



 