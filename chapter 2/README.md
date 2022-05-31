# Chapter 2: Packing Information into Names - Đóng gói thông tin thành tên

<img width="346" alt="image" src="https://user-images.githubusercontent.com/95276558/170826985-79b334d7-bdc1-45b5-91ad-933746aa7bb1.png">

Cho dù bạn đang đặt tên cho một biến, một hàm hay một lớp, thì rất nhiều nguyên tắc áp dụng giống nhau. Chúng tôi thích nghĩ về một cái tên như một nhận xét nhỏ. Mặc dù không có nhiều chỗ trống, bạn có thể truyền tải rất nhiều thông tin bằng cách chọn một cái tên hay.

## KEY IDEA

Gói thông tin vào tên của bạn.
Rất nhiều cái tên mà chúng ta thấy trong các chương trình rất mơ hồ, chẳng hạn như `tmp`. Ngay cả những từ có vẻ hợp lý, chẳng hạn như `size` hoặc `get`, không đóng gói nhiều thông tin. Chương này hướng dẫn bạn cách chọn những cái tên phù hợp.
Chương này được sắp xếp thành sáu chủ đề cụ thể:

- Chọn các từ cụ thể
- Tránh các tên chung chung (hoặc biết khi nào nên sử dụng chúng)
- Sử dụng tên cụ thể thay vì tên trừu tượng
- Đính kèm thông tin bổ sung vào tên, bằng cách sử dụng hậu tố hoặc tiền tố
- Quyết định tên nên dài bao nhiêu
- Sử dụng định dạng tên để đóng gói thêm thông tin

## Chọn các từ cụ thể

Một phần của việc “đóng gói thông tin thành tên” là chọn những từ rất cụ thể và tránh những từ “trống rỗng”.
Ví dụ: từ “get” rất không cụ thể, như trong ví dụ này:

```ruby
def GetPage (url): ...
```

Từ “get” không thực sự nói lên nhiều điều. Phương pháp này có lấy một trang từ bộ nhớ cache cục bộ, từ cơ sở dữ liệu hoặc từ Internet không? Nếu là từ Internet, một tên cụ thể hơn có thể là `FetchPage()` hoặc `DownloadPage()`.
Đây là một ví dụ về một lớp Cây nhị phân:

```ruby
class BinaryTree {
  int Size ();
  ...
};
```

Bạn mong đợi điều gì phương thức `Size()` để trả về? Chiều cao của cây, số lượng nút, hoặc dấu chân bộ nhớ của cây?
Vấn đề là ở đó `Size()` không truyền đạt nhiều thông tin. Một tên cụ thể hơn sẽ là `Height()`, `NumNodes()`, hoặc `MemoryBytes()`.
Một ví dụ khác, giả sử bạn có một số loại lớp Thread:

```ruby
class Thread {void Stop();
...
};
```

Tên `Stop()` không sao cả, nhưng tùy thuộc vào chức năng chính xác của nó, có thể có một tên cụ thể hơn. Ví dụ, bạn có thể gọi nó là `Kill()` thay vào đó, nếu đó là một thao tác nặng không thể hoàn tác. Hoặc bạn có thể gọi `Pause()`, nếu có một cách `Resume()` nó.

## Tìm thêm các từ "đầy màu sắc"

<img width="346" alt="image" src="https://user-images.githubusercontent.com/95276558/170827046-f5f77e7d-b0f6-4431-8736-5c491e3de3cb.png">

Đừng ngại sử dụng từ điển đồng nghĩa hoặc nhờ bạn bè gợi ý về tên hay hơn. Tiếng Anh là một ngôn ngữ phong phú, và có rất nhiều từ để bạn lựa chọn.
Dưới đây là một số ví dụ về một từ, cũng như các phiên bản "đầy màu sắc" hơn có thể áp dụng cho trường hợp của bạn:

|  Từ   | Giải pháp thay thế                                                                                         |
| :---: | ---------------------------------------------------------------------------------------------------------- |
| send  | Deliver (cung cấp), dispatch (điều phối), announce (thông báo), distribute (phân phối), route (định tuyến) |
| find  | search (tìm kiếm), extract (giải nén), locate (xác định vị trí),recover (phục hồi)                         |
| start | lauch (khởi chạy), create (tạo), begin (bắt đầu), open (mở)                                                |
| make  | create (tạo), setup (thiết lập), build (xây dựng), generate (tạo), compose (soạn), add (thêm), new (mới)   |

