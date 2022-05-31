# Chapter 13: Writing Less Code
- Biết được khi nào không nên viết mã có thể là kỹ năng quan trọng nhất mà một lập trình viên có thể học. Mỗi dòng mã bạn viết là một dòng phải được kiểm tra và bảo trì. Bằng cách sử dụng lại các thư viện hoặc loại bỏ các tính năng, bạn có thể tiết kiệm thời gian và giữ cho codebase của mình gọn gàng và có ý nghĩa.
- Khi bạn bắt đầu một dự án, điều tự nhiên là bạn sẽ hào hứng và nghĩ đến tất cả các tính năng thú vị mà bạn muốn triển khai. Nhưng các lập trình viên có xu hướng liệt kê ra nhiều tính năng thực sự cần thiết cho dự án của họ. Rất nhiều tính năng chưa hoàn thành hoặc không được sử dụng hoặc chỉ làm phức tạp ứng dụng.
- Các lập trình viên thường đánh giá thấp thời gian hoàn thành tính năng, Chúng ta thường ước lượng lạc quan sẽ hoàn thành nhanh chóng tính năng, nhưng lại quên mất thời gian để bảo trì, tài liệu, và yêu cầu bổ sung

## Đặt câu hỏi và chia nhỏ các yêu cầu của bạn
Không phải tất cả các chương trình điều yêu cầu phải nhanh, chính xác 100%,và có thể xử lý mọi đầu vào có thể. **Nếu bạn thật sự xem xét kỹ lưỡng các yêu cầu đầu vào vào của mình đôi khi bạn sẽ tìm ra được cách giải quyết đơn giản hơn, và ít mã hơn.**
Vd: Thêm bộ nhớ cache
Chúng tôi từng có một ứng dụng thường xuyên đọc các đối tượng từ ổ đĩa, Tốc độ đọc sẽ bị giới hạn bởi những lần đọc cùng một đối tượng. Vì vậy chúng tôi muốn thực hiện bộ nhớ cache cho việc này. Trình tự đọc trong như thế này: 
```
read Object A 
read Object A 
read Object A 
read Object B 
read Object B 
read Object C 
read Object D 
read Object D
```
 - Như chúng ta thấy có rất nhiều lần đọc cùng một đối tượng, nên bộ nhớ cache chắc chắn sẽ cải thiện được tốc độ.
 - Để giải quyết vấn đề này điều đầu tiên chúng tôi nghĩ đến là sử dụng bộ nhớ cache để loại bỏ các đối tượng ít sử dụng nhất gần đây(Vì không có sử dụng thư viện phải tự viết mã cho chương trình nên mất khoảng 100 dòng code).Tuy nhiên, chúng tôi nhận thấy rằng các lần truy cập lặp lại luôn liên tiếp. Vì vậy, thay vì triển khai bộ đệm [LRU](https://en.wikipedia.org/wiki/Cache_replacement_policies#LRU), chúng tôi chỉ triển khai bộ đệm một mục: 

```
DiskObject lastUsed; // class member
DiskObject lookUp(String key) {
 if (lastUsed == null || !lastUsed.key().equals(key)) {
 lastUsed = loadDiskObject(key);
 }
 return lastUsed;
}
```
- Điều này làm giảm 90% số lượng code, và chương trình cũng chiếm bộ nhớ nhỏ.
Nên việc chia nhỏ yêu cầu là điều cần thiết, **Nên việc giải quyết một nữa vấn đề có thể chỉ tốn ¼ thời gian thời gian cần giải quyết một vấn đề**
## Giữ cho codebase nhỏ
<img width="450" alt="codebase chart" src="https://user-images.githubusercontent.com/73456508/169939100-7f0ab78d-462a-4ec9-9f57-5a5db290f95f.png">

- Lần đầu tiên bắt đầu một dự án phần mềm và bạn chỉ có một hoặc hai tệp nguồn, mọi thứ thật tuyệt vời. Việc biên dịch và chạy mã chỉ trong tích tắc, dễ dàng thực hiện các thay đổi và dễ dàng nhớ nơi mỗi hàm hoặc lớp được định nghĩa.
- Sau đó, khi dự án phát triển, thư mục của bạn ngày càng nhiều với nhiều tệp hơn. Chẳng bao lâu bạn cần có nhiều thư mục để sắp xếp tất cả. Khó nhớ hàm nào gọi hàm nào và việc theo dõi lỗi sẽ mất nhiều công sức hơn.
- Cuối cùng, bạn có rất nhiều mã nguồn trải rộng trên nhiều thư mục khác nhau. Dự án rất lớn, và không một người nào hiểu hết được. Việc thêm các tính năng mới trở nên khó khăn và làm việc với mã rất cồng kềnh và khó chịu.
Những gì chúng tôi đã mô tả là quy luật tự nhiên, khi bất kỳ ứng dụng nào phát triển, độ phức tạp để cho chúng gắn kết với nhau thậm chí còn tăng nhanh hơn.
- Cách tốt nhất để đối phó là giữ cho codebase của bạn càng nhỏ và nhẹ càng tốt, ngay cả khi dự án của bạn phát triển. Vì vậy, bạn nên:
Tạo càng nhiều code “tiện ích” chung chung càng tốt để loại bỏ mã trùng lặp. (Xem Chương 10)
  - Xóa mã không sử dụng hoặc các tính năng vô dụng.
  - Giữ cho dự án của bạn được phân chia thành các tiểu dự án.
  - Nói chung, hãy chú ý đến “trọng lượng” của code của bạn. Giữ cho nó nhẹ nhàng và nhanh nhẹn.

## Xoá mã không sử dụng
- Một khi code được viết, các lập trình viên thường miễn cưỡng xóa nó, bởi vì nó đại diện cho rất nhiều công việc thực tế (kiểm tra các tính năng liên quan)
- Xóa nó có nghĩa là thừa nhận rằng thời gian dành cho nó đã bị lãng phí. 
- Xóa các chức năng biệt lập rất dễ dàng, nhưng đôi khi “mã không sử dụng” thực sự có mặt trong suốt dự án của bạn mà bạn không biết. Dưới đây là một số ví dụ:
  - Ban đầu, bạn đã thiết kế hệ thống của mình để xử lý các tên tệp quốc tế, và bây giờ mã này có rất nhiều mã chuyển đổi. Tuy nhiên, mã đó không hoạt động đầy đủ và ứng dụng của bạn không bao giờ được sử dụng với tên tệp quốc tế. Tại sao không loại bỏ chức năng này?
  - Bạn muốn chương trình của mình hoạt động ngay cả khi hệ thống hết bộ nhớ, vì vậy bạn có rất nhiều logic thông minh để cố gắng khôi phục từ các tình huống hết bộ nhớ. Đó là một ý tưởng hay, nhưng trên thực tế, khi hệ thống hết bộ nhớ, chương trình của bạn sẽ trở thành một xác sống không ổn định, tất cả các tính năng cốt lõi đều không thể sử dụng được và chỉ cần một cú nhấp chuột là bạn sẽ chết. Tại sao không kết thúc chương trình với một câu đơn giản "Hệ thống hết bộ nhớ, xin lỗi" và xóa tất cả mã hết bộ nhớ này?
## Sử dụng những thư viện có sẵn
- Đôi khi, các lập trình viên không biết được rằng các thư viện hiện có có thể giải quyết vấn đề của họ. Hoặc đôi khi họ quên rằng thư viện có thể làm gì. Điều quan trọng là phải biết các tính năng của thư viện để bạn có thể sử dụng nó.
- Đây là một gợi ý nhỏ: thỉnh thoảng, hãy dành 15 phút để đọc tên của tất cả các hàm / mô-đun / loại dữ liệu trong thư viện chuẩn của bạn. Chúng bao gồm C ++, Thư viện mẫu tiêu chuẩn (STL), API Java, các mô-đun Python tích hợp sẵn và những thứ khác.
Mục tiêu không phải là ghi nhớ toàn bộ thư viện. Nó chỉ để hiểu về những thứ có sẵn, để lần sau khi làm việc trên mã mới, bạn sẽ nghĩ: “Chờ đã, điều này nghe tương tự như điều gì đó tôi đã thấy trong API….” Chúng tôi tin rằng thực hiện công việc này, bạn sẽ nhanh chóng thấy được hiệu quả

- Vd: List và Set trong Python
  Giả sử bạn có một danh sách bằng Python (như [2,1,2]) và bạn muốn một danh sách các phần tử duy nhất (trong trường hợp này, [2,1]). Bạn có thể thực hiện việc này để một bộ từ điển, trong đó có một danh sách các khóa được đảm bảo là duy nhất:
  ```
  def unique(elements):
   temp = {}
   for element in elements:
   temp[element] = None # The value doesn't matter.
   return temp.keys()
  unique_elements = unique([2,1,2])
  ```
  - Thay vào đó bạn có thể dùng Set
  ```
  unique_elements = set([2,1,2]) # Remove duplicates
  ```
  - Nếu bạn muốn có một List bạn có thể dùng
  ```
  unique_elements = list(set([2,1,2])) # Remove duplicates
  ```
  **Rõ ràng Set là một công cụ phù hợp cho việc này, nhưng nếu bạn không biết bạn sẽ phải tốn công sức để viết hàm unique cho việc này. **

## Tại sao nên sử dụng thư viện

- Một thống kê thường được trích dẫn là một kỹ sư phần mềm trung bình tạo ra mười dòng mã có thể chuyển đổi mỗi ngày. Khi các lập trình viên lần đầu tiên nghe thấy điều này, họ không tin nổi— “Mười dòng mã? Tôi có thể viết điều đó trong một phút! ”

- Mỗi dòng mã trong một thư viện đại diện cho một lượng lớn design, debugging, rewriting, documenting, optimizing, and testing. Đây là lý do tại sao việc sử dụng lại các thư viện là tốt, vừa tiết kiệm thời gian vừa ít phải viết mã hơn.

Ví dụ: Sử dụng Unix Tools thay vì Coding
  - Khi máy chủ web thường xuyên trả về mã phản hồi HTTP 4xx hoặc 5xx, đó là dấu hiệu của sự cố tiềm ẩn (4xx là lỗi máy khách; 5xx là lỗi máy chủ). Vì vậy, chúng tôi muốn viết một chương trình phân tích logs truy cập của máy chủ web và xác định URL nào gây ra nhiều lỗi nhất. Logs lỗi trong như thế này:
  ```
  1.2.3.4 example.com [24/Aug/2010:01:08:34] "GET /index.html HTTP/1.1" 200 ...
  2.3.4.5 example.com [24/Aug/2010:01:14:27] "GET /help?topic=8 HTTP/1.1" 500 ...
  3.4.5.6 example.com [24/Aug/2010:01:15:54] "GET /favicon.ico HTTP/1.1" 404 ...
  ...
  ```
  Những dòng lỗi sẽ có dạng thế này: 
  ```
  browser-IP host [date] "GET /url-path HTTP/1.1" HTTP-response-code … 
  ```
  - Viết một chương trình để tìm các url với mã phản hồi 4xx hoặc 5xx có thể dễ dàng mất 20 dòng mã trong một ngôn ngữ như C ++ hoặc Java.
  - Thay vào đó sử dụng Unix command line:
  ```
   cat access.log | awk '{ print $5 " " $7 }' | egrep "[45]..$" \
   | sort | uniq -c | sort -nr
  ```
  - kết quả
  ```
  95 /favicon.ico 404
  13 /help?topic=8 500
  11 /login 403
  ...
  <count> <path> <http response code>
  ```
## Tổng hợp
- Viết càng ít mã càng tốt, mỗi dòng mã mới cần được tested, documented, and maintained.
- Càng nhiều codebase, thì nó càng "nặng" và càng khó phát triển.
- Bạn có thể tránh khi viết những mã mới bằng cách:
  - Loại bỏ những tính năng không cần thiết, không sử dụng
  - Xem xét lại yêu cầu, chia nhỏ yêu cầu, để tìm cách giải quyết đơn giản nhất
  - Làm quen với các thư viện tiêu chuẩn 
