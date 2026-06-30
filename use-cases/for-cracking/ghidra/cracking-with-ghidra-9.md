---
description: Bài viết sưu tập trên mạng không rõ tác giả
---

# Cracking with Ghidra 9

<figure><img src="../../../.gitbook/assets/unknown.webp" alt="" height="475" width="700"><figcaption></figcaption></figure>

Đã lâu lắm rồi tôi mới bẻ khóa một số phần mềm. Tôi đã sử dụng W32Dasm, SoftIce và Hex32 vào năm 1998; Họ đại diện cho 3 trụ cột của bất kỳ kỹ sư đảo ngược đáng kính và cracker thiếu tôn trọng. Vào thời điểm đó, tôi quyết định ngủ đông cái bẻ khóa nhỏ xấu xa trong tôi để tập trung vào các hoạt động nhàm chán hơn, như phát triển phần mềm.

<figure><img src="../../../.gitbook/assets/unknown.gif" alt="" height="359" width="440"><figcaption></figcaption></figure>

## **20 năm sau** <a href="#id-7beb" id="id-7beb"></a>

NSA vừa phát hành công cụ an ninh mạng siêu bí mật nội bộ của họ được sử dụng trong 10 năm qua để đảo ngược kỹ thuật, tìm lỗi và cuối cùng khai thác chúng để hack vào các hệ thống xấu xa của nước ngoài. Tất cả sự cường điệu này đã đánh thức chiếc bánh quy nhỏ mà tôi đã giữ đông lạnh trong 20 năm :)

Trong 20 năm qua, nhiều thứ đã thay đổi, các công cụ mới xuất hiện (Radare 2, OllyDbg, Hopper, Binary Ninja) và các khung công tác mới (toàn bộ khung LLVM đã làm cho việc xây dựng trình dịch ngược trở nên dễ dàng!). Tôi cảm thấy hơi xúc động khi thấy IdaPro vẫn là một trong những công cụ tốt nhất hiện có **:'(**

