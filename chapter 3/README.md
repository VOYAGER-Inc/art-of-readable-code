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

## Ưu tiên tối thiểu và tối đa cho các giới hạn

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

