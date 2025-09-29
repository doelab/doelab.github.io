---
title:  EE3041 Xử lý tín hiệu số với FPGA - Bài tập lớn Lab 2 - Bộ lọc FIR và IIR
date: 2025-02-06 09:10:00 +0700
categories: [Lab Courses, EE3041 DSP on FPGA Big Project]
tags: [lab, dsponfpga, fpga, courses]      # TAG names should always be lowercase
author: link
toc: true
comments: false
description: Lab 2 cho khóa học EE3041 DSP trên FPGA tại HCMUT
media_subpath: /assets/img/DSPonFPGA/Lab2/

---

# Lab 2 - Bộ lọc FIR và IIR

## Mục tiêu

Mục tiêu của bài lab này là thiết kế, triển khai và kiểm tra bộ lọc đáp ứng xung hữu hạn (FIR) và bộ lọc đáp ứng xung vô hạn (IIR) sử dụng công nghệ FPGA. Mục tiêu là hiểu lý thuyết đằng sau bộ lọc FIR và IIR, áp dụng các kỹ thuật thiết kế bộ lọc số, và có kinh nghiệm thực tế trong thiết kế và triển khai phần cứng. Bằng cách hoàn thành bài lab này, sinh viên sẽ phát triển kỹ năng lập trình FPGA và xử lý tín hiệu số (DSP).

## Lưu ý về hợp tác

Bài lab được thực hiện theo nhóm. Tất cả công việc phải do các thành viên trong nhóm của bạn thực hiện.

Gợi ý từ những người khác có thể rất hữu ích, cả đối với người gợi ý và người nhận gợi ý.  
Do đó, thảo luận và gợi ý về bài tập được khuyến khích. Tuy nhiên, dự án phải được mã hóa và viết báo cáo theo nhóm (bạn không được hiển thị, cũng như xem bất kỳ mã nguồn nào từ các nhóm sinh viên khác). Chúng tôi **SẼ** sử dụng các công cụ tự động để phát hiện sao chép.

Sử dụng Email/LMS để đặt câu hỏi hoặc đến thăm chúng tôi (203B3) trong giờ làm việc.  
Chúng tôi muốn thấy bạn thành công, nhưng bạn phải yêu cầu hỗ trợ.

## Tổng quan

Trong bài lab này, sinh viên sẽ thiết kế bộ lọc FIR và IIR sử dụng Verilog hoặc SystemVerilog. Trọng tâm sẽ là hiểu quy trình thiết kế bộ lọc, mã hóa kiến trúc bộ lọc và chạy mô phỏng để xác minh chức năng của bộ lọc FIR và IIR. Sinh viên được yêu cầu mô phỏng thiết kế để xác nhận tính đúng đắn của nó.

Đối với những ai muốn có thêm điểm, có tùy chọn tổng hợp thiết kế lên FPGA và thực hiện kiểm tra thời gian thực. Điều này sẽ cho phép sinh viên thể hiện việc triển khai bộ lọc của họ trên phần cứng, củng cố thêm hiểu biết của họ về xử lý tín hiệu số (DSP) dựa trên FPGA.

Bài lab sẽ bao gồm các bước sau:

- Hiểu lý thuyết đằng sau thiết kế bộ lọc FIR và IIR.
- Hiểu sự khác biệt (ưu nhược điểm) giữa bộ lọc FIR và IIR.
- Triển khai kiến trúc bộ lọc FIR và IIR sử dụng Verilog và SystemVerilog.
- Chạy mô phỏng để xác minh chức năng của bộ lọc.
- Tổng hợp thiết kế trên FPGA, đánh giá kết quả tổng hợp.
- Thực hiện kiểm tra thời gian thực, trình diễn.

## Kiến thức nền

### Bộ lọc FIR

