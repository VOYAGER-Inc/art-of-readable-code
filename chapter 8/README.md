# Chapter 8: Breaking Down Giant Expressions

<img width="477" alt="Screen Shot 2022-05-30 at 08 59 43" src="https://user-images.githubusercontent.com/42312796/170903786-20d42536-300a-43ed-8520-ad8bde795cdb.png">

Mực khổng lồ là một loài động vật thông minh và đáng kinh ngạc, nhưng thiết kế cơ thể gần như hoàn hảo của nó có một lỗ hổng chết người: nó có một bộ não hình bánh rán (donut-shaped brain) bao quanh thực quản. Vì vậy, nếu nó nuốt quá nhiều thức ăn cùng một lúc, não của nó sẽ bị tổn thương.

Điều này có liên quan gì đến code? Chà, code có dạng "khối" quá lớn có thể dẫn đến ảnh hưởng tương tự. Nghiên cứu gần đây cho thấy rằng, hầu hết chúng ta chỉ có thể nghĩ về ba hoặc bốn "sự việc" tại một thời điểm.\* Nói một cách đơn giản, một biểu thức mã càng lớn thì nó sẽ càng khó hiểu.

**KEY IDEA (Thông điệp chính)**

> **Break down your giant expressions into more digestible pieces**
>
> _(Tạm dịch: Chia nhỏ các biểu thức khổng lồ của bạn thành các phần dễ hiểu (dễ tiêu hóa) hơn. )_

Trong chương này, chúng tôi sẽ xem xét các cách khác nhau mà bạn có thể thao tác và chia nhỏ mã của mình
để dễ hiểu hơn.

_(\* Cowan, N. (2001). The magical number 4 in short-term memory: A reconsideration of mental storage capacity. Behavioral and Brain Sciences, 24, 97–185.)_

### Explaining Variables

Cách đơn giản nhất để chia nhỏ một biểu thức là thêm một biến bổ sung để chứa một biểu thức con nhỏ hơn. Biến bổ sung này đôi khi được gọi là "explaining variable" vì nó giúp giải thích ý nghĩa của biểu thức con.

Ví dụ:

```ruby
if line.split(':')[0].strip() == "root":
    ...
```

Với mã tương tự, bây giờ thêm một "explaining variable":

```ruby
username = line.split(':')[0].strip()
if username == "root":
 ...
```

### Summary Variables

Ngay cả khi một biểu thức không cần giải thích (vì bạn có thể tìm ra ý nghĩa của nó), thì vẫn có thể hữu ích khi nạp biểu thức đó vào một biến mới. Chúng ta gọi đây là một "summary variable" - khi mục đích của nó chỉ đơn giản là thay thế một đoạn mã lớn hơn chỉ bằng một cái tên để có thể quản lý và suy nghĩ dễ dàng hơn.

Ví dụ, hãy xem xét các biểu thức trong mã này:

```js
if (request.user.id == document.owner_id) {
 // user can edit this document...
}
...
if (request.user.id != document.owner_id) {
 // document is read-only...
}
```

Biểu thức `request.user.id == document.owner_id` có vẻ không quá lớn, nhưng nó có 5 biến, vì vậy cần mất một chút thời gian để suy nghĩ về nó.

Khái niệm chính trong đoạn mã này là: "Người dùng có sở hữu tài liệu không?". Khái niệm có thể được nêu rõ ràng hơn bằng cách thêm một "summary variable":

```ruby
final boolean user_owns_document = (request.user.id == document.owner_id);
if (user_owns_document) {
 // user can edit this document...
}
...
if (!user_owns_document) {
 // document is read-only...
}
```

Tuy không nhiều, nhưng câu lệnh `if (user_own_document)` giúp việc suy nghĩ trở nên dễ hơn. Ngoài ra, việc xác định `user_own_document` ở trên cùng cho người đọc biết trước rằng “đây là một khái niệm mà chúng ta sẽ đề cập đến trong suốt function này."

### Sử dụng Luật De Morgan

Nếu bạn đã từng tham gia một khóa học về mạch điện hoặc logic, bạn có thể nhớ đến Luật De Morgan. Có hai cách để viết lại biểu thức boolean thành dạng tương đương:

```ruby
1) not (a or b or c) ⇔ (not a) and (not b) and (not c)
2) not (a and b and c) ⇔ (not a) or (not b) or (not c)
```

Nếu bạn gặp khó khăn khi nhớ những luật này, có một tóm tắt đơn giản là "Phân bổ `not` và chuyển đổi thành `and/or`.” (Hoặc theo cách khác, bạn "đặt thừa số chung `not`.")

Đôi khi bạn có thể sử dụng các luật này để làm cho biểu thức boolean dễ đọc hơn. Ví dụ, với mã của bạn là:

```js
if (!(file_exists && !is_protected)) Error("Sorry, could not read file.");
```

Có thể được viết lại như sau:

```js
if (!file_exists || is_protected) Error("Sorry, could not read file.");
```

### Lạm dụng Short-Circuit Logic (Logic Đoản mạch)

Hầu hết các ngôn ngữ lập trình, toán tử boolean thực hiện việc đánh giá mang tính chất short-circuit (đoản mạch). Ví dụ, câu lệnh `if (a || b)` sẽ không xét giá trị `b` nếu `a` đúng. Cách xử lý này rất tiện dụng nhưng đôi khi có thể bị lạm dụng để thực hiện logic phức tạp.

Đây là ví dụ về một câu lệnh được viết bởi một trong các tác giả:

```python
assert((!(bucket = FindBucket(key))) || !bucket->IsOccupied());
```

Đoạn mã này có nghĩa là: “Lấy `bucket` tương ứng với `key` này. Nếu `bucket` không rỗng, thì hãy đảm bảo rằng nó không bị chiếm dụng."

Mặc dù chỉ là một dòng code nhưng nó thực sự khiến hầu hết các lập trình viên phải dừng lại và suy nghĩ. Bây giờ hãy so sánh nó với đoạn code này:

```python
bucket = FindBucket(key);
if (bucket != NULL) assert(!bucket->IsOccupied());
```

Nó thực hiện chính xác điều tương tự, và mặc dù với hai dòng code, nhưng nó thật sự dễ hiểu hơn rất nhiều.

Vậy tại sao mã được viết dưới dạng một biểu thức khổng lồ ngay từ đầu? Vào thời điểm đó, có vẻ cảm thấy đoạn code này rất thông minh, cùng với niềm vui nhất định trong việc phân tích logic thành một đoạn code ngắn gọn. Có thể hiểu được — nó giống như giải một câu đố nhỏ, và tất cả chúng ta đều thích thú với việc này. Tuy nhiên, vấn đề của mã là đã trở thành một gờ giảm tốc độ suy nghĩ đối với bất kỳ ai đọc qua nó.

**KEY IDEA (Thông điệp chính)**

> **Beware of “clever” nuggets of code — they’re often confusing when others read the code later.**
>
> _(Tạm dịch: Hãy cẩn thận những đoạn code "thông minh" - chúng thường gây nhầm lẫn cho những người đọc code sau này. )_

Điều này có nghĩa là bạn nên tránh sử dụng short-circuit? Không. Có rất nhiều trường hợp mà nó có thể được sử dụng một cách ngắn gọn, ví dụ:

```python
if (object && object->method()) ...
```

Ngoài ra còn có một cách biểu diễn mới hơn đáng đề cập đến: với các ngôn ngữ như Python, JavaScript và Ruby, toán tử “or” trả về một trong các đối số của nó (không chuyển đổi thành boolean), như code bên dưới:

```python
x = a || b || c
```

Được dùng để lấy ra giá trị "truthy" đầu tiên từ a, b hoặc c.

### Ví dụ về việc vật lộn với logic phức tạp

Giả sử bạn đang triển khai lớp `Range` như sau:
```c
struct Range {
    int begin;
    int end;
    // For example, [0,5) overlaps with [3,8)
    bool OverlapsWith(Range other);
};
```
Hình sau cho thấy một số phạm vi ví dụ:

<img width="338" alt="img-range" src="https://user-images.githubusercontent.com/42312796/170903589-085bfc52-2518-4b9b-be2c-adc3de934fec.png">

Lưu ý điểm kết thúc có thể trùng. Vì vậy A, B và C không chồng lên nhau, nhưng D thì phủ trên tất cả các đoạn.

Thử triển khai `OverlapsWith ()` để kiểm tra xem một trong hai điểm thuộc phạm vi này có nằm trong phạm vi khác hay không:
```c
bool Range::OverlapsWith(Range other) {
    // Check if 'begin' or 'end' falls inside 'other'.
    return (begin >= other.begin && begin <= other.end) ||
    (end >= other.begin && end <= other.end);
}
```
Mặc dù code chỉ dài hai dòng, nhưng có rất nhiều thứ đang diễn ra. Hình sau cho thấy tất cả các logic liên quan.
<img width="476" alt="img-logic" src="https://user-images.githubusercontent.com/42312796/170903431-9eb473de-ee7b-4a82-868a-0aca3a1b80e4.png">

Có rất nhiều trường hợp và điều kiện để suy nghĩ về việc dễ phát sinh bug. Nói đến đây thì xuất hiện một bug. Với code trước đó sẽ cho rằng phạm vi [0,2) chồng lên [2,4), nhưng trong thực tế thì không như vậy.

