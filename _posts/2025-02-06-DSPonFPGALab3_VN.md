---
title:  EE3041 Xử lý tín hiệu số với FPGA - Bài tập lớn Lab 3 - Bộ cân bằng âm thanh (Audio Equalizer)
date: 2025-02-06 09:20:00 +0700
categories: [Lab Courses, EE3041 DSP on FPGA Big Project]
tags: [lab, dsponfpga, fpga, courses]      # TAG names should always be lowercase
author: link
toc: true
comments: false
description: Lab 3 cho khóa học EE3041 DSP trên FPGA tại HCMUT
media_subpath: /assets/img/DSPonFPGA/Lab3/

---

# Lab 3 - Bộ cân bằng âm thanh (Audio Equalizer)

## Mục tiêu

Mục tiêu của bài lab này là thiết kế, triển khai và kiểm tra một bộ cân bằng âm thanh sử dụng công nghệ FPGA. Bạn sẽ có kinh nghiệm thực tế trong thiết kế bộ lọc số và xử lý tín hiệu số (DSP) dựa trên FPGA. Đến cuối bài lab này, bạn sẽ có một bộ cân bằng chức năng có khả năng điều chỉnh các dải tần số cụ thể để tăng cường hoặc làm suy giảm các đặc điểm âm thanh nhất định.

## Lưu ý về hợp tác

Dự án lớn được thực hiện theo nhóm. Tất cả công việc phải do các thành viên trong nhóm của bạn thực hiện.

Gợi ý từ những người khác có thể rất hữu ích, cả đối với người gợi ý và người nhận gợi ý.  
Do đó, thảo luận và gợi ý về bài tập được khuyến khích. Tuy nhiên, dự án phải được mã hóa và viết báo cáo theo nhóm (bạn không được hiển thị, cũng như xem bất kỳ mã nguồn nào từ các nhóm sinh viên khác). Chúng tôi **SẼ** sử dụng các công cụ tự động để phát hiện sao chép.

Sử dụng Email/LMS để đặt câu hỏi hoặc đến thăm chúng tôi (203B3) trong giờ làm việc.  
Chúng tôi muốn thấy bạn thành công, nhưng bạn phải yêu cầu hỗ trợ.

## Tổng quan

Trong bài lab này, bạn sẽ thiết kế một bộ cân bằng âm thanh có khả năng điều chỉnh nhiều dải tần số. Bạn có tùy chọn sử dụng bộ lọc FIR (đáp ứng xung hữu hạn) hoặc IIR (đáp ứng xung vô hạn), tùy thuộc vào sở thích và hiểu biết của bạn về đặc điểm và sự đánh đổi của mỗi bộ lọc. Tối thiểu, bộ cân bằng phải có ba dải tần số cơ bản: bass, mid và treble. Tuy nhiên, nếu bạn muốn đạt điểm cao hơn, hãy xem xét chia phổ tần số thành các dải mịn hơn—chẳng hạn như bộ cân bằng 10 dải bao phủ từ 20Hz đến 20kHz. Thêm nhiều dải hơn sẽ cho phép kiểm soát chính xác hơn tín hiệu âm thanh và thể hiện khả năng thiết kế bộ lọc nâng cao.

Nếu bạn muốn đạt điểm cao hơn, bộ cân bằng của bạn phải cho phép thay đổi gain tần số theo thời gian thực. Gain có thể được điều chỉnh bằng cách sử dụng công tắc và phím của bảng DE10 hoặc bằng cách triển khai một chương trình giao tiếp với FPGA để điều khiển cài đặt bộ lọc một cách động.

Bài lab bao gồm các bước sau:

- Hiểu lý thuyết bộ lọc cho bộ cân bằng âm thanh.
- Thiết kế kiến trúc bộ lọc cho các dải tần số khác nhau.
- Mã hóa bộ lọc trong HDL và xác minh hiệu suất thông qua mô phỏng.
- Kiểm tra bộ lọc với đầu vào âm thanh thực và xuất âm thanh đã cân bằng qua loa.

## Kiến thức nền

### Bộ cân bằng âm thanh
Bộ cân bằng âm thanh là một công cụ cho phép người dùng sửa đổi biên độ của các dải tần số nhất định (band) trong tín hiệu âm thanh. Các dải tần số phổ biến bao gồm bass (tần số thấp), mid (tần số trung bình) và treble (tần số cao). Bằng cách điều chỉnh các dải này, bạn có thể tăng cường hoặc giảm bớt một số yếu tố của âm thanh để có trải nghiệm nghe tùy chỉnh hơn.

Bộ cân bằng có thể được xây dựng bằng cách sử dụng bộ lọc FIR hoặc IIR mà bạn đã triển khai trong các bài lab trước.

![](mxr-ten-band-eq.jpg)
*MXR M108S 10 Band Equalizer*

Đây là sơ đồ khối ví dụ cho bộ cân bằng 3 dải. Trong thiết kế này, tín hiệu âm thanh được chia thành ba dải tần số riêng biệt bằng cách sử dụng bộ lọc FIR: bass, mid và treble. Mỗi dải có gain có thể điều chỉnh, cho phép bạn tăng hoặc giảm biên độ của tín hiệu âm thanh trong dải tần số cụ thể đó.

![](audio-eq-diagram.svg)
*Ví dụ về bộ cân bằng 3 dải*

## Tổng quan bài tập

