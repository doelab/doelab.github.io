---
title:  EE3041 Xử lý tín hiệu số với FPGA - Bài tập lớn Lab 1 - Bộ tạo sóng (Waveform Generator)
date: 2025-02-06 09:00:00 +0700
categories: [Lab Courses, EE3041 DSP on FPGA Big Project]
tags: [lab, dsponfpga, fpga, courses]      # TAG names should always be lowercase
author: link
toc: true
comments: false
description: Lab 1 cho khóa học EE3041 DSP trên FPGA tại HCMUT
media_subpath: /assets/img/DSPonFPGA/Lab1/

---

# Lab 1 - Bộ tạo sóng (Waveform Generator)

## Mục tiêu
Mục tiêu chính của bài lab này là thiết kế và triển khai một bộ tạo sóng đa năng sử dụng FPGA. Bộ tạo sóng phải có khả năng tạo ra các loại sóng khác nhau với các tham số có thể điều chỉnh như tần số, biên độ và chu kỳ nhiệm vụ. Ngoài ra, hệ thống phải có khả năng tiêm nhiễu vào các sóng này. Việc triển khai cuối cùng sẽ được trình diễn trên FPGA sử dụng DE10 Standard Development Kit hoặc DE2 Standard Development Kit (sinh viên được tự do lựa chọn), với đầu ra được hiển thị trên oscilloscope.

## Lưu ý về hợp tác

Dự án lớn được thực hiện theo nhóm. Tất cả công việc phải do các thành viên trong nhóm của bạn thực hiện.

Gợi ý từ những người khác có thể rất hữu ích, cả đối với người gợi ý và người nhận gợi ý.  
Do đó, thảo luận và gợi ý về bài tập được khuyến khích. Tuy nhiên, dự án phải được mã hóa và viết báo cáo theo nhóm (bạn không được hiển thị, cũng như xem bất kỳ mã nguồn nào từ các nhóm sinh viên khác). Chúng tôi **SẼ** sử dụng các công cụ tự động để phát hiện sao chép.

Sử dụng Email/LMS để đặt câu hỏi hoặc đến thăm chúng tôi (203B3) trong giờ làm việc.  
Chúng tôi muốn thấy bạn thành công, nhưng bạn phải yêu cầu hỗ trợ.

## Tổng quan

Trong bài lab này, bạn sẽ thiết kế một bộ tạo sóng có khả năng tạo ra nhiều loại sóng như sine, vuông, tam giác, răng cưa, ECG và nhiễu. Bộ tạo sóng phải cho phép điều chỉnh các tham số chính bao gồm tần số, biên độ và chu kỳ nhiệm vụ (nếu có). Ngoài ra, hệ thống phải có khả năng tiêm nhiễu vào các sóng được tạo.

## Kiến thức nền

### Các loại sóng
Sóng là biểu diễn đồ họa của sự biến thiên của tín hiệu theo thời gian. Các loại sóng phổ biến bao gồm:
- **Sóng Sine**: Một dao động tuần hoàn mượt mà.
- **Sóng vuông**: Một sóng tuần hoàn thay đổi giữa hai mức.
- **Sóng tam giác**: Một sóng tuần hoàn tăng và giảm tuyến tính.
- **Sóng răng cưa**: Một sóng tuần hoàn tăng tuyến tính rồi giảm đột ngột.
- **Sóng ECG**: Một sóng mô phỏng hoạt động điện của tim.

![](waveforms.svg)
![](ECG.svg)

### Bộ tạo sóng
Bộ tạo sóng là một thiết bị điện tử có thể tạo ra các loại sóng khác nhau này. Trong bài lab này, bạn sẽ thiết kế một bộ tạo sóng số sử dụng FPGA, cho phép kiểm soát chính xác các tham số sóng.

![](wavegen.png)

Bộ tạo sóng phải bao gồm khả năng tiêm nhiễu vào các sóng gốc. Tính năng này rất cần thiết cho Lab 2, nơi bạn sẽ được yêu cầu lọc bỏ nhiễu được tiêm bằng bộ lọc đã thiết kế. Đảm bảo rằng việc tiêm nhiễu có thể kiểm soát và có thể điều chỉnh về tần số, biên độ, bạn có thể sử dụng bộ tạo ngẫu nhiên giả (ví dụ: LSFR) để tạo ra các giá trị.