Bộ lọc đáp ứng xung hữu hạn (FIR) là một thành phần thiết yếu trong các hệ thống xử lý tín hiệu số (DSP), được sử dụng rộng rãi cho các tác vụ như giảm nhiễu, làm mượt tín hiệu và lựa chọn tần số. Bộ lọc FIR được biết đến với tính ổn định và tính chất pha tuyến tính, khiến chúng phù hợp cho nhiều ứng dụng yêu cầu thao tác tín hiệu chính xác.

Trong các hệ thống số, bộ lọc FIR thường được triển khai bằng cách sử dụng tích chập thời gian rời rạc của tín hiệu đầu vào với một tập hợp các hệ số bộ lọc. Những hệ số này xác định đáp ứng tần số của bộ lọc, và chúng có thể được thiết kế để cho qua, loại bỏ hoặc làm suy giảm các dải tần số cụ thể. Số lượng hệ số, hoặc "tap", ảnh hưởng trực tiếp đến hiệu suất của bộ lọc, với nhiều tap hơn thường dẫn đến lọc tín hiệu chính xác hơn nhưng cũng tăng độ phức tạp tính toán.

Dưới đây là bộ lọc FIR dạng trực tiếp bậc 8. Nói chung, tăng bậc bộ lọc (tap) cải thiện hiệu suất của bộ lọc bằng cách cung cấp lọc tín hiệu tốt hơn và đáp ứng tần số chính xác hơn. Tuy nhiên, khi triển khai thiết kế như vậy trong phần cứng, vấn đề về thời gian trở nên quan trọng. Vì mỗi tap được triển khai như một phần tử tổ hợp, chuỗi càng dài, càng khó đáp ứng các ràng buộc thời gian, dẫn đến tần số hoạt động tối đa thấp hơn.

![](fir_diagram.png)

Để giải quyết điều này, các kỹ thuật pipeline có thể được áp dụng. Bằng cách giới thiệu các giai đoạn pipeline giữa các tap, độ trễ tổ hợp được giảm, cho phép tần số xung nhịp cao hơn và hiệu suất tổng thể tốt hơn trong việc triển khai phần cứng.

![](fir_pipelined_diagram.png)

Sơ đồ trên chỉ là một ví dụ về bộ lọc FIR pipeline. Bạn không bắt buộc phải tuân theo thiết kế này chính xác và được khuyến khích đề xuất cách tiếp cận thiết kế của riêng bạn. Hãy tự do khám phá các kiến trúc hoặc tối ưu hóa khác nhau phù hợp với triển khai cụ thể và mục tiêu hiệu suất của bạn.

### Bộ lọc IIR

Bộ lọc đáp ứng xung vô hạn (IIR) là một bộ lọc số dựa vào cả các giá trị đầu vào và đầu ra trong quá khứ để tạo ra đầu ra hiện tại. Không giống như bộ lọc FIR, chỉ sử dụng các giá trị đầu vào và một số lượng hữu hạn các hệ số, bộ lọc IIR sử dụng cơ chế phản hồi để đạt được hành vi đệ quy. Điều này làm cho bộ lọc IIR rất hiệu quả cho một số tác vụ nhất định, vì chúng có thể đáp ứng các yêu cầu đáp ứng tần số với ít hệ số hơn so với bộ lọc FIR.

Tuy nhiên, thiết kế bộ lọc IIR đi kèm với những thách thức, chẳng hạn như đảm bảo tính ổn định, đặc biệt khi triển khai trong phần cứng. Cần đặc biệt chú ý để tránh mất ổn định số, vì các lỗi hoặc độ trễ nhỏ trong phản hồi có thể khiến bộ lọc dao động hoặc hoạt động không thể đoán trước.

Dưới đây là sơ đồ khối tổng quát của cấu trúc bộ lọc IIR:

![](iir.png)

Bộ lọc IIR được sử dụng cho các tác vụ như làm mượt, giảm nhiễu và lựa chọn dải tần số, thường trong các ứng dụng yêu cầu xử lý tín hiệu thời gian thực. Bản chất đệ quy của chúng cho phép chúng triển khai hiệu quả các bộ lọc thông thấp, thông cao, thông dải hoặc chặn dải.

