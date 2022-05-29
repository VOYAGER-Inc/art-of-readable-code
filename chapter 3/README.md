# Chapter 3: Names That Can’t Be Misconstrued

 ![image](https://user-images.githubusercontent.com/52561098/170833361-ea9c8665-41ad-418f-ab31-9568b58d986c.png)
 
Trong chương trước, chúng tôi đã trình bày cách đặt nhiều thông tin vào tên của bạn. Trong chương này, chúng tôi tập trung vào một chủ đề khác: _"Đề phòng những cái tên có thể bị hiểu nhầm"_.
   
Ý TƯỞNG CHÍNH
>**Tích cực xem xét kỹ lưỡng tên của bạn bằng cách tự hỏi bản thân, "Ai đó có thể diễn giải những ý nghĩa nào khác từ cái tên này ?"**
   
Hãy thực sự cố gắng sáng tạo ở đây, tích cực tìm kiếm “cách hiểu sai”. Bước này sẽ giúp bạn phát hiện những cái tên không rõ ràng, để từ đó bạn có thể thay đổi chúng.

Đối với các ví dụ trong chương này, chúng ta sẽ "suy nghĩ thật lớn" khi chúng ta thảo luận về những cách hiểu sai của từng tên mà chúng ta phát hiện được và sau đó chọn những tên tốt hơn.

## Ví dụ 1: Filter()

Giả sử bạn đang viết code để lấy ra một tập hợp các kết quả từ cơ sở dữ liệu:

```python
results = Database.all_objects.filter("year <= 2011")
```

Kết quả bây giờ chứa nội dung gì ?

• Đối tượng có năm <= 2011 ?

• Đối tượng có năm không <= 2011 ?

Vấn đề là ```filter``` là một từ rất mơ hồ, chúng ta sẽ không xác định được ý nghĩa của nó là _“lấy ra”_ hay _“loại bỏ”_. Vì vậy, tốt nhất nên tránh sử dụng tên ```filter``` vì nó rất dễ bị hiểu sai.

Nếu bạn muốn _“lấy ra”_, 1 tên tốt hơn có thể sử dụng là ```select```, hoặc ```exclude``` nếu bạn muốn _"loại bỏ"_

## Ví dụ 2: Clip(text, length)

Giả sử bạn có hàm cắt bớt nội dung của một đoạn văn:

```python

# Cắt bỏ phần cuối của văn bản và thay bằng "..."

def clip(text, length):
    ...
```

Có hai cách mà bạn có thể hình dung cách hoạt động của ```Clip```:

1. Loại bỏ ```length``` độ dài ký tự tính từ cuối đoạn văn
2. Cắt ngắn đoạn văn đến độ dài tối đa là ```length```

Cách thứ hai (cắt ngắn) rất có thể xảy ra, nhưng cũng không chắc chắn được. Cho nên, thay vì để lại cho người đọc bất kỳ nghi vấn nào, tốt hơn là bạn nên đặt tên cho hàm là ```truncate(text, length)```.

Tuy nhiên, tham số tên là ```length``` cũng là một điều đáng trách. Nếu tham số được đặt là ```max_length```, nó sẽ được rõ nghĩa hơn.

Nhưng câu chuyện vẫn chưa dừng lại, cái tên ```max_length``` vẫn để lại nhiều cách hiểu:
1. Số lượng bytes
2. Số lượng ký tự
3. Số lượng từ

Như bạn đã thấy trong chương trước, đây là trường hợp các đơn vị nên được gắn vào tên. Đối với trường hợp này, ý nghĩa sẽ là "số lượng ký tự", vì vậy thay vì dùng ```max_length```, chúng ta có thể thay thế bằng ```max_chars```.

## Ưu tiên **min** và **max** cho các giới hạn _Inclusive_

Giả sử ứng dụng giỏ hàng của bạn cần ngăn mọi người mua nhiều hơn 10 mặt hàng cùng một lúc:

```python
CART_TOO_BIG_LIMIT = 10
if shopping_cart.num_items() >= CART_TOO_BIG_LIMIT:
    Error("Too many items in cart.")
```
Code này có một lỗi [**off-by-one**](https://vi.wikipedia.org/wiki/L%E1%BB%97i_Off-by-one) cổ điển. Chúng ta có thể sửa chữa nó bằng cách thay >= thành > :

```python
if shopping_cart.num_items() > CART_TOO_BIG_LIMIT:
```
(hoặc bằng cách xác định lại ```CART_TOO_BIG_LIMIT``` thành 11). Nhưng vấn đề chính là ```CART_TOO_BIG_LIMIT``` là một cái tên không được rõ ràng, không rõ ý nghĩa của nó là “lớn hơn” hay “lớn hơn hoặc bằng”.

LỜI KHUYÊN
>Cách rõ ràng nhất để đặt tên cho một "giới hạn" là thêm ```max_``` hoặc ```min_``` trước đối tượng được giới hạn.

Trong trường hợp này, tên phải là ```MAX_ITEMS_IN_CART```. Code mới đơn giản và rõ ràng:

```python
MAX_ITEMS_IN_CART = 10
if shopping_cart.num_items() > MAX_ITEMS_IN_CART:
   Error("Too many items in cart.")
```
## Ưu tiên first và last cho Phạm vi _Inclusive_
<img width="247" src="https://user-images.githubusercontent.com/52561098/170853636-14755597-eb1d-46ac-94e5-cea83eb73eb4.png">

Đây là một ví dụ khác mà bạn không thể biết đó là “lớn hơn” hay "lớn hơn hoặc bằng":
```python
print integer_range(start=2, stop=4)
# Does this print [2,3] or [2,3,4] (or something else)?
```
Mặc dù ```start``` là một tên tham số hợp lý, nhưng ```stop``` có thể được hiểu theo nhiều cách ở đây.

Đối với các phạm vi _bao gồm_ như thế này (trong đó phạm vi phải bao gồm cả hai điểm đầu-cuối), một lựa chọn tốt là ```first```/```last```. Ví dụ:
```python
set.PrintKeys(first="Bart", last="Maggie")
```
Không giống với ```stop```, từ ```last``` có ý nghĩa _bao hàm_ rõ ràng hơn.

Ngoài ```first```/```last```, tên ```min```/```max``` cũng có thể sử dụng cho các phạm vi _bao gồm_, giả sử chúng "nghe đúng" trong ngữ cảnh đó.

## Ưu tiên begin và end cho phạm vi _Inclusive_ / _Exclusive_
<img width="267" src="https://user-images.githubusercontent.com/52561098/170854128-9e14ffd1-ce31-4ade-b7f4-4d5235350434.png">

Trên thực tế, việc sử dụng các phạm vi Inclusive / Exclusive thường thuận tiện hơn. Ví dụ: nếu bạn muốn in tất cả các sự kiện đã xảy ra vào ngày 16 tháng 10, sẽ dễ dàng hơn nếu viết:
```python
PrintEventsInRange("OCT 16 12:00am", "OCT 17 12:00am")
```
hơn là:
```python
PrintEventsInRange("OCT 16 12:00am", "OCT 16 11:59:59.9999pm")
```
Vậy, một cặp tên tốt cho các tham số là gì? Tốt, quy ước lập trình điển hình để đặt tên cho một phạm vi _inclusive_/_exclusive_ là ```begin```/```end```.

Nhưng từ ```end``` lại có một chút mơ hồ. Ví dụ, trong câu “I’m at the end of the book”, từ ```end``` ở đây là bao gồm. Thật tiếc là trong tiếng Anh không có từ ngắn gọn cho "vượt quá giá trị cuối cùng".

Bởi vì ```begin```/```end``` có tính thành ngữ (ít nhất, nó được sử dụng theo cách này trong thư viện chuẩn cho C++ và hầu hết những nơi mà một mảng cần được "cắt" theo cách này), nó là lựa chọn tốt nhất.

## Đặt tên cho Boolean

Khi chọn tên cho một biến boolean hoặc một hàm trả về giá trị boolean, hãy đảm bảo rằng _true_ và _false_ thực sự có nghĩa là gì. Đây là một ví dụ nguy hiểm:

```c#
bool read_password = true;
```
Tùy thuộc vào cách bạn đọc nó (không có ý định chơi chữ), có hai cách hiểu rất khác nhau:
1. Chúng ta cần xem mật khẩu
2. Mật khẩu đã được xem

Trong trường hợp này, tốt nhất bạn nên tránh đặt tên là _read_ và thay vào đó là _need_password_ hoặc _user_is_authenticated_.

Nói chung, việc thêm các từ như ```is```, ```has```, ```can```, hoặc ```should``` giúp cho các **boolean** trở nên rõ nghĩa hơn.

Ví dụ: một hàm có tên _SpaceLeft()_ có vẻ như sẽ trả về một số. Nếu nó trả về boolean, một cái tên tốt hơn sẽ là _HasSpaceLeft()_.

Cuối cùng, tốt nhất là tránh các thuật ngữ mang tính **phủ định** ở trong tên. Ví dụ, thay vì:
```c#
bool disable_ssl = false;
```
sẽ dễ đọc hơn (và cô đọng hơn) nếu dùng:
```c#
bool use_ssl = true;
```

## Phù hợp với kỳ vọng của người dùng

Một số tên gây hiểu lầm vì người dùng đã có suy diễn trước về ý nghĩa của cái tên, cho dù bạn có ý nghĩa khác. Trong những trường hợp này, tốt nhất bạn chỉ nên “nhượng bộ” và thay đổi tên khác để nó không gây hiểu lầm.

### Ví dụ 1: get*()
Nhiều lập trình viên đã quen với quy ước rằng các phương thức bắt đầu bằng ```get``` là "bộ truy cập nhẹ" chỉ trả về một thành phần nội bộ. Đi ngược lại quy ước này có thể gây hiểu lầm cho những người dùng đó.

Dưới đây là một ví dụ, về những việc không nên làm trong Java:
```java
public class StatisticsCollector {
  public void addSample(double x) { ... }
  
  public double getMean() {
    // Iterate through all samples and return total / num_samples
  }
  ...
}
```
Trong trường hợp này, việc triển khai _getMean()_ là lặp lại dữ liệu trong quá khứ và tính giá trị trung bình một cách nhanh chóng. Bước này có thể rất tốn kém nếu có nhiều dữ liệu! Nhưng một lập trình viên không nghi ngờ có thể gọi _getMean()_ một cách bất cẩn, cho rằng đó là một lệnh gọi tốn ít chi phí.

Thay vào đó, hàm nên được đổi tên thành một cái gì đó như computeMean(), nghe giống như một hoạt động tốn kém hơn. (Ngoài ra, nó nên được cấu trúc lại để thực sự là một hoạt động ít tốn chi phí hơn.)

### Ví dụ 2: list::size()

Dưới đây là một ví dụ từ Thư viện chuẩn C ++. Đoạn mã sau là nguyên nhân gây ra lỗi rất khó tìm khiến một trong các máy chủ của chúng tôi bị chậm khi thu thập thông tin:

```c++
void ShrinkList(list<Node>& list, int max_size) {
    while (list.size() > max_size) {
      FreeNode(list.back());
      list.pop_back();
    }
}
```
"bug" ở đây là tác giả không biết rằng ```list.size()``` có độ phức tạp _O(n)_, nó đếm thông qua từng node của danh sách liên kết, thay vì chỉ trả về một số lượng được tính toán trước, điều này làm cho ```ShrinkList()``` trở thành một hoạt động có độ phức tạp O(n2).

Về mặt kỹ thuật, Code này chạy đúng và trên thực tế đã vượt qua tất cả các _unit test_ của chúng tôi. Nhưng khi ```ShrinkList()``` được gọi trong danh sách có *một triệu phần tử*, phải mất hơn một giờ đồng hồ mới kết thúc!

Có thể bạn đang nghĩ “Đó là lỗi của người gọi — lẽ ra người đó nên đọc tài liệu cẩn thận hơn”. Đúng là như vậy, nhưng trong trường hợp này, thực tế đáng ngạc nhiên ```list.size()``` không phải là một phép toán có thời gian cố định. Tất cả các containers trong C++ đều có một phương thức size() có thời gian thực thi không đổi.

Nếu size() được đặt tên là ```countSize()``` hoặc ```countElements()```, thì lỗi tương tự sẽ ít xảy ra hơn. Các tác giả của Thư viện chuẩn C++ có lẽ muốn đặt tên cho phương thức là size() để khớp với tất cả các containers khác như _vector_ và _map_. Nhưng bởi vì họ đã làm như vậy, nên các lập trình viên dễ dàng nhầm lẫn đó là một hoạt động nhanh, như cách nó diễn ra đối với các container khác. Thật may, tiêu chuẩn C++ mới nhất hiện yêu cầu size() phải là O(1).
