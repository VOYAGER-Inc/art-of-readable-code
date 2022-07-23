# Chapter 1: Code Should Be Easy to Understand
![image_01](https://user-images.githubusercontent.com/72190013/180602640-98826a04-2e3d-4a12-8c94-761e4e63fde7.png)

Trong năm năm qua, chúng tôi đã thu thập hàng trăm ví dụ về “code xấu” (phần lớn là của chúng tôi) và phân tích điều gì khiến nó trở nên tồi tệ và những nguyên tắc / kỹ thuật nào được sử dụng để làm cho nó tốt hơn. Điều chúng tôi nhận thấy là tất cả các nguyên tắc đều bắt nguồn từ một chủ đề duy nhất.

## KEY IDEA
  **Code nên dễ hiểu**

Chúng tôi tin rằng đây là nguyên tắc hướng dẫn quan trọng nhất mà bạn có thể sử dụng khi quyết định cách viết code của mình. Trong suốt cuốn sách, chúng tôi sẽ chỉ ra cách áp dụng nguyên tắc này cho các khía cạnh khác nhau của việc viết code hàng ngày của bạn. Nhưng trước khi bắt đầu, chúng tôi sẽ giải thích kỹ hơn về nguyên tắc này và giải thích lý do tại sao nó lại quan trọng như vậy.

## What Makes Code “Better”?

Hầu hết các lập trình viên (bao gồm cả các tác giả) đưa ra quyết định lập trình dựa trên cảm giác và trực giác. Tất cả chúng ta đều biết code như thế này:

```
for (Node* node = list->head; node != NULL; node = node->next)
  Print(node->data)
```

Code sẽ tốt hơn nếu viết lại như thế này

```
Node* node = list->head;
if (node == NULL) return;
while (node->next != NULL) {
  Print(node->data);
  node = node->next;
}
if (node != NULL) Print(node->data);
```

Mặc dù cả hai ví dụ hoạt động hoàn toàn giống nhau.
Nhưng rất nhiều lần, đó lại là một lựa chọn khó khăn hơn. Ví dụ:

```
return exponent >= 0 ? mantissa * (1 << exponent) : mantissa / (1 << -exponent);
```

tốt hơn hoặc tệ hơn:

```
if (exponent >= 0) {
  return mantissa * (1 << exponent);
} else {
  return mantissa / (1 << -exponent);
}
```

Đoạn code đầu tiên nhỏ gọn hơn, nhưng đoạn code thứ hai ít đáng sợ hơn, giúp người đọc code dễ hiểu hơn.
Tiêu chí nào quan trọng hơn? Nói chung, làm thế nào để bạn đưa ra quyết định dùng cách nào để viết code?

## The Fundamental Theorem of Readability (Định lý cơ bản về đọc code)

Sau khi nghiên cứu nhiều ví dụ code như thế này, chúng tôi đã đi đến kết luận rằng có một số liệu về khả năng đọc quan trọng hơn bất kỳ số liệu nào khác.
Điều quan trọng đến mức chúng tôi gọi đó là “Định lý cơ bản về khả năng đọc”.

**KEY IDEA.**

  **Code phải được viết, để giảm thiểu thời gian cho người khác hiểu nó.**

Ý chúng tôi là gì? Theo nghĩa đen, nếu bạn chọn một đồng nghiệp điển hình của mình và đo lường thời gian anh ta đọc qua code của bạn và hiểu nó, thì “thời gian cho đến khi hiểu” này là số liệu lý thuyết mà bạn muốn giảm thiểu.

Và khi chúng ta nói "hiểu", chúng ta có một điểm rất cao cho từ này. Để ai đó hiểu đầy đủ về code của bạn, họ sẽ có thể thực hiện các thay đổi đối với code, phát hiện lỗi và hiểu cách nó tương tác với phần còn lại của code của bạn.

Bây giờ, bạn có thể đang nghĩ, Ai quan tâm nếu người khác có thể hiểu nó? Tôi là người duy nhất sử dụng code này! Ngay cả khi bạn đang tham gia dự án một người, bạn cũng nên theo đuổi mục tiêu này.
“Người khác” đó có thể là bạn sáu tháng sau, khi code của chính bạn trông không quen thuộc với bạn. Và bạn không bao giờ biết — ai đó có thể tham gia dự án của bạn hoặc “code bỏ đi” của bạn có thể được sử dụng lại cho một dự án khác.

## Is Smaller Always Better?

Nói chung, bạn viết càng ít code để giải quyết vấn đề thì càng tốt (xem Chương 13, Writing Less Code). Có thể mất ít thời gian hơn để hiểu Class 2000 dòng so với class 5000 dòng.

Nhưng ít dòng hơn không phải lúc nào cũng tốt hơn! Có rất nhiều lần khi biểu thức một dòng như:

```
 assert((!(bucket = FindBucket(key))) || !bucket->IsOccupied());
```

Mất nhiều thời gian để hiểu hơn nếu nó là hai dòng:

```
 bucket = FindBucket(key);
if (bucket != NULL) assert(!bucket->IsOccupied());
```

Tương tự, một nhận xét có thể giúp bạn hiểu code nhanh hơn, ví dụ:

```
// Fast version of "hash = (65599 * hash) + c"
hash = (hash << 6) + (hash << 16) - hash + c;
```

**Vì vậy, mặc dù có ít dòng code hơn là một mục tiêu tốt, nhưng giảm thiểu thời gian cho đến khi hiểu được là một mục tiêu tốt hơn.**

## Does Time-Till-Understanding Conflict with Other Goals?

Bạn có thể đang nghĩ, Còn những ràng buộc khác, chẳng hạn như làm cho code hiệu quả, hoặc được kiến trúc tốt, hoặc dễ kiểm tra, v.v.? Đôi khi những điều này có mâu thuẫn với việc muốn làm cho code dễ hiểu không?

Chúng tôi nhận thấy rằng những mục tiêu khác này không ảnh hưởng nhiều. Ngay cả trong lĩnh vực code được tối ưu hóa cao, vẫn có nhiều cách để làm cho nó cũng có thể đọc được. Và làm cho code của bạn dễ hiểu thường dẫn đến code được cấu trúc tốt và dễ kiểm tra.

Phần còn lại của cuốn sách thảo luận về cách áp dụng “dễ đọc” trong các trường hợp khác nhau. Nhưng hãy nhớ rằng, khi còn nghi ngờ, Định lý Cơ bản về Khả năng Đọc sẽ vượt trội hơn bất kỳ quy tắc hoặc nguyên tắc nào khác trong cuốn sách này.

Ngoài ra, một số lập trình viên có nhu cầu bắt buộc phải sửa bất kỳ code nào không được kiểm chứng hoàn hảo. Điều quan trọng là phải lùi lại và hỏi, code này có dễ hiểu không? Nếu vậy, có lẽ bạn nên chuyển sang code khác.

## The Hard Part

Vâng, nó đòi hỏi phải làm việc thêm để liên tục suy nghĩ về việc liệu một người ngoài tưởng tượng có thấy code của bạn dễ hiểu hay không. Làm như vậy đòi hỏi phải bật một phần bộ não của bạn mà trước đây có thể không được bật trong khi viết code.

Nhưng nếu bạn áp dụng mục tiêu này (như chúng tôi đã có), chúng tôi chắc chắn rằng bạn sẽ trở thành một lập trình viên giỏi hơn, ít lỗi hơn, tự hào hơn trong công việc của mình và tạo ra code mà mọi người xung quanh bạn sẽ thích sử dụng. Vậy hãy bắt đầu!