![](noise_inject.png)

### DE10 Standard Development Kit

Trong Lab 1 này, chúng ta sẽ sử dụng [DE10 Standard Development Kit](https://ftp.intel.com/Public/Pub/fpgaup/pub/Intel_Material/Boards/DE10-Standard/DE10_Standard_User_Manual.pdf), một nền tảng phát triển FPGA mạnh mẽ dựa trên Cyclone V SX SoC—5CSXFC6D6F31C6N. Bộ kit DE10 cung cấp nhiều tài nguyên, bao gồm:

  - 110K LEs, 41,509 ALMs  
  - 5,761 Kbits bộ nhớ nhúng  
  - 6 FPGA PLLs và 3 HPS PLLs  
  - 2 Bộ điều khiển bộ nhớ cứng  
  - 112 khối DSP
  - 64MB (32Mx16) SDRAM trên FPGA
  - [Wolfson WM8731 24-bit Audio CODEC](https://cdn.sparkfun.com/datasheets/Dev/Arduino/Shields/WolfsonWM8731.pdf)

![](de10-audio.jpg)

Điều này làm cho nó lý tưởng cho các tác vụ xử lý tín hiệu số (DSP). Trong bài lab này, chúng ta sẽ sử dụng Audio CODEC như một DAC để xuất dữ liệu 24-bit đã xử lý qua cổng âm thanh. Ngoài ra, trong Lab 203B3, chúng tôi cung cấp cáp jack 3.5mm đến BNC, mà bạn có thể sử dụng để kết nối đầu ra với oscilloscope để hiển thị tín hiệu.

![](3.5-bnc.jpg)

Bạn cũng có thể mua cáp riêng của mình: https://s.shopee.vn/7fQoF97hYY

## Tổng quan bài tập

1. **Thiết kế Bộ tạo sóng**:
   - Chọn ngôn ngữ mô tả phần cứng (HDL) phù hợp cho thiết kế của bạn.
   - Tạo các module để tạo ra các loại sóng khác nhau.
   - Triển khai các điều khiển để điều chỉnh tần số, biên độ và chu kỳ nhiệm vụ.
   - Thêm chức năng để tiêm nhiễu vào các sóng.

2. **Mô phỏng Thiết kế**:
   - Sử dụng các công cụ mô phỏng để xác minh tính đúng đắn của thiết kế của bạn.
   - Đảm bảo rằng tất cả các sóng và tham số có thể được tạo và điều chỉnh chính xác.

3. **Triển khai trên FPGA**:
   - Sử dụng DE10 Standard Development Kit hoặc DE2 Standard Development Kit để triển khai thiết kế của bạn.
   - Kết nối FPGA với oscilloscope bằng cách sử dụng CODEC tích hợp và cáp 3.5-to-BNC.
   - Xác minh đầu ra trên oscilloscope để đảm bảo nó khớp với kết quả mong đợi.

![](schem.jpg)

## Trình diễn trên FPGA

Để thể hiện bộ tạo sóng của bạn trên DE10 Standard Development Kit hoặc DE2 Standard Development Kit, việc triển khai của bạn phải đáp ứng các yêu cầu sau:

- **Tạo sóng**: Hệ thống phải tạo nhiều loại sóng, bao gồm sine, vuông, tam giác, răng cưa và ECG. Người dùng phải có thể điều chỉnh tần số, biên độ và chu kỳ nhiệm vụ (nếu có) bằng cách sử dụng công tắc và nút bấm. Phương pháp triển khai linh hoạt.
- **Tiêm nhiễu**: Hệ thống phải hỗ trợ tiêm nhiễu vào các sóng được tạo.
- **Hiển thị sóng**: Sóng đầu ra phải được hiển thị trên oscilloscope bằng cách sử dụng CODEC tích hợp của FPGA và cáp 3.5mm-to-BNC.

## Để được điểm
- **1 điểm**: Thể hiện sự hiểu biết cơ bản về tạo sóng và thiết kế các thông số kỹ thuật cho bộ tạo sóng. Điều này bao gồm định nghĩa các loại sóng (sine, vuông, tam giác, răng cưa, ECG, nhiễu) và các tham số có thể điều chỉnh (tần số, biên độ, chu kỳ nhiệm vụ).
- **1 điểm**: Hoàn thành thiết kế RTL cho bộ tạo sóng, đảm bảo nó phù hợp với thông số kỹ thuật đã cho. Tối thiểu, thiết kế phải bao gồm tạo sóng sine và vuông, và chức năng tiêm nhiễu. Những điểm này chỉ được trao nếu bạn thành công mô phỏng và xác minh chức năng của bộ tạo sóng bằng cách tạo ra ít nhất một loại sóng.
- **1.5 điểm**: Các sóng bổ sung được trao như sau:
  - Sóng đơn giản (ví dụ: tam giác, răng cưa): **0.25 điểm mỗi sóng**.
  - Sóng phức tạp (ví dụ: ECG): **0.75 điểm mỗi sóng**.
- **1.5 điểm**: Thành công mô phỏng thiết kế bộ tạo sóng với các tham số đầu vào được cung cấp. Phân tích của bạn nên giải thích bộ tạo sóng hoạt động tốt như thế nào, bao gồm độ chính xác của các sóng và khả năng phản hồi của việc điều chỉnh tham số.
- **2 điểm**: Tổng hợp thiết kế và kiểm tra báo cáo tổng hợp để đảm bảo thiết kế có thể tổng hợp và đáp ứng các ràng buộc thời gian.
- **3 điểm**: Thực hiện demo trực tiếp trên FPGA, thể hiện tạo sóng thời gian thực và điều chỉnh tham số. Đầu ra phải được hiển thị trên oscilloscope qua CODEC tích hợp và cáp 3.5-to-BNC.
- Điểm thưởng:
	- Đề xuất và triển khai các cải tiến sáng tạo của riêng bạn cho bộ tạo sóng, chẳng hạn như chức năng bổ sung hoặc tối ưu hóa.
	- Nếu kết quả bộ tạo sóng của bạn không tối ưu, hãy cung cấp phân tích chi tiết giải thích tại sao kết quả không đủ và đề xuất giải pháp để cải thiện nó. Ngay cả khi hiệu suất không lý tưởng, một phân tích được giải thích tốt vẫn có thể kiếm được điểm thưởng.

## Yêu cầu báo cáo

Báo cáo của bạn nên ngắn gọn nhưng đủ chi tiết để giải thích rõ ràng quá trình thiết kế, mô phỏng và kết quả của bạn. Đảm bảo:
- Hiển thị bằng chứng rõ ràng về cách bộ tạo sóng của bạn hoạt động với các tín hiệu đầu vào được cung cấp, bao gồm đồ thị hoặc sóng và phân tích.
- Bao gồm báo cáo tổng hợp và phân tích kết quả, chẳng hạn như sử dụng tài nguyên, ràng buộc thời gian và tần số hoạt động.
- Cung cấp hình ảnh của đầu ra oscilloscope nếu bạn thành công triển khai thiết kế trên FPGA.
- Nếu bộ lọc không hoạt động tốt, hãy cung cấp phân tích chi tiết về tại sao kết quả không như mong đợi và đề xuất cải thiện hoặc giải pháp.
  
Báo cáo của bạn nên tập trung vào chất lượng thay vì độ dài. Mặc dù nó không nên quá dài, nhưng cần phải toàn diện và có cấu trúc tốt. Điểm sẽ bị trừ cho báo cáo kém, chẳng hạn như thiếu chi tiết quan trọng, phân tích không đủ hoặc trình bày không rõ ràng. Đảm bảo trình bày công việc của bạn ở định dạng chuyên nghiệp và dễ đọc.

## Cách nộp giải pháp

Học kỳ này chúng ta sẽ sử dụng LMS, chỉ cần nộp file zip với báo cáo và mã của bạn.

## Demo và phạt trễ

Chúng ta sẽ có thời gian demo / thuyết trình vào thời gian lớp vào hoặc gần ngày đến hạn. Vì chúng ta sẽ demo từ các file trong zip của bạn, có thể bạn sẽ demo vào ngày hôm sau.

**Định nghĩa Trễ:** Việc trễ được xác định bởi ngày tháng file của bài nộp của bạn trên LMS.