Tuy nhiên, đừng để bị cuốn trôi. Trong PHP, có một chức năng để `explode()` một chuỗi. Đó là một cái tên đầy màu sắc và nó vẽ nên một bức tranh tuyệt đẹp về việc phá vỡ một thứ gì đó thành nhiều mảnh, nhưng nó khác gì với `split()`? (Hai chức năng khác nhau, nhưng thật khó để đoán sự khác biệt của chúng dựa trên tên gọi.

## KEY IDEA

Tốt hơn là nên rõ ràng và chính xác hơn là dễ thương.
Tránh những tên chung chung như `tmp` và `retval`
Những cái tên như `tmp`, `retval`, và `foo` thường là những lời cảnh cáo có nghĩa là "Tôi không thể nghĩ ra một cái tên." Thay vì sử dụng một tên trống như thế này, hãy chọn một tên mô tả giá trị hoặc mục đích của thực thể.
Ví dụ: đây là một hàm JavaScript sử dụng `retval`:

```js
var euclidean_norm = function (v) {
  var retval = 0.0;
  for (var i = 0; i < v.length; i += 1) {
    retval += v[i] * v[i];
  }
  return Math.sqrt(retval);
};
```

Nó hấp dẫn để sử dụng `retval` khi bạn không thể nghĩ ra một cái tên tốt hơn cho giá trị trả về của mình. Nhưng _retval_ không chứa nhiều thông tin ngoài “Tôi là giá trị trả về” (dù sao thì điều này cũng thường hiển nhiên).
Một cái tên tốt hơn sẽ mô tả mục đích của biến hoặc giá trị mà nó chứa. Trong trường hợp này, biến đang tích lũy tổng các bình phương của v. Vì vậy, một cái tên hay hơn là `sum_squares`. Điều này sẽ thông báo trước mục đích của biến và có thể giúp bắt lỗi.
Ví dụ, hãy tưởng tượng nếu bên trong vòng lặp vô tình:

```js
retval += v[i];
```

Lỗi này sẽ rõ ràng hơn nếu tên sum_squares:

```js
sum_squares += v[i];
// Đâu là "hình vuông" mà chúng ta đang tính tổng? Bug!
```

### LỜI KHUYÊN

Tên `retval` không đóng gói nhiều thông tin. Thay vào đó, hãy sử dụng tên mô tả giá trị của biến.
Tuy nhiên, có một số trường hợp những cái tên chung chung thực sự mang ý nghĩa. Hãy xem khi nào thì hợp lý khi sử dụng chúng.
tmp
Hãy xem xét trường hợp cổ điển của việc hoán đổi hai biến:

```js
if (right < left) {
  tmp = right;
  right = left;
  left = tmp;
}
```

Trong những trường hợp như thế này, tên tmp là hoàn toàn tốt. Mục đích duy nhất của biến là lưu trữ tạm thời, với thời gian tồn tại chỉ vài dòng. Tên `tmp` truyền tải ý nghĩa cụ thể cho người đọc - rằng biến này không có nhiệm vụ nào khác. Nó không được chuyển cho các chức năng khác hoặc được đặt lại hoặc sử dụng lại nhiều lần.
Nhưng đây là một trường hợp mà `tmp` chỉ được sử dụng vì sự lười biếng:

```js
String tmp = user.name();
tmp += "" + user.phone_number();
tmp += "" + user.email();
...
template.set("user_info", tmp);
```

Mặc dù biến này có tuổi thọ ngắn, nhưng việc lưu trữ tạm thời không phải là điều quan trọng nhất đối với biến này. Thay vào đó, một cái tên như `user_info` sẽ được mô tả nhiều hơn.
Trong trường hợp sau, tmp nên có trong tên, nhưng chỉ là một phần của nó:

```js
tmp_file= tempfile.NamedTemporaryFile ()
...
SaveData (tmp_file, ...)
```

Lưu ý rằng chúng tôi đã đặt tên cho biến `tmp_file` và không chỉ `tmp`, bởi vì nó là một đối tượng tệp. Hãy tưởng tượng nếu chúng ta vừa gọi nó `tmp`:

```
SaveData (tmp, ...)
```

Chỉ nhìn vào một dòng mã này, không rõ liệu tmp là một tệp, một tên tệp hoặc thậm chí có thể là dữ liệu đang được ghi.

### LỜI KHUYÊN

Tên `tmp` chỉ nên được sử dụng trong trường hợp thời gian tồn tại ngắn hạn và tạm thời là thực tế quan trọng nhất về biến đó.
Trình lặp vòng lặp
Những cái tên như `i`, `j`, `iter`, và `it` thường được sử dụng làm chỉ số và trình lặp vòng lặp. Mặc dù những cái tên này là chung chung, chúng được hiểu là "Tôi là một người lặp". (Trên thực tế, nếu bạn sử dụng một trong những tên này cho một số mục đích khác, nó sẽ gây nhầm lẫn — vì vậy đừng làm như vậy!)
Nhưng đôi khi có những tên trình lặp tốt hơn i, j, và k. Ví dụ: các vòng lặp sau tìm người dùng thuộc câu lạc bộ nào:

```js
for (int i = 0; i <club.size(); i++)
  for (int j = 0; j < club[i].members.size(); j ++)
    for (int k = 0; k < users.size(); k ++)
      if (club[i].members[k] == users[j])
        cout << "user[" << j << "] is in club[" << i << "]" << endl;

```

Bên trong tuyên bố if, các `members[]` và `users[]` đang sử dụng chỉ mục sai. Khó có thể phát hiện ra những lỗi như thế này vì dòng mã đó có vẻ ổn khi tách biệt:

```ruby
if (club[i].members[k] == users[j])
```

Trong trường hợp này, việc sử dụng các tên chính xác hơn có thể hữu ích. Thay vì đặt tên cho các chỉ mục vòng lặp (i, j, k), một sự lựa chọn khác sẽ là (`clubs_i`, `members_i`, `users_i`) hoặc ngắn gọn hơn (`ci`, `mi`, `ui`). Cách tiếp cận này sẽ giúp lỗi nổi bật hơn:

```ruby
if (club[ci].members[ui] == users[mi]) # Bug! Các chữ cái đầu tiên không khớp với nhau.
```

Khi được sử dụng đúng cách, chữ cái đầu tiên của chỉ mục sẽ khớp với chữ cái đầu tiên của mảng:

```ruby
if (club[ci].members[mi] == users[ui]) # OK. Các chữ cái đầu tiên khớp nhau.
```

**Phán quyết về tên chung**
Như bạn đã thấy, có một số tình huống mà tên chung rất hữu ích.

### LỜI KHUYÊN

Nếu bạn định sử dụng một cái tên chung chung như tmp, it, hoặc retval, có lý do chính đáng để làm như vậy.

Phần lớn thời gian, họ bị lạm dụng vì sự lười biếng thuần túy. Điều này có thể hiểu được — khi không có gì tốt hơn trong tâm trí, sẽ dễ dàng hơn nếu chỉ sử dụng một cái tên vô nghĩa như foo và tiếp tục. Nhưng nếu bạn có thói quen dành thêm vài giây để nghĩ ra một cái tên hay, bạn sẽ thấy “khả năng đặt tên” của mình hình thành nhanh chóng.

## Ưu tiên tên cụ thể hơn tên trừu tượng

<img width="311" alt="image" src="https://user-images.githubusercontent.com/95276558/170827107-2f15b887-e121-4adb-8cb5-fe9a646bd906.png">

Khi đặt tên cho một biến, hàm hoặc phần tử khác, hãy mô tả nó một cách cụ thể thay vì trừu tượng.
Ví dụ: giả sử bạn có một phương thức nội bộ có tên `ServerCanStart()`, kiểm tra xem máy chủ có thể lắng nghe trên một cổng TCP / IP nhất định hay không. Tuy nhiên, tên `ServerCanStart()` là hơi trừu tượng. Một cái tên cụ thể hơn sẽ là `CanListenOnPort()`. Tên này mô tả trực tiếp những gì phương thức sẽ thực hiện.
Hai ví dụ tiếp theo minh họa khái niệm này sâu hơn.

### Ví dụ: DISALLOW_EVIL_CONTHERORS

Đây là một ví dụ từ cơ sở mã tại Google. Trong C++, nếu bạn không xác định hàm tạo bản sao hoặc toán tử gán cho lớp của mình, thì giá trị mặc định sẽ được cung cấp. Mặc dù tiện dụng, nhưng những phương pháp này có thể dễ dàng dẫn đến rò rỉ bộ nhớ và các rủi ro khác vì chúng được thực hiện "ở hậu trường" ở những nơi bạn có thể không nhận ra.
Do đó, Google có quy ước không cho phép các trình tạo “ác” này bằng cách sử dụng macro:

```c++
class ClassName {
  private: DISALLOW_EVIL_CONTHERORS(ClassName);
  public: ...
};
```

Macro này được định nghĩa là:

```c++
#define DISALLOW_EVIL_CONTHERORS(ClassName)
  ClassName(const ClassName&);
    void operator = (const ClassName&);
```

Bằng cách đặt macro này trong private một lớp, hai phương thức này trở thành private, do đó chúng không thể được sử dụng, thậm chí là vô tình.
Mặc dù vậy tên `DISALLOW_EVIL_CONTHERORS` không tốt lắm. Việc sử dụng từ "ác" thể hiện một lập trường quá mạnh mẽ về một vấn đề gây tranh cãi. Quan trọng hơn, không rõ macro đó không cho phép điều gì. Nó không cho phép phương thức `operator()` và đó thậm chí không phải là một "constructor"!
Cái tên đã được sử dụng trong nhiều năm nhưng cuối cùng đã được thay thế bằng một cái gì đó ít khiêu khích hơn và cụ thể hơn:

```c++
#define DISALLOW_COPY_AND_ASSIGN (ClassName) ...
```

### Ví dụ: --run_locally

Một trong các chương trình của chúng tôi có cờ dòng lệnh tùy chọn có tên `--run_locally`. Cờ này sẽ khiến chương trình in thêm thông tin gỡ lỗi nhưng chạy chậm hơn. Cờ thường được sử dụng khi kiểm tra trên máy cục bộ, chẳng hạn như máy tính xách tay. Nhưng khi chương trình đang chạy trên một máy chủ từ xa, hiệu suất là rất quan trọng, vì vậy cờ không được sử dụng.
Bạn có thể xem cách đặt tên `--run_locally` xuất hiện, nhưng nó có một số vấn đề:

- Một thành viên mới của nhóm không biết nó đã làm gì. Anh ấy sẽ sử dụng nó khi chạy cục bộ (hãy tưởng tượng điều đó), nhưng anh ấy không biết tại sao nó lại cần thiết.
- Đôi khi, chúng tôi cần in thông tin gỡ lỗi trong khi chương trình chạy từ xa. Đi qua `--run_locally` đối với một chương trình đang chạy từ xa trông rất buồn cười và thật khó hiểu.
- Đôi khi chúng tôi chạy kiểm tra hiệu suất cục bộ và không muốn ghi nhật ký làm chậm nó, vì vậy chúng tôi sẽ không sử dụng `--run_locally`.
  Vấn đề là ở đó `--run_locally` được đặt tên theo trường hợp mà nó thường được sử dụng. Thay vào đó, một tên cờ như `--extra_logging` sẽ trực tiếp và rõ ràng hơn.
  Nhưng nếu `--run_locally` cần phải làm gì nhiều hơn là chỉ ghi nhật ký bổ sung? Ví dụ, giả sử rằng nó cần thiết lập và sử dụng một cơ sở dữ liệu cục bộ đặc biệt. Bây giờ tên `--run_locally` có vẻ hấp dẫn hơn vì nó có thể kiểm soát cả hai điều này cùng một lúc.
  Nhưng sử dụng nó cho mục đích đó sẽ chọn một cái tên vì nó mơ hồ và gián tiếp, điều này có lẽ không phải là một ý kiến hay. Giải pháp tốt hơn là tạo một cờ thứ hai có tên `--use_local_database`. Mặc dù bây giờ bạn phải sử dụng hai cờ, các cờ này rõ ràng hơn nhiều; họ không cố gắng làm tan vỡ hai ý tưởng trực giao thành một, và họ cung cấp cho bạn tùy chọn sử dụng chỉ một ý tưởng chứ không phải ý tưởng khác.

## Đính kèm thông tin bổ sung vào tên

<img width="311" alt="image" src="https://user-images.githubusercontent.com/95276558/170827133-e5c80ce1-ea3d-4a3e-8d5e-e5a8203af0c1.png">

Như chúng ta đã đề cập trước đây, tên của một biến giống như một chú thích nhỏ. Mặc dù không có nhiều chỗ, bất kỳ thông tin bổ sung nào bạn chèn vào tên sẽ được nhìn thấy mỗi khi biến được nhìn thấy.
Vì vậy, nếu có điều gì đó rất quan trọng về một biến mà người đọc phải biết, thì bạn nên gắn thêm một “từ” vào tên. Ví dụ: giả sử bạn có một biến chứa chuỗi thập lục phân:

```js
string id; // Ví dụ: "af84ef845cd8"
```

Thay vào đó bạn có thể muốn đặt tên cho nó là `hex_id`, nếu điều quan trọng là người đọc phải nhớ định dạng của ID.

### Giá trị với các đơn vị

Nếu biến của bạn là một phép đo (chẳng hạn như một lượng thời gian hoặc một số byte), thì việc mã hóa các đơn vị thành tên của biến sẽ rất hữu ích.
Ví dụ: đây là một số mã JavaScript đo thời gian tải của một trang web:

```js
var start = (new Date()).getTime(); // đầu trang
...
var elapsed = (new Date()).getTime() - start; // cuối trang
document.writeln("Thời gian tải là:" + elapsed + "giây");
```

Rõ ràng là không có gì sai với mã này, nhưng nó không hoạt động, bởi vì dành thời gian()trả về mili giây, không phải giây.
Bằng cách chắp thêm_bệnh đa xơ cứngđối với các biến của chúng tôi, chúng tôi có thể làm cho mọi thứ rõ ràng hơn:

```js
var start_ms = (new Date()).getTime(); // đầu trang
...
var elapsed_ms = (new Date()).getTime() - start_ms; // cuối trang
document.writeln("Thời gian tải là:" + elapsed_ms / 1000 + "seconds");
```

Ngoài thời gian, có rất nhiều đơn vị khác xuất hiện trong lĩnh vực lập trình. Dưới đây là bảng tham số hàm không đơn vị và các phiên bản tốt hơn bao gồm các đơn vị:
| Tham số chức năng | Đổi tên tham số để mã hóa đơn vị |
| ----------------------------- | -------------------------------- |
| Start(int delay ) | delay → delay_secs |
| CreateCache(int size ) | size → size_mb |
| ThrottleDownload(float limit) | limit → max_kbps |
| Rotate(float angle) | angle → degrees_cw |

### Mã hóa các thuộc tính quan trọng khác

Kỹ thuật gắn thêm thông tin vào tên này không giới hạn ở các giá trị có đơn vị. Bạn nên làm điều đó bất cứ khi nào có điều gì đó nguy hiểm hoặc đáng ngạc nhiên về biến số.
Ví dụ: nhiều khai thác bảo mật đến từ việc không nhận ra rằng một số dữ liệu mà chương trình của bạn nhận được vẫn chưa ở trạng thái an toàn. Đối với điều này, bạn có thể muốn sử dụng các tên biến như `untrustedUrl` hoặc `unsafeMessageBody`. Sau khi gọi các hàm đầu vào không an toàn, các biến kết quả có thể là `TrustUrl` hoặc `safeMessageBody`.
Bảng sau đây cho thấy các ví dụ bổ sung về thời điểm thông tin bổ sung nên được mã hóa trong tên:

| Tình huống                                                                       | Tên biến |    Tên hay hơn     |
| -------------------------------------------------------------------------------- | :------: | :----------------: |
| Một mật khẩu mở khóa ở dạng “plaintext” và phải được mã hóa trước khi xử lý thêm | password | plaintext_password |
| Một người dùng cung cấpnhận xétcần thoát trước khi được hiển thị                 | comment  | unescaped_comment  |
| Byte trong sốhtmlđã được chuyển đổi thành UTF-8                                  |   html   |     html_utf8      |
| Dữ liệu đến đã được "mã hóa url"                                                 |   data   |    data_urlenc     |

Bạn không nên sử dụng các thuộc tính như `unescaped_` hoặc `_utf8` cho mọi biến trong chương trình của bạn. Chúng quan trọng nhất ở những nơi mà lỗi có thể dễ dàng xâm nhập nếu ai đó nhầm lẫn biến đó là gì, đặc biệt nếu hậu quả là nghiêm trọng, như với lỗi bảo mật. Về cơ bản, nếu đó là một điều quan trọng cần hiểu, hãy đặt tên cho nó.

## ĐÂY CÓ PHẢI LÀ THUYẾT MINH HUNGARIAN?

[Ký hiệu Hungary](<https://docs.microsoft.com/en-us/previous-versions/visualstudio/visual-studio-6.0/aa260976(v=vs.60)?redirectedfrom=MSDN>) là một hệ thống đặt tên được sử dụng rộng rãi bên trong Microsoft. Nó mã hóa “loại” của mọi biến thành tiền tố của tên. Dưới đây là một số ví dụ:

|    Tên    | Nghĩa                                                                                  |
| :-------: | -------------------------------------------------------------------------------------- |
|   pLast   | Một con trỏ (p) đến phần tử cuối cùng trong một số cấu trúc dữ liệu                    |
| pszBuffer | Một con trỏ (p) tới một (các) chuỗi ký tự (z) kết thúc bằng 0                          |
|    cch    | Đếm (c) ký tự (ch)                                                                     |
|  mpcopx   | Một bản đồ (m) từ một con trỏ đến một màu (pco) đến một con trỏ với độ dài trục x (px) |

Nó thực sự là một ví dụ về “gắn thuộc tính vào tên”. Nhưng đó là một hệ thống chính thức và chặt chẽ hơn, tập trung vào việc mã hóa một tập hợp các thuộc tính cụ thể.
Những gì chúng tôi đang ủng hộ trong phần này là một hệ thống rộng hơn, không chính thức hơn: xác định bất kỳ thuộc tính quan trọng nào của một biến và mã hóa chúng một cách dễ hiểu, nếu chúng cần thiết. Bạn có thể gọi nó là “English Notation”.

## Bao lâu nên có một cái tên?

<img width="346" alt="image" src="https://user-images.githubusercontent.com/95276558/170827149-d3a11ccf-036e-488e-a828-60849e61ea8c.png">

Khi chọn một cái tên hay, có một hạn chế ngầm là tên đó không được quá dài. Không ai thích làm việc với các số nhận dạng như thế này:
`newNavigationControllerWrappingViewControllerForDataSourceOfClass`

Tên càng dài thì càng khó nhớ và càng chiếm nhiều dung lượng trên màn hình, có thể gây ra các dòng thừa.
Mặt khác, các lập trình viên có thể đưa lời khuyên này đi quá xa, chỉ sử dụng tên một từ (hoặc tên singleletter). Vậy bạn nên quản lý sự đánh đổi này như thế nào? Làm thế nào để bạn quyết định giữa việc đặt tên cho một biến `d`, `days`, hoặc `days_since_last_update`?
Quyết định này là một lệnh gọi phán đoán mà câu trả lời tốt nhất phụ thuộc vào chính xác cách mà biến đó đang được sử dụng. Nhưng đây là một số hướng dẫn để giúp bạn quyết định.

### Tên ngắn hơn được chấp nhận cho phạm vi ngắn hơn

Khi bạn đi nghỉ ngắn ngày, bạn thường mang ít hành lý hơn so với khi bạn đi nghỉ dài ngày. Tương tự, các mã định danh có “phạm vi” nhỏ (bao nhiêu dòng mã khác có thể “nhìn thấy” tên này) không cần mang nhiều thông tin. Có nghĩa là, bạn có thể loại bỏ các tên ngắn hơn vì tất cả thông tin đó (loại biến là gì, giá trị ban đầu của nó, cách nó bị phá hủy) rất dễ thấy:

```ruby
if (debug) {
  map<string,int> m;
  LookUpNamesNumbers(&m);
  Print(m);
}
```

Mặc dù `m` không đóng gói bất kỳ thông tin nào, nó không phải là một vấn đề, bởi vì người đọc đã có tất cả thông tin mà cô ấy cần để hiểu mã này.
Tuy nhiên, giả sử `m` là một thành viên trong lớp hoặc một biến toàn cục và bạn đã thấy đoạn mã này:

```ruby
LookUpNamesNumbers(&m);
Print(m);
```

Mã này khó đọc hơn nhiều, vì không rõ loại hoặc mục đích của `m` là gì.
Vì vậy, nếu một định danh có phạm vi lớn, tên cần phải mang đủ thông tin để làm cho nó rõ ràng.

### Nhập tên dài — Không phải là vấn đề nữa

Có nhiều lý do chính đáng để tránh những cái tên dài, nhưng “chúng khó gõ hơn” không còn là một trong số đó nữa. Mọi trình soạn thảo văn bản lập trình mà chúng tôi từng thấy đều được tích hợp sẵn tính năng “hoàn thành từ”. Điều đáng ngạc nhiên là hầu hết các lập trình viên không biết đến tính năng này. Nếu bạn chưa thử tính năng này trên trình chỉnh sửa của mình, vui lòng đặt cuốn sách này xuống ngay bây giờ và thử nó:

1. Nhập một vài ký tự đầu tiên của tên.
2. Kích hoạt lệnh hoàn thành từ (xem bên dưới).
3. Nếu từ đã hoàn thành không đúng, hãy tiếp tục kích hoạt lệnh cho đến khi tên chính xác xuất hiện.
   Nó chính xác một cách đáng ngạc nhiên. Nó hoạt động trên bất kỳ loại tệp nào, bằng bất kỳ ngôn ngữ nào. Và nó hoạt động với bất kỳ mã thông báo nào, ngay cả khi bạn đang nhập nhận xét.

| Editor        | Command                  |
| ------------- | ------------------------ |
| Vi            | Ctrl-p                   |
| Emacs         | Meta-/ (hit ESC, then /) |
| Eclipse       | Alt-/                    |
| IntelliJ IDEA | Alt-/                    |
| TextMate      | ESC                      |

### Từ viết tắt và chữ viết tắt

Các lập trình viên đôi khi sử dụng các từ viết tắt và chữ viết tắt để giữ cho tên của chúng nhỏ đi — ví dụ: đặt tên cho một lớp `BEManager` thay vì `BackEndManager`. Sự co rút này có đáng để gây nhầm lẫn tiềm ẩn không?
Theo kinh nghiệm của chúng tôi, viết tắt dành riêng cho dự án thường là một ý tưởng tồi. Chúng có vẻ khó hiểu và đáng sợ đối với những người mới tham gia dự án. Có đủ thời gian, chúng thậm chí còn bắt đầu tỏ ra khó hiểu và đáng sợ đối với các tác giả.
Vì vậy, quy tắc ngón tay cái của chúng tôi là: liệu một đồng đội mới có hiểu tên có nghĩa là gì không? Nếu vậy thì có lẽ không sao đâu.
Ví dụ: khá phổ biến đối với các lập trình viên sử dụng `eval` thay vì `evaluation`, `doc` thay vì `document`, `str` thay vì `string`. Vì vậy, một đồng đội mới nhìn thấy `FormatStr()` có lẽ sẽ hiểu điều đó có nghĩa là gì. Tuy nhiên, họ có thể sẽ không hiểu thế nào là một `BEManager` là gì.

### Nói ra những từ không cần thiết

Đôi khi các từ bên trong tên có thể bị xóa mà không làm mất bất kỳ thông tin nào. Ví dụ, thay vì `ConvertToString()`, tên `ToString()` nhỏ hơn và không làm mất bất kỳ thông tin thực nào. Tương tự, thay vì `DoServeLoop()`, tên `ServeLoop()` cũng rõ ràng như vậy.

### Sử dụng định dạng tên để truyền đạt ý nghĩa

Cách bạn sử dụng dấu gạch dưới, dấu gạch ngang và viết hoa cũng có thể đóng gói thêm thông tin trong tên. Ví dụ: đây là một số mã C++ theo [quy ước định dạng được sử dụng cho Các dự án nguồn mở của Google](https://google.github.io/styleguide/):

```c++
static const int kMaxOpenFiles = 100;
class LogReader {
  public:
    void OpenFile(string local_file);
  private:
    int offset_;
    DISALLOW_COPY_AND_ASSIGN(LogReader);
};
```

Việc có các định dạng khác nhau cho các thực thể khác nhau giống như một dạng tô sáng cú pháp — nó giúp bạn đọc mã dễ dàng hơn.
Hầu hết các định dạng trong ví dụ này là khá phổ biến — sử dụng `CamelCase` cho tên lớp và sử dụng `lower_separated` cho các tên biến. Nhưng một số quy ước khác có thể khiến bạn ngạc nhiên.
Ví dụ, các giá trị không đổi có dạng `kConstantName` thay vì `CONSTANT_NAME`. Phong cách này có lợi ích là dễ dàng phân biệt với #define macro, là MACRO*NAME theo quy ước.
Các biến thành viên của lớp giống như các biến bình thường, nhưng phải kết thúc bằng dấu gạch dưới, như `offset*`. Thoạt đầu, quy ước này có vẻ lạ, nhưng có thể phân biệt ngay lập tức các thành viên với các biến khác rất tiện dụng. Ví dụ: nếu bạn đang xem qua mã của một phương thức lớn và thấy dòng:

```js
stats.clear();
```

Thông thường bạn có thể tự hỏi, Liệu `stats` thuộc về lớp này? Mã này có đang thay đổi trạng thái bên trong của lớp không? Nếu thành viên `_convention` được sử dụng, bạn có thể nhanh chóng kết luận, Không, `stats` phải là một biến cục bộ. Nếu không nó sẽ được đặt tên `stats_`.

### Các quy ước định dạng khác

Tùy thuộc vào ngữ cảnh của dự án hoặc ngôn ngữ của bạn, có thể có các quy ước định dạng khác mà bạn có thể sử dụng để đặt tên chứa nhiều thông tin hơn.
Ví dụ, trong [JavaScript: The Good Parts](https://www.oreilly.com/library/view/javascript-the-good/9780596517748/) (Douglas Crockford, O'Reilly, 2008), tác giả gợi ý rằng "constructors" (các hàm dự định được gọi với `new`) phải được viết hoa và các hàm thông thường phải bắt đầu bằng chữ thường:

```js
var x = new DatePicker(); // DatePicker () là một hàm "constructor"
var y = pageHeight(); // pageHeight () là một hàm bình thường
```

Đây là một ví dụ JavaScript khác: khi gọi hàm thư viện jQuery (có tên là ký tự đơn `$`), một quy ước hữu ích là đặt tiền tố kết quả jQuery cũng bằng `$` giống như:

```js
var $all_images = $("img"); // $ all_images là một đối tượng jQuery
var height = 250; // chiều cao thì không
```

Trong suốt mã, nó sẽ rõ ràng rằng `$all_images` là một đối tượng kết quả jQuery.
Đây là ví dụ cuối cùng, lần này là về HTML/CSS: khi thẻ HTML có thuộc tính id hoặc class, cả dấu gạch dưới và dấu gạch ngang đều là các ký tự hợp lệ để sử dụng trong giá trị. Một quy ước khả thi là sử dụng dấu gạch dưới để phân tách các từ trong ID và dấu gạch ngang để phân tách các từ trong các class:

```html
<div id="middle_column" class="main-content">...</div>
```

Việc bạn quyết định sử dụng các quy ước như thế này hay không là tùy thuộc vào bạn và nhóm của bạn. Nhưng cho dù bạn sử dụng hệ thống nào, hãy nhất quán trong toàn bộ dự án của bạn.

# Tóm lược

Chủ đề duy nhất của chương này là: **đóng gói thông tin vào tên của bạn**. Bởi điều này, chúng tôi muốn nói rằng người đọc có thể trích xuất rất nhiều thông tin chỉ từ việc đọc tên.
Dưới đây là một số mẹo cụ thể mà chúng tôi đã đề cập:

- **Sử dụng các từ cụ thể** — ví dụ, thay vì `Get`, từ như `Fetch` hoặc `Download` có thể tốt hơn, tùy thuộc vào ngữ cảnh.
- **Tránh những tên chung chung** như `tmp` và `retval`, trừ khi có một lý do cụ thể để sử dụng chúng.
- **Sử dụng tên cụ thể** mô tả mọi thứ chi tiết hơn — tên `ServerCanStart()` là mơ hồ so với `CanListenOnPort()`.
- **Đính kèm các chi tiết quan trọng** vào tên biến — ví dụ: `append _ms` thành một biến có giá trị tính bằng mili giây hoặc thêm vào trước `raw_to` một biến chưa cần xử lý thoát.
- **Sử dụng tên dài hơn cho phạm vi lớn hơn** — Không sử dụng các tên khó hiểu gồm một hoặc hai chữ cái cho các biến trải dài trên nhiều màn hình; tên ngắn hơn sẽ tốt hơn cho các biến chỉ kéo dài một vài dòng.
- **Sử dụng cách viết hoa, dấu gạch dưới, v.v. theo cách có ý nghĩa** — Ví dụ, bạn có thể thêm “\_” vào các thành viên trong lớp để phân biệt chúng với các biến cục bộ.