## Tổng quan bài tập

Trong bài tập lab này, bạn sẽ thiết kế và mô phỏng bộ lọc FIR và IIR sử dụng Verilog hoặc SystemVerilog. Nhiệm vụ của bạn là triển khai bộ lọc FIR và IIR dựa trên thông số kỹ thuật của bạn và xác minh chức năng của nó thông qua mô phỏng. Bạn được yêu cầu xây dựng testbench của riêng mình và lấy mẫu âm thanh để kiểm tra kỹ lưỡng thiết kế của bạn.

Mục tiêu chính của bạn là hoàn thành thiết kế bộ lọc FIR và IIR và xác minh hoạt động của chúng thông qua mô phỏng bằng cách sử dụng testbench và tín hiệu đầu vào. Bạn cũng cần tổng hợp thiết kế và demo nó trên FPGA để kiểm tra thời gian thực.

## Trình diễn FPGA

Để đạt được điểm cao, bạn phải triển khai thiết kế của bạn trên FPGA bằng cách sử dụng DE10 Standard Development Kit. Trình diễn của bạn nên bao gồm hai mẫu: sóng tuần hoàn và sóng âm thanh. Đảm bảo cả hai trình diễn được chuẩn bị tốt và thể hiện rõ ràng chức năng và hiệu suất của thiết kế của bạn.

1. **Trình diễn Sóng tuần hoàn**  
   - Sử dụng module bạn đã thiết kế trong Lab 1 để tạo sóng tuần hoàn với nhiễu được thêm vào. Tín hiệu nhiễu này sẽ phục vụ như đầu vào cho bộ lọc của bạn.  
   - Áp dụng bộ lọc của bạn để loại bỏ nhiễu khỏi tín hiệu.  
   - Hiển thị cả sóng đầu vào (nhiễu) và đầu ra (đã lọc) trên oscilloscope. Sử dụng CODEC âm thanh và cáp 3.5mm đến BNC để kết nối FPGA với oscilloscope.  

   ![](schem_wave.jpg)

1. **Trình diễn Sóng âm thanh**  
   - Sử dụng đầu vào âm thanh từ laptop hoặc microphone của bạn được kết nối với đầu vào line-in/mic của FPGA.  
   - Xử lý tín hiệu âm thanh nhiễu thông qua bộ lọc của bạn để tạo ra đầu ra âm thanh sạch, đã lọc.  
   - Phát âm thanh đã lọc qua loa để thể hiện hiệu quả của bộ lọc của bạn.  
   - Chọn mẫu âm thanh thể hiện rõ ràng sự khác biệt giữa phiên bản nhiễu và đã lọc. 
   - Bạn có thể sử dụng cáp âm thanh 3.5mm đến 3.5mm để kết nối đầu ra âm thanh của laptop với đầu vào âm thanh của bảng DE10. Đầu ra âm thanh của bảng DE10 có thể được kết nối với loa.
  
   ![auxcable](cable.png)

   ![](schem_audio.jpg)


## Để được điểm