Vì vậy, tôi đã tải xuống [Ghidra](https://ghidra-sre.org/), cập nhật JDK trên máy của mình, google cho một ứng dụng [crackme](http://reverse.put.as/wp-content/uploads/2010/05/1-Sandwich.zip) để nhanh chóng dùng thử nó trên macOS của tôi và sau 5 phút tôi đã trở lại trò chơi.

<figure><img src="../../../.gitbook/assets/unknown (1).webp" alt="" height="359" width="700"><figcaption></figcaption></figure>

Crack-me được gọi là Sandwich đang yêu cầu một số sê-ri, bạn nhận được một lỗi cho đến khi bạn chèn đúng. Hãy bẻ khóa nó!

Tạo một dự án mới trong Ghidra (tất cả các tùy chọn mặc định, chỉ cần chọn tên dự án) và nhập ứng dụng (chọn tab TreeView, sau đó kéo và thả sandwich.app):

<figure><img src="../../../.gitbook/assets/unknown (2).webp" alt="" height="515" width="700"><figcaption></figcaption></figure>

Chọn kiến trúc bạn muốn kiểm tra (tôi sẽ làm việc trên kiến trúc x86), nhấp đúp vào nó và bạn sẽ nhận được ứng dụng được phân tích và tháo rời.

## Hãy suy nghĩ như một chiếc bánh quy giòn: <a href="#c1bf" id="c1bf"></a>

Làm sao bây giờ? Chúng tôi có những gì đã từng được gọi là danh sách chết ([+ ORC](https://en.wikipedia.org/wiki/Old_Red_Cracker), tôi nhớ bạn như thế nào!); Danh sách chết vì không thể gỡ lỗi, bạn không thể kiểm tra các biến, giá trị trong thanh ghi, v.v. Nó chết đến mức bạn thậm chí có thể in nó và sử dụng nó như một giờ đi ngủ đọc :)

Bây giờ chúng ta cần tìm cách để ứng dụng tin rằng số sê-ri được chèn vào luôn chính xác!\
Có nhiều cách có thể bạn có thể giải quyết vấn đề này. Sandwich là một ứng dụng crack me rất đơn giản, tôi hy vọng rằng bất kỳ kỹ thuật nào chúng tôi sử dụng, nó sẽ không cần quá 1 phút để bẻ khóa nó. Hãy đi theo cách rõ ràng nhất: hãy tìm chuỗi thông báo lỗi và sau đó chúng ta di chuyển các từ ngược vào dòng mã gây ra lỗi.

Trong bảng điều khiển Cây biểu tượng, hãy tìm các nhãn nhắc nhở bạn thông báo lỗi .." cf\_Error!" nghe có vẻ như là một ứng cử viên sáng giá:

<figure><img src="../../../.gitbook/assets/unknown (3).webp" alt="" height="454" width="700"><figcaption></figcaption></figure>

Như bạn có thể thấy, _cf\_Theserialisnotvalid_ và _cf\_Tryagain_ cũng sẽ là những ứng cử viên tốt. Bây giờ nhấp chuột phải vào nhãn, chọn "Hiển thị tham chiếu đến" và đi đến dòng mã tham chiếu chuỗi này:

<figure><img src="../../../.gitbook/assets/unknown (4).webp" alt="" height="410" width="1000"><figcaption></figcaption></figure>

Ở bên trái, bạn có mã x86 đã dịch ngược, bên phải biểu diễn ngôn ngữ **C** của nó. Điều này thật tuyệt nếu bạn không quá quen thuộc với các hướng dẫn **ARM**, mã bytecode **Dex** hoặc bất kỳ kiến trúc nào khác có thể muốn đảo ngược kỹ sư. Nhìn vào mã C:

if (cVar1 == 0) {\
//bad guy\
}\
else {\
//good guy\
}

Chúng ta cần thay đổi dòng chảy ở đây. Bây giờ hãy nhấp vào lệnh **IF**, mã asm tương ứng được chọn:

<figure><img src="../../../.gitbook/assets/unknown (5).webp" alt="" height="440" width="700"><figcaption></figcaption></figure>

Dòng đầu tiên (TEST AL, AL) là điều kiện và dòng thứ hai là bước nhảy sẽ được thực hiện chỉ trong trường hợp điều kiện là sai (nối tiếp là chính xác). Thay đổi nó thành một bước nhảy đơn giản (chỉ cần chỉnh sửa nó trong Ghidra chọn _Hướng dẫn vá_) thay thế nó bằng lệnh **JMP** hoặc **JZ**. Tôi sẽ thay thế nó bằng một hướng dẫn **JZ**. Như bạn có thể thấy mã **C** cũng đã được cập nhật, bây giờ nếu điều kiện là đúng (sai nối tiếp), bạn là một người tốt :)

<figure><img src="../../../.gitbook/assets/unknown (6).webp" alt="" height="428" width="1000"><figcaption></figcaption></figure>

Chỉ cần lưu dự án, xuất tệp nhị phân đã vá, chmod + x nó, đặt nó bên trong Sandwich.app thay thế tệp nhị phân gốc, khởi chạy nó và tận hưởng màn hình thành công bất cứ nối tiếp nào bạn thử! (Nếu vô tình bạn thử một cái đúng, bạn sẽ gặp lỗi :) )

<figure><img src="../../../.gitbook/assets/unknown (7).webp" alt="" height="359" width="700"><figcaption></figcaption></figure>

## Kết thúc: <a href="#e2d9" id="e2d9"></a>

Đảo ngược và vá một ứng dụng chưa bao giờ dễ dàng như vậy! Ghidra9 hoàn toàn miễn phí, đa nền tảng và mã nguồn mở. Nó có thể nhắm mục tiêu hầu hết mọi kiến trúc cpu, nó có tất cả các tùy chọn và công cụ bạn có thể mong đợi từ một sản phẩm thương mại nhưng nó không thể được sử dụng để **gỡ lỗi** (đó là cách tiếp cận thay thế cho **Phân tích danh sách chết**). Hiện tại, theo như tôi biết, chỉ có Ida Pro 8 mới có thể dịch ngược và sau đó gỡ lỗi quá, hỗ trợ nhiều kiến trúc khác nhau như vậy. Ida Pro 8 khá đắt nhưng nếu bạn là một người chuyên nghiệp, tôi khá chắc chắn rằng bạn có thể mua được.

Ghidra9 có thể sớm đạt đến cùng cấp độ của IdaPro8 nhờ sự giúp đỡ của cộng đồng nguồn mở. Tôi sẽ thử lại một lần nữa để đảo ngược một số APK Android.
