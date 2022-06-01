# Chương 9: Biến và khả năng đọc

![Image from 003_the_art_of_readable_code, page 93](https://user-images.githubusercontent.com/38417570/171199532-430ad60d-5998-4257-8e98-7b4508870001.jpg)

Trong chương này, bạn sẽ thấy cách sử dụng các biến cẩu thả làm cho một chương trình khó hiểu hơn như thế nào.
Cụ thể, có ba vấn đề cần giải quyết:
1. Càng có nhiều biến, càng khó theo dõi.
2. Phạm vi của biến càng lớn, theo dõi càng lâu.
3. Một biến càng thay đổi thường xuyên thì càng khó theo dõi giá trị hiện tại.
Ba phần tiếp theo sẽ thảo luận về cách giải quyết những vấn đề này.

## Loại bỏ các biến
Trong Chương 8, Phá vỡ các biểu thức khổng lồ, chúng tôi đã chỉ ra cách giới thiệu các biến "giải thích" hoặc "tóm tắt" có thể làm cho mã dễ đọc hơn.
Các biến này rất hữu ích vì chúng đã chia nhỏ các biểu thức khổng lồ và hoạt động như một dạng tài liệu.
Trong phần này, chúng tôi quan tâm đến việc loại bỏ các biến không cải thiện khả năng đọc.
Khi một biến như thế bị xóa, mã mới sẽ ngắn gọn và dễ hiểu hơn.
Trong phần sau là một vài ví dụ về cách các biến không cần thiết này hiển thị.
## Biến tạm thời vô dụng
Trong đoạn mã Python sau, hãy xem xét biến `now`:
```python
  now = datetime.datetime.now()
  root_message.last_view_time = now
```
`now` có phải là một biến đáng để giữ lại không? Không, và đây là những lý do:
- Nó không phá vỡ một biểu thức phức tạp.
- Nó không làm rõ thêm — biểu thức ```datetime.datetime.now()``` đã đủ rõ ràng.
- Nó chỉ được sử dụng một lần, vì vậy nó không nén bất kỳ mã thừa nào.
Không có `now`, mã trở nên dễ hiểu như sau:
```python
  root_message.last_view_time = datetime.datetime.now()
```
Các biến như `now` thường là "phần thừa" thứ mà vẫn còn lại sau khi mã đã được chỉnh sửa. Biến `now` có thể đã được sử dụng ở nhiều nơi ngay từ ban đầu. Hoặc cũng có thể lập trình viên dự đoán sẽ sử dụng `now` nhiều lần, nhưng thực sự không bao giờ cần đến nó.
## Loại bỏ kết quả trung gian

![Image from 003_the_art_of_readable_code, page 95](https://user-images.githubusercontent.com/38417570/171200364-72ee67d6-6894-4b4b-a3a6-354f0b73c4c0.jpg)

Dưới đây là một ví dụ về hàm JavaScript xóa một giá trị khỏi mảng:
```javascript
var remove_one = function (array, value_to_remove) {
  var index_to_remove = null;
  for (var i = 0; i < array.length; i += 1) {
    if (array[i] === value_to_remove) {
    index_to_remove = i;
    break;
    }
  }
  if (index_to_remove !== null) {
    array.splice(index_to_remove, 1);
  }
};
```
Biến `index_to_remove` chỉ được sử dụng để giữ kết quả trung gian. Các biến như thế này đôi khi có thể được loại bỏ bằng cách xử lý kết quả ngay khi bạn nhận được:
```javascript
  var remove_one = function (array, value_to_remove) {
    for (var i = 0; i < array.length; i += 1) {
      if (array[i] === value_to_remove) {
        array.splice(i, 1);
        return;
      }
    }
  };
```
Bằng cách cho phép mã trả lại sớm, chúng tôi đã loại bỏ hoàn toàn `index_to_remove` và đơn giản hóa mã một chút.
Nói chung, đó là một chiến lược tốt để hoàn thành nhiệm vụ càng nhanh càng tốt

## Loại bỏ các biến luồng kiểm soát

Đôi khi bạn sẽ thấy dạng mã này trong các vòng lặp:
```python
  boolean done = false;
  while (/* condition */ && !done) {
    …
    if (...) {
      done = true;
      continue;
    }
  }
```
Biến được thực hiện thậm chí có thể được đặt thành `đúng` ở nhiều nơi trong suốt vòng lặp.
Mã như thế này thường cố gắng đáp ứng một số quy tắc bất thành văn là bạn không nên thoát ra giữa vòng lặp. Không có quy tắc như vậy!
Các biến được thực hiện như vậy chúng tôi gọi là "các biến luồng điều khiển". Mục đích duy nhất của chúng là chỉ đạo việc thực thi chương trình — chúng không chứa bất kỳ dữ liệu chương trình thực nào. Theo kinh nghiệm của chúng tôi, các biến luồng điều khiển thường có thể được loại bỏ bằng cách tốt hơn là sử dụng lập trình có cấu trúc:
```python
  while (/* condition */) {
    …
    if (...) {
      break;
    }
  }
```
Trường hợp này khá dễ sửa, nhưng điều gì sẽ xảy ra nếu có nhiều vòng lặp lồng nhau mà một điểm dừng đơn giản sẽ không đủ? Trong những trường hợp phức tạp hơn như vậy, giải pháp thường liên quan đến việc di chuyển mã vào một hàm mới (hoặc mã bên trong vòng lặp hoặc toàn bộ vòng lặp).
* * *
<p align="center">BẠN CÓ MUỐN ĐỒNG NGHIỆP CỦA BẠN CẢM THẤY NHƯ THỂ MỌI LÚC HỌ ĐANG Ở TRONG MỘT CUỘC PHỎNG VẤN?</p>

Eric Brechner của Microsoft đã nói về cách một câu hỏi phỏng vấn tuyệt vời nên liên quan đến ít nhất ba biến số. 
* Có thể là do việc xử lý ba biến số cùng một lúc buộc bạn phải suy nghĩ nhiều! Điều này có ý nghĩa đối với một cuộc phỏng vấn, nơi bạn đang cố gắng đẩy một ứng viên đến giới hạn.
Nhưng bạn có muốn đồng nghiệp của mình cảm thấy như họ đang tham gia một cuộc phỏng vấn khi họ đang đọc mã của bạn không?
* * *
* Eric Brechner’s I. M. Wright’s “Hard Code” (Microsoft Press, 2007), p. 166.

## Thu hẹp phạm vi các biến

Tất cả chúng ta đều đã nghe lời khuyên “tránh các biến toàn cục”. Đây là lời khuyên hữu ích, vì thật khó để theo dõi tất cả các biến toàn cục đó đang được sử dụng ở đâu và như thế nào. Và bằng cách "làm ô nhiễm không gian tên" (đặt một loạt các tên có thể xung đột với các biến cục bộ của bạn), mã có thể vô tình sửa đổi một biến toàn cục khi nó có ý định sử dụng một biến cục bộ hoặc ngược lại.
Trên thực tế, bạn nên "thu hẹp phạm vi" của tất cả các biến không chỉ đối với các biến toàn cục.
> **Ý TƯỞNG CHÍNH**
> 
> **Làm cho biến của bạn hiển thị bằng càng ít dòng mã càng tốt.**
>
Nhiều ngôn ngữ lập trình cung cấp nhiều phạm vi / cấp độ truy cập, bao gồm phạm vi mô-đun, lớp, chức năng và khối. Sử dụng quyền truy cập hạn chế thường tốt hơn vì nó có nghĩa là biến chỉ có thể được “nhìn thấy” bởi ít dòng mã hơn.
Tại sao lại làm điều này? Bởi vì nó làm giảm một cách hiệu quả số lượng các biến mà người đọc phải nghĩ đến cùng một lúc. Nếu bạn thu hẹp phạm vi của tất cả các biến của mình theo hệ số hai, thì trung bình sẽ có một nửa số biến trong phạm vi bất kỳ lúc nào.
Ví dụ: giả sử bạn có một lớp rất lớn, với một biến thành viên chỉ được sử dụng bởi hai phương thức, theo cách sau:
```python
  class LargeClass {
    string str_;
    void Method1() {
      str_ = ...;
      Method2();
    }
    void Method2() {
      // Sử dụng str_
    }
    // Rất nhiều phương thức khác không sử dụng str_ …
  };
```
Theo một nghĩa nào đó, một biến thành viên của lớp giống như một "mini-global" bên trong phạm vi của lớp. Đặc biệt, đối với các lớp lớn, thật khó để theo dõi tất cả các biến thành viên và phương thức nào đó đã sửa đổi từng biến. Càng ít "mini-global" càng tốt.
Đối với trường hợp này, có thể hợp lý khi “hạ cấp” `str_` thành một biến cục bộ:
```python
  class LargeClass {
    void Method1() {
      string str = ...;
      Method2(str);
    }
    void Method2(string str) {
      // Sử dụng str
    }
    // Bây giờ những phương thức khác không thể nhìn thấy str.
  };
```
Một cách khác để hạn chế quyền truy cập vào các thành viên trong lớp là đặt càng nhiều phương thức tĩnh càng tốt. Các phương thức tĩnh là một cách tuyệt vời để cho người đọc biết “các dòng mã này được tách biệt khỏi các biến đó”.
Hoặc một cách tiếp cận khác là chia lớp lớn thành các lớp nhỏ hơn. Cách tiếp cận này chỉ hữu ích nếu các lớp nhỏ hơn trên thực tế bị cô lập với nhau. Nếu bạn tạo ra hai lớp có thể truy cập vào các thành viên của nhau, bạn đã không thực sự hoàn thành được bất cứ điều gì.
Điều tương tự cũng xảy ra đối với việc chia nhỏ các tệp lớn thành các tệp nhỏ hơn hoặc các chức năng lớn thành các chức năng nhỏ hơn. Động lực lớn để làm như vậy là tách biệt dữ liệu (tức là các biến).
Nhưng các ngôn ngữ khác nhau có các quy tắc khác nhau về những gì chính xác tạo nên một phạm vi. Chúng tôi chỉ muốn chỉ ra một số quy tắc thú vị liên quan đến phạm vi của các biến.
## Phạm vi mệnh đề if trong C ++

Giả sử bạn có mã C++ sau:
```c++
  PaymentInfo* info = database.ReadPaymentInfo();
  if (info) {
    cout << "User paid: " << info->amount() << endl;
  }
  // Nhiều dòng mã khác bên dưới...
```
Thông tin biến sẽ vẫn nằm trong phạm vi cho phần còn lại của hàm, vì vậy người đọc mã này có thể ghi nhớ thông tin, tự hỏi nếu / làm thế nào nó sẽ được sử dụng lại.
Nhưng trong trường hợp này, thông tin chỉ được sử dụng bên trong câu lệnh if. Trong C++, chúng ta thực sự có thể xác định thông tin trong biểu thức điều kiện:
```c++
  if (PaymentInfo* info = database.ReadPaymentInfo()) {
    cout << "User paid: " << info->amount() << endl;
  }
```
Giờ đây, người đọc có thể dễ dàng quên thông tin sau khi nó vượt ra khỏi phạm vi.

## Tạo các biến "private" trong JavaScript

Giả sử bạn có một biến cố định chỉ được sử dụng bởi một hàm:
```javascript
  submitted = false; // chú ý: biến toàn cục
  var submit_form = function (form_name) {
    if (submitted) {
      return; // đừng gửi form 2 lần
    }
    …
    submitted = true;
  };
```
Các biến toàn cục như đã gửi có thể khiến người đọc mã rất khó chịu. Có vẻ như submit_form() là hàm duy nhất sử dụng submit, nhưng bạn không thể biết chắc. Trên thực tế, một tệp JavaScript khác cũng có thể đang sử dụng một biến toàn cục có tên được gửi cho một mục đích khác!
Bạn có thể ngăn chặn sự cố này bằng cách gói `submitted` bên trong một lần đóng:
```Javascript
  var submit_form = (function () {
    var submitted = false; // Chú ý: chỉ có thể được truy cập bởi hàm bên dưới
    return function (form_name) {
      if (submitted) {
        return; // đừng gửi form 2 lần
      }
      …
      submitted = true;
    };
  }());
```
Lưu ý dấu ngoặc đơn ở dòng cuối cùng — hàm bên ngoài ẩn danh được thực thi ngay lập tức, trả về hàm bên trong.
Nếu bạn chưa từng thấy kỹ thuật này trước đây, thoạt nhìn có thể rất lạ. Nó có tác dụng tạo một phạm vi "riêng tư" mà chỉ hàm bên trong mới có thể truy cập. Giờ đây, người đọc không phải tự hỏi Nơi nào khác đã gửi được sử dụng? hoặc lo lắng về xung đột với các biến toàn cục khác cùng tên. (Xem JavaScript: The Good Parts của Douglas Crockford [O’Reilly, 2008] để biết thêm các kỹ thuật như thế này.)

## Phạm vi toàn cục của JavaScript

Trong JavaScript, nếu bạn bỏ qua từ khóa var khỏi định nghĩa biến (ví dụ: x = 1 thay vì var x = 1), thì biến sẽ được đưa vào phạm vi toàn cục, nơi mọi tệp JavaScript và khối <script> đều có thể truy cập nó. Đây là một ví dụ:
```javascript
  <script>
    var f = function () {
      // NGUY HIỂM: 'i' không được khai báo với 'var'!
      for (i = 0; i < 10; i += 1) …
    };
    f();
  </script>
```
Mã này vô tình đưa tôi vào phạm vi toàn cục, vì vậy một khối sau này vẫn có thể nhìn thấy nó:
```Javascript
  <script>
    alert(i); // Cảnh báo '10'. 'i' là biến toàn cục!
  </script>
```
Nhiều lập trình viên không biết về quy tắc xác định phạm vi này và hành vi đáng ngạc nhiên này có thể gây ra các lỗi lạ. Một biểu hiện phổ biến của lỗi này là khi hai hàm cùng tạo một biến cục bộ có cùng tên, nhưng lại quên sử dụng var. Những chức năng này sẽ vô tình bị “nói chuyện chéo” và lập trình viên kém có thể kết luận rằng máy tính của anh ta bị chiếm hữu hoặc RAM của anh ta đã hỏng.
“Phương pháp hay nhất” phổ biến cho JavaScript là luôn xác định các biến bằng cách sử dụng từ khóa var (ví dụ: var x = 1). Phương pháp này giới hạn phạm vi của biến đối với hàm (trong cùng) nơi nó được xác định.

## Không có phạm vi lồng nhau trong Python và JavaScript

Các ngôn ngữ như C ++ và Java có phạm vi khối, trong đó các biến được xác định bên trong cấu trúc if, for, try hoặc tương tự được giới hạn trong phạm vi lồng nhau của khối đó:
```c++
  if (...) {
    int x = 1;
  }
  x++; // Biên dịch lỗi! 'x' không được xác định.
```
Nhưng trong Python và JavaScript, các biến được xác định trong một khối "tràn ra" cho toàn bộ hàm.
Ví dụ: lưu ý việc sử dụng example_value trong mã Python hoàn toàn hợp lệ này:
```Python
  # No use of example_value up to this point.
  if request:
  for value in request.values:
  if value > 0:
  example_value = value
  break
  for logger in debug.loggers:
  logger.log("Example:", example_value)
```
Quy tắc xác định phạm vi này gây ngạc nhiên cho nhiều lập trình viên và mã như thế này khó đọc hơn. Trong các ngôn ngữ khác, sẽ dễ dàng hơn để tìm nơi example_value được xác định đầu tiên — bạn sẽ nhìn dọc theo “cạnh bên trái” của hàm mà bạn đang ở bên trong.
Ví dụ trước cũng có lỗi: nếu example_value không được đặt trong phần đầu tiên của mã, phần thứ hai sẽ đưa ra một ngoại lệ:
"NameError:‘ example_value ’không được xác định”.
Chúng tôi có thể khắc phục điều này và làm cho mã dễ đọc hơn, bằng cách xác định example_value ở "tổ tiên chung gần nhất" (về mặt lồng) cho nơi mã được sử dụng:
```python
example_value = None
if request:
for value in request.values:
if value > 0:
example_value = value
break
if example_value:
for logger in debug.loggers:
logger.log("Example:", example_value)
```
Tuy nhiên, đây là trường hợp mà example_value có thể bị loại bỏ hoàn toàn. example_value chỉ chứa một kết quả trung gian và như chúng ta đã thấy trong “Loại bỏ kết quả trung gian” trên trang 95, các biến như thế này thường có thể bị loại bỏ bằng cách “hoàn thành nhiệm vụ càng sớm càng tốt”. Trong trường hợp này, điều đó có nghĩa là ghi lại giá trị ví dụ ngay khi chúng tôi tìm thấy nó.
Đây là mã mới trông như thế nào:
```python
  def LogExample(value):
  for logger in debug.loggers:
  logger.log("Example:", value)
  if request:
  for value in request.values:
  if value > 0:
  LogExample(value) # deal with 'value' immediately
  break
```
## Di chuyển định nghĩa xuống

Ngôn ngữ lập trình C ban đầu yêu cầu tất cả các định nghĩa biến phải ở trên cùng của hàm hoặc khối. Yêu cầu này là không may, bởi vì đối với các hàm dài với nhiều biến, nó buộc người đọc phải suy nghĩ về tất cả các biến đó ngay lập tức, ngay cả khi chúng không được sử dụng cho đến sau này. (C99 và C ++ đã loại bỏ yêu cầu này.)
Trong ví dụ sau, tất cả các biến được xác định một cách vô tội vạ ở trên cùng của hàm:
```c
  def ViewFilteredReplies(original_id):
  filtered_replies = []
  root_message = Messages.objects.get(original_id)
  all_replies = Messages.objects.select(root_id=original_id)
  root_message.view_count += 1
  root_message.last_view_time = datetime.datetime.now()
  root_message.save()
  for reply in all_replies:
  if reply.spam_votes <= MAX_SPAM_VOTES:
  filtered_replies.append(reply)
  return filtered_replies
```
Vấn đề với mã ví dụ này là nó buộc người đọc phải suy nghĩ về ba biến cùng một lúc và chuyển các bánh răng giữa chúng.
Bởi vì người đọc không cần phải biết về tất cả chúng cho đến sau này, nên thật dễ dàng chỉ cần di chuyển từng định nghĩa ngay trước lần sử dụng đầu tiên:
```python
  def ViewFilteredReplies(original_id):
  root_message = Messages.objects.get(original_id)
  root_message.view_count += 1
  root_message.last_view_time = datetime.datetime.now()
  root_message.save()
  all_replies = Messages.objects.select(root_id=original_id)
  filtered_replies = []
  for reply in all_replies:
  if reply.spam_votes <= MAX_SPAM_VOTES:
  filtered_replies.append(reply)
  return filtered_replies
```
Bạn có thể tự hỏi liệu all_replies có phải là một biến cần thiết hay nó có thể bị loại bỏ bằng cách thực hiện:
```python
  for reply in Messages.objects.select(root_id=original_id):
  ...
```
Trong trường hợp này, all_replies là một biến giải thích khá tốt, vì vậy chúng tôi quyết định giữ nó.

## Ưu tiên các biến ghi một lần

![Image from 003_the_art_of_readable_code, page 93](https://user-images.githubusercontent.com/38417570/171200835-6bfa512a-982c-42a5-ba94-df148b68c7c3.jpg)

Cho đến nay trong chương này, chúng ta đã thảo luận về việc khó hiểu các chương trình có nhiều biến số “đang diễn ra” như thế nào. Chà, thậm chí còn khó hơn khi nghĩ về các biến liên tục thay đổi.
Theo dõi các giá trị của chúng thêm một mức độ khó khăn.
Để giải quyết vấn đề này, chúng tôi có một gợi ý nghe có vẻ hơi kỳ lạ: ưu tiên viết một lần các biến.
Các biến là một "vật cố định vĩnh viễn" dễ nghĩ hơn. Chắc chắn, các hằng số như:
```python
static const int NUM_THREADS = 10;
```
không yêu cầu người đọc phải suy nghĩ nhiều. Và vì lý do tương tự, việc sử dụng const trong C ++ (và cuối cùng trong Java) rất được khuyến khích.
Trên thực tế, trong nhiều ngôn ngữ (bao gồm cả Python và Java), một số kiểu tích hợp sẵn như chuỗi là bất biến. Như James Gosling (người tạo ra Java) đã nói, “[Bất biến] có xu hướng thường không gặp rắc rối hơn”.
Nhưng ngay cả khi bạn không thể ghi biến một lần, nó vẫn hữu ích nếu biến thay đổi ở ít vị trí hơn.
> **Ý TƯỞNG CHÍNH**
> 

> **Một biến càng bị thao túng nhiều vị trí thì càng khó lý luận về giá trị hiện tại của nó.**
>
vậy bạn sẽ làm sao? Làm thế nào bạn có thể thay đổi một biến thành ghi một lần? Chà, nhiều khi nó đòi hỏi phải cấu trúc lại mã một chút, như bạn sẽ thấy trong ví dụ tiếp theo.

## Một ví dụ cuối cùng

Đối với ví dụ cuối cùng của chương, chúng tôi muốn đưa ra một ví dụ chứng minh nhiều nguyên tắc mà chúng tôi đã thảo luận cho đến nay.
Giả sử bạn có một trang web với một số trường văn bản đầu vào, được sắp xếp như sau:
```html
<input type="text" id="input1" value="Dustin">
<input type="text" id="input2" value="Trevor">
<input type="text" id="input3" value="">
<input type="text" id="input4" value="Melissa">
...
```
Như bạn có thể thấy, id bắt đầu bằng input1 và tăng dần từ đó.
Công việc của bạn là viết một hàm có tên setFirstEmptyInput() lấy một chuỗi và đặt nó vào <input> trống đầu tiên trên trang (trong ví dụ minh họa là "input3"). Hàm phải trả về phần tử DOM đã được cập nhật (hoặc null nếu không còn đầu vào trống). Dưới đây là một số mã để thực hiện việc này không áp dụng các nguyên tắc trong chương này:
```javascript
  var setFirstEmptyInput = function (new_value) {
    var found = false;
    var i = 1;
    var elem = document.getElementById('input' + i);
    while (elem !== null) {
      if (elem.value === '') {
        found = true;
        break;
      }
      i++;
      elem = document.getElementById('input' + i);
    }
    if (found) elem.value = new_value;
    return elem;
  };
```
Mã này hoàn thành công việc, nhưng nó không đẹp. Có gì sai với nó và làm cách nào để chúng tôi cải thiện nó?
Có rất nhiều cách để suy nghĩ về việc cải thiện mã này, nhưng chúng tôi sẽ xem xét nó từ quan điểm của các biến mà nó sử dụng:
- var found
- var i
- var elem
Cả ba biến này đều tồn tại cho toàn bộ hàm và được ghi vào nhiều lần. Hãy cố gắng cải thiện việc sử dụng từng người trong số họ.
Như chúng ta đã thảo luận trước đó trong chương, các biến trung gian như found thường có thể bị loại bỏ bằng cách quay lại sớm. Đây là cải tiến đó:
```javascript
  var setFirstEmptyInput = function (new_value) {
    var i = 1;
    var elem = document.getElementById('input' + i);
    while (elem !== null) {
    if (elem.value === '') {
      elem.value = new_value;
      return elem;
    }
    i++;
    elem = document.getElementById('input' + i);
  }
  return null;
};
```
Tiếp theo, hãy nhìn vào độ cao. Nó được sử dụng nhiều lần trong toàn bộ mã theo một cách rất “lộn xộn” mà khó theo dõi được giá trị của nó. Đoạn mã làm cho có vẻ như elem là giá trị mà chúng tôi đang lặp lại, trong khi thực sự chúng tôi chỉ đang tăng dần qua i. Vì vậy, hãy cấu trúc lại vòng lặp while thành vòng lặp for trên tôi:
```javascript
  var setFirstEmptyInput = function (new_value) {
    for (var i = 1; true; i++) {
      var elem = document.getElementById('input' + i);
      if (elem === null)
      return null; // Search Failed. No empty input found.
      if (elem.value === '') {
        elem.value = new_value;
        return elem;
      }
    }
  };
```
Đặc biệt, hãy lưu ý cách elem hoạt động như một biến ghi một lần có tuổi thọ được chứa bên trong vòng lặp. Việc sử dụng true làm điều kiện vòng lặp for là không bình thường, nhưng đổi lại, chúng ta có thể thấy định nghĩa và các sửa đổi của i trong một dòng duy nhất. (Một thời gian truyền thống (đúng) cũng sẽ hợp lý.)

## Tổng kết

Chương này nói về cách các biến trong một chương trình có thể nhanh chóng tích lũy và trở nên quá nhiều để theo dõi. Bạn có thể làm cho mã của mình dễ đọc hơn bằng cách có ít biến hơn và làm cho chúng “nhẹ” nhất có thể. Đặc biệt:
- Loại bỏ các biến cản trở. Đặc biệt, chúng tôi đã đưa ra một vài ví dụ về cách loại bỏ các biến “kết quả trung gian” bằng cách xử lý kết quả ngay lập tức.
- Giảm phạm vi của mỗi biến càng nhỏ càng tốt. Di chuyển mỗi biến đến một nơi mà ít dòng mã nhất có thể nhìn thấy nó. Ra khỏi tầm mắt là mất trí.
- Ưu tiên các biến ghi một lần. Các biến chỉ được đặt một lần (hoặc const, cuối cùng hoặc bất biến) làm cho mã dễ hiểu hơn.