- **1 điểm**: Thể hiện sự hiểu biết cơ bản về bộ lọc FIR và thiết kế thông số kỹ thuật cho bộ lọc FIR.
- **1 điểm**: Hoàn thành thiết kế RTL cho bộ lọc FIR, đảm bảo nó phù hợp với thông số kỹ thuật đã cho. Những điểm này chỉ được trao nếu bạn thành công mô phỏng và xác minh chức năng của bộ lọc với ít nhất một mẫu.
- **1 điểm**: Thành công mô phỏng thiết kế bộ lọc FIR với các mẫu đầu vào được cung cấp. Phân tích của bạn nên giải thích bộ lọc hoạt động tốt như thế nào, xác định bất kỳ vấn đề nào trong lọc và đáp ứng tần số.
- **2 điểm**: Triển khai kiến trúc pipeline/song song để đạt được tần số hoạt động cao hơn.
- **2 điểm**: Tổng hợp thiết kế, bạn nên kiểm tra báo cáo để đảm bảo thiết kế có thể tổng hợp.
- **3 điểm**: Thực hiện demo trực tiếp trên FPGA, thể hiện hiệu suất thời gian thực và xử lý tín hiệu.
- Điểm thưởng:
	- Bộ lọc FIR và/hoặc IIR có thể tham số hóa đầy đủ 
	- Thể hiện hiệu suất của bộ lọc bằng cách sử dụng tín hiệu thế giới thực (tín hiệu âm thanh)
	- Bộ lọc FIR và/hoặc IIR thích ứng với hệ số được cập nhật động dựa trên tín hiệu đầu vào
	- Đề xuất ý tưởng riêng của bạn cho các cải tiến
	- Nếu kết quả bộ lọc của bạn không tối ưu, bạn nên phân tích và giải thích tại sao kết quả không đủ, và đề xuất giải pháp để cải thiện nó. Ngay cả khi hiệu suất bộ lọc không lý tưởng, một phân tích được giải thích tốt vẫn có thể kiếm được điểm thưởng.

## Yêu cầu báo cáo

Báo cáo của bạn nên ngắn gọn nhưng đủ chi tiết để giải thích rõ ràng quá trình thiết kế, mô phỏng và kết quả của bạn. Đảm bảo:
- Bao gồm giải thích về các lựa chọn thiết kế bạn đã thực hiện, đặc biệt nếu bạn triển khai kiến trúc pipeline hoặc bất kỳ tối ưu hóa nào.
- Hiển thị bằng chứng rõ ràng về cách bộ lọc FIR và IIR của bạn hoạt động với các tín hiệu đầu vào được cung cấp, bao gồm đồ thị hoặc sóng và phân tích.
- Nếu bộ lọc không hoạt động tốt, hãy cung cấp phân tích chi tiết về tại sao kết quả không như mong đợi và đề xuất cải thiện hoặc giải pháp.
  
Báo cáo của bạn nên tập trung vào chất lượng thay vì độ dài. Mặc dù nó không nên quá dài, nhưng cần phải toàn diện và có cấu trúc tốt. Điểm sẽ bị trừ cho báo cáo kém, chẳng hạn như thiếu chi tiết quan trọng, phân tích không đủ hoặc trình bày không rõ ràng. Đảm bảo trình bày công việc của bạn ở định dạng chuyên nghiệp và dễ đọc.

## Câu hỏi thường gặp

1. **Nếu sử dụng phép nhân, tôi có cần viết ra bộ nhân, hay chỉ cần sử dụng toán tử nhân?**  
   Bạn có thể sử dụng toán tử nhân.

2. **Trong mã của tôi, tôi sử dụng vòng lặp for cho tính toán. Điều đó có chấp nhận được không?**  
   Bạn có thể mã hóa theo bất kỳ cách nào hoạt động để có kết quả mô phỏng. Tuy nhiên, nếu bạn dự định tổng hợp thiết kế lên bộ kit FPGA, mã phải có thể tổng hợp.

3. **FPGA có giới hạn tối đa 33 tap không?**  
   Khi mô phỏng, bạn có thể sử dụng bao nhiêu tap tùy thích. Khi tổng hợp và tải lên bộ kit FPGA, bạn cần lưu ý về tài nguyên trên DE10 và điều chỉnh số lượng tap cho phù hợp.

## Cách nộp giải pháp

Học kỳ này chúng ta sẽ sử dụng LMS, chỉ cần nộp file zip với báo cáo và mã của bạn.

## Demo và phạt trễ

Chúng ta sẽ có thời gian demo / thuyết trình vào thời gian lớp vào hoặc gần ngày đến hạn. Vì chúng ta sẽ demo từ các file trong zip của bạn, có thể bạn sẽ demo vào ngày hôm sau.

**Định nghĩa Trễ:** Việc trễ được xác định bởi ngày tháng file của bài nộp của bạn trên LMS.