Vấn đề là bạn phải cẩn thận khi so sánh các giá trị `begin / end` bằng cách sử dụng `<=` hoặc chỉ dùng `<`. Đây là cách khắc phục việc này:
```c
return (begin >= other.begin && begin < other.end) ||
        (end > other.begin && end <= other.end);
```
Bây giờ nó đúng rồi phải không? Trên thực tế thì xuất hiện một lỗi khác. Đoạn code này đã bỏ qua trường hợp khi `begin / end` hoàn toàn bao quanh phạm vi khác.

Dưới đây cũng là một cách sửa lỗi trường hợp này:
```c
return (begin >= other.begin && begin < other.end) ||
        (end > other.begin && end <= other.end) ||
        (begin <= other.begin && end >= other.end);
```
Rất tiếc, đoạn code trên đã trở nên quá phức tạp. Bạn không thể mong đợi bất kỳ ai đọc đoạn code này và tự tin là nó chính xác. Vậy chúng ta phải làm gì? Làm thế nào chúng ta có thể chia nhỏ biểu thức khổng lồ này?

### Tìm cách tiếp cận đơn giản hơn

Đây là thời điểm bạn nên dừng lại và xem xét một cách tiếp cận hoàn toàn khác. Bắt đầu với một vấn đề đơn giản (kiểm tra xem hai phạm vi có chồng lên nhau hay không) đã trở thành một đoạn logic khá phức tạp. Đây thường là dấu hiệu cho thấy nên có một cách tiếp cận dễ dàng hơn.

Nhưng để tìm ra một giải pháp đơn giản hơn cần có sự sáng tạo. Bạn bắt tay vào việc này như thế nào? Một kỹ thuật dễ hiểu nếu bạn có thể giải quyết theo hướng ngược lại. Tuỳ thuộc vào trường hợp mà bạn gặp, điều này có nghĩa là lặp lại các mảng theo chiều ngược lại hoặc điền vào một số cấu trúc dữ liệu lùi hơn là tiến.

Trong trường hợp này, ngược lại với `OverlapsWith()` là "không chồng nhau". Việc xác định hai phạm vi không
chồng nhau hóa ra là một vấn đề đơn giản hơn nhiều, bởi vì chỉ có hai khả năng:

1. Phạm vi khác kết thúc trước khi phạm vi này bắt đầu.
2. Phạm vi khác bắt đầu sau khi phạm vi này kết thúc.

Chúng ta có thể dễ dàng viết thành đoạn code:
```c
bool Range::OverlapsWith(Range other) {
    if (other.end <= begin) return false; // They end before we begin
    if (other.begin >= end) return false; // They begin after we end
    return true; // Only possibility left: they overlap
}
```
Mỗi dòng code ở đây nhìn đơn giản hơn nhiều — nó chỉ liên quan đến phép so sánh duy nhất. Điều đó làm cho
người đọc có đủ sự tập trung suy nghĩ vào việc xem `<=` có đúng hay không.

### Phá vỡ những câu lệnh khổng lồ

Chương này nói về việc chia nhỏ các biểu thức riêng biệt, nhưng các kỹ thuật trên vẫn tốt khi áp dụng cho việc
phá vỡ những câu lệnh lớn. Ví dụ, có rất nhiều biểu thức cùng tham gia trong đoạn code JavaScript sau:
```js
var update_highlight = function (message_num) {
    if ($("#vote_value" + message_num).html() === "Up") {
    $("#thumbs_up" + message_num).addClass("highlighted");
    $("#thumbs_down" + message_num).removeClass("highlighted");
    } else if ($("#vote_value" + message_num).html() === "Down") {
    $("#thumbs_up" + message_num).removeClass("highlighted");
    $("#thumbs_down" + message_num).addClass("highlighted");
    } else {
    $("#thumbs_up" + message_num).removeClass("highighted");
    $("#thumbs_down" + message_num).removeClass("highlighted");
    }
};
```
Các biểu thức riêng biệt trong code này không quá lớn, nhưng khi đặt tất cả lại với nhau sẽ tạo thành một câu lệnh khổng lồ.

May mắn thay, rất nhiều biểu thức giống nhau, chúng ta có thể trích xuất chúng dưới dạng
các biến "summary variable" và khai báo ở đầu hàm (đây cũng là một ví dụ của nguyên tắc DRY — Không lặp lại chính bạn):
```js
var update_highlight = function (message_num) {
    var thumbs_up = $("#thumbs_up" + message_num);
    var thumbs_down = $("#thumbs_down" + message_num);
    var vote_value = $("#vote_value" + message_num).html();
    var hi = "highlighted";
    if (vote_value === "Up") {
    thumbs_up.addClass(hi);
    thumbs_down.removeClass(hi);
    } else if (vote_value === "Down") {
    thumbs_up.removeClass(hi);
    thumbs_down.addClass(hi);
    } else {
    thumbs_up.removeClass(hi);
    thumbs_down.removeClass(hi);
    }
};
```
Việc tạo `var hi = "highlighted"` không hoàn toàn cần thiết, nhưng vì nó đã có sáu bản sao,
có những lợi ích thuyết phục trong việc này:

- Giúp tránh những lỗi đánh máy. (Thực tế, bạn có nhận thấy trong ví dụ đầu tiên, chuỗi
  đã bị viết sai chính tả thành "highighted" trong trường thứ năm?)
- Thu gọn code hơn, làm cho code dễ nhìn hơn.
- Nếu tên lớp cần thay đổi, chỉ cần thay đổi nó ở một chỗ.

### Một cách sáng tạo khác để đơn giản hóa các biểu thức

Dưới đây là một ví dụ khác với nhiều tính toán trong mỗi biểu thức, lần này là trong C ++:
```c++
void AddStats(const Stats& add_from, Stats* add_to) {
    add_to->set_total_memory(add_from.total_memory() + add_to->total_memory());
    add_to->set_free_memory(add_from.free_memory() + add_to->free_memory());
    add_to->set_swap_memory(add_from.swap_memory() + add_to->swap_memory());
    add_to->set_status_string(add_from.status_string() + add_to->status_string());
    add_to->set_num_processes(add_from.num_processes() + add_to->num_processes());
    ...
}
```
Một lần nữa, mắt bạn phải đối mặt với đoạn mã dài và tương tự, nhưng không hoàn toàn giống nhau. Sau mười giây xem xét kỹ lưỡng, bạn có thể nhận ra rằng mỗi dòng đang hoạt động giống nhau, chỉ khác mỗi trường trong mỗi lần gọi:

    add_to->set_XXX(add_from.XXX() + add_to->XXX());

Trong C ++, chúng ta có thể xác định một macros (tập lệnh) để thực hiện:
```c++
void AddStats(const Stats& add_from, Stats* add_to) {
    #define ADD_FIELD(field) add_to->set_##field(add_from.field() + add_to->field())
    ADD_FIELD(total_memory);
    ADD_FIELD(free_memory);
    ADD_FIELD(swap_memory);
    ADD_FIELD(status_string);
    ADD_FIELD(num_processes);
    ...
    #undef ADD_FIELD
}
```
Bây giờ chúng ta đã loại bỏ tất cả sự lộn xộn, bạn có thể xem đoạn code và ngay lập tức hiểu được bản chất đoạn code đang thực hiện những gì. Rất rõ ràng, mỗi dòng code đều đang hoạt động giống nhau.

Lưu ý rằng chúng tôi không ủng hộ việc sử dụng macros thường xuyên — trên thực tế, chúng tôi thường tránh chúng
bởi vì chúng có thể làm cho mã khó hiểu và tạo ra các lỗi nhỏ. Nhưng đôi khi, như trong trường hợp này, chúng đơn giản và có thể mang lại lợi ích rõ ràng cho việc đọc hiểu.

### Tổng kết

Những biểu thức khổng lồ làm cho việc suy nghĩ trở nên khó khăn. Ở chương này đã chỉ ra một số cách để phá vỡ
chúng, giúp người đọc có thể dễ dàng hiểu được từng chút một.

Một kỹ thuật đơn giản là khai báo các biến “explaining variable” để chứa giá trị của một số biểu thức con lớn. Cách tiếp cận này có ba lợi ích:

- Chia nhỏ một biểu thức khổng lồ thành nhiều phần.
- Viết lại code bằng cách mô tả biểu thức con với một tên ngắn gọn.
- Giúp người đọc xác định các “khái niệm” chính trong code.

Một kỹ thuật khác là quản lý logic của bạn bằng cách sử dụng định luật của De Morgan — Đôi khi, kỹ thuật này có thể viết lại biểu thức boolean một cách rõ ràng hơn (ví dụ: `if (! (a &&! b))` chuyển thành `if (! a || b)`).

Chúng tôi đã đưa ra một ví dụ trong đó một điều kiện logic phức tạp được chia thành các câu lệnh như “if (a < b) ...”. Trên thực tế, tất cả các ví dụ code được cải tiến trong chương này đều có câu lệnh `if` với ít hơn hai giá trị bên trong chúng. Cấu trúc này là lý tưởng. Có thể không phải lúc nào cũng làm được điều này — đôi khi nó yêu cầu việc "phủ nhận" vấn đề hoặc xem xét việc đối lập với mục đích của bạn.

Cuối cùng, mặc dù chương này nói về việc chia nhỏ các biểu thức riêng lẻ, nhưng các kỹ thuật thường áp dụng cho các đoạn code lớn hơn. Vì vậy, hãy tích cực phá vỡ sự phức tạp logic bất cứ nơi nào bạn nhìn thấy nó.