Nhiệm vụ của bạn là tạo một bộ cân bằng âm thanh với ít nhất ba dải tần số: bass, mid và treble. Đối với bài lab này, bạn được yêu cầu xây dựng testbench của riêng mình và lấy mẫu âm thanh để kiểm tra kỹ lưỡng thiết kế của bạn. Testbench phải mô phỏng hiệu suất của bộ lọc và cho phép bạn xác minh rằng mỗi dải tần số đang được điều chỉnh đúng cách theo cài đặt bộ cân bằng của bạn. Đảm bảo rằng mẫu âm thanh bạn sử dụng có dải tần số để hiệu ứng của bộ cân bằng trên mỗi dải có thể được quan sát rõ ràng.

## Trình diễn trên FPGA

Nếu bạn muốn đạt điểm cao, bạn sẽ cần triển khai thiết kế của bạn trên FPGA bằng cách sử dụng DE10 Standard Development Kit hoặc DE2 Standard Development Kit. Các yêu cầu cho bài lab này như sau:

- **Thể hiện chức năng cơ bản của bộ cân bằng của bạn**, cho thấy rằng mỗi dải (bass, mid và treble) có thể được điều chỉnh riêng lẻ.
- **Cung cấp phương pháp điều chỉnh các dải** theo thời gian thực. Điều này có thể được thực hiện bằng cách sử dụng công tắc và phím của bảng hoặc, để có giải pháp nâng cao hơn, thông qua một chương trình trên máy tính của bạn giao tiếp với FPGA, cho phép bạn điều chỉnh các dải tần số một cách động.
- **Thực hiện kiểm tra âm thanh trực tiếp** bằng cách phát mẫu âm thanh từ thiết bị bên ngoài (ví dụ: laptop hoặc điện thoại) được kết nối với jack microphone của bảng. Truyền âm thanh này qua bộ cân bằng của bạn, xuất âm thanh đã sửa đổi đến loa để hiệu ứng của mỗi điều chỉnh dải trên âm thanh có thể được nghe rõ ràng. Trình diễn này nên làm nổi bật hiệu suất thời gian thực và chất lượng âm thanh của thiết kế bộ cân bằng của bạn. Bạn có thể sử dụng cáp âm thanh 3.5mm đến 3.5mm để kết nối đầu ra âm thanh của laptop với đầu vào âm thanh của bảng. Đầu ra âm thanh của bảng có thể được kết nối với loa.

![auxcable](cable.png)

## Để được điểm

- **0.5 điểm**: Thể hiện sự hiểu biết về bộ cân bằng, bao gồm các yêu cầu bộ lọc cơ bản cho các dải tần số khác nhau. 
- **0.5 điểm**: Cung cấp giải thích có lý do cho việc chọn FIR, IIR hoặc kết hợp cả hai và định nghĩa thông số kỹ thuật đầy đủ cho bộ cân bằng.
- **2 điểm**: Triển khai một bộ cân bằng âm thanh hoạt động với ba dải tần số (bass, mid và treble). Những điểm này chỉ được trao nếu bạn thành công mô phỏng và xác minh thiết kế.
- **2 điểm**: Hoàn thành và xác minh thiết kế bộ lọc trong mô phỏng, đảm bảo nó đáp ứng các thông số kỹ thuật yêu cầu cho mỗi dải tần số.
- **2 điểm**: Tổng hợp thiết kế, bạn nên kiểm tra báo cáo để đảm bảo thiết kế có thể tổng hợp.
- **3 điểm**: Thể hiện kiểm soát thời gian thực thành công của gain cho mỗi dải tần số bằng cách sử dụng công tắc hoặc phím FPGA bằng mẫu âm thanh từ thiết bị bên ngoài.
- **Điểm thưởng**:
  - Thêm các dải tần số bổ sung ngoài ba dải yêu cầu.
  - Triển khai một chương trình cho phép điều chỉnh gain tần số theo thời gian thực thông qua giao tiếp với FPGA.
  - Cung cấp thiết kế có thể tham số hóa đầy đủ, cho phép giới hạn dải tần số được điều chỉnh trong mã.
  - Bạn có thể đề xuất ý tưởng riêng của mình
  - Nếu kết quả của bạn không tối ưu, bạn nên phân tích và giải thích tại sao kết quả không đủ, và đề xuất giải pháp để cải thiện nó. Ngay cả khi hiệu suất không lý tưởng, một phân tích được giải thích tốt vẫn có thể kiếm được điểm thưởng.

## Yêu cầu báo cáo

Báo cáo của bạn nên giải thích các lựa chọn thiết kế, mô phỏng và kết quả của bạn. Đảm bảo nó bao gồm bằng chứng rõ ràng về hiệu suất của bộ lọc của bạn, chẳng hạn như sóng và đồ thị, và phân tích chi tiết về bất kỳ vấn đề nào gặp phải. Để biết thêm chi tiết, tham khảo [Lab 2 - Bộ lọc FIR và IIR, Yêu cầu báo cáo]({% post_url 2025-02-06-DSPonFPGALab2 %}#reporting-requirements)

## Cách nộp giải pháp

Học kỳ này chúng ta sẽ sử dụng LMS, chỉ cần nộp file zip với báo cáo và mã của bạn.

## Demo và phạt trễ

Chúng ta sẽ có thời gian demo / thuyết trình vào thời gian lớp vào hoặc gần ngày đến hạn. Vì chúng ta sẽ demo từ các file trong zip của bạn, có thể bạn sẽ demo vào ngày hôm sau.

**Định nghĩa Trễ:** Việc trễ được xác định bởi ngày tháng file của bài nộp của bạn trên LMS.
