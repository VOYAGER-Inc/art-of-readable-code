# Chapter 11: One Task at a Time

![Image from 003_the_art_of_readable_code, page 133](https://user-images.githubusercontent.com/47113232/169859030-f94fc75e-acbb-4a52-8e3b-6119e7b05a81.png)

Code mà thực hiện nhiều thứ cùng 1 lúc thì sẽ khó hiểu hơn. Một khối code vừa có thể khởi tạo đối tượng mới, vừa xóa data, parsing input, và áp dụng logic nghiệp vụ, tất cả tại cùng 1 thời điểm. Nếu tất cả code đó được kết hợp với nhau, sẽ khó hiểu hơn là nếu mỗi “task” được bắt đầu và hoàn thành một mình.

> **KEY IDEA**
> 

**Code phải được sắp xếp sao cho code chỉ thực hiện một nhiệm vụ tại một thời điểm.**

Nói một cách khác, chương này nói về “defragmenting” code của bạn. Sơ đồ sau minh họa quá trình này: bên trái hiển thị các tác vụ khác nhau mà một đoạn code đang thực hiện và bên phải hiển thị cùng một đoạn code đó sau khi nó được sắp xếp để thực hiện một task tại một thời điểm.

![Image from 003_the_art_of_readable_code, page 134](https://user-images.githubusercontent.com/47113232/169859376-6a3a814b-2641-4533-8d9f-2e143d02a9a9.png)

Bạn có thể đã nghe lời khuyên rằng “functions should do only one thing.” Lời khuyên của chúng tôi là tương tự nhưng không phải lúc nào cũng nói về ranh giới của function. Chắc chắn, việc chia một function lớn thành nhiều functions nhỏ hơn có thể tốt. Nhưng ngay cả khi bạn không làm điều này, bạn vẫn có thể tổ chức code bên trong function lớn đó để cho nó thành các phần logic tách biệt nhỏ.

Đây là quy trình chúng tôi sử dụng để làm cho code thực hiện “one task at a time”:

1. Liệt kê tất cả các “tasks” mà code của bạn đang thực hiện. Chúng tôi sử dụng từ “tasks” rất lỏng lẻo — nó có thể nhỏ như “đảm bảo rằng đối tượng này hợp lệ” hoặc mơ hồ như “lặp lại qua mọi nút trong cây”.
2. Cố gắng tách các tasks đó nhiều nhất có thể thành các functions khác nhau hoặc ít nhất là thành các phần logic nhỏ trong code

Trong chương này, chúng tôi sẽ chỉ cho bạn một số ví dụ về cách thực hiện việc này.

## Tasks Can Be Small

Giả sử có 1 voting widget trên 1 cái blog, là nơi người dùng có thể vote cho 1 comment “Up” hoặc “Down”. Tổng số điểm của 1 comment được tính bằng cách cộng tất cả lượt vote lại: +1 cho mỗi “Up” vote, -1 cho mỗi “Down” vote.

Dưới đây là 3 trạng thái vote chính của user, và nó sẽ ảnh hưởng như thế nào đến tổng điểm:

![Image from 003_the_art_of_readable_code, page 135](https://user-images.githubusercontent.com/47113232/169859916-318e702b-d361-49b2-a7f3-b663a8bf7af8.png)

Khi người dùng nhấp vào một trong các nút trên (để thực hiện thay đổi phiếu bầu của họ), JavaScript sau được gọi:

```jsx
vote_changed(old_vote, new_vote); // each vote is "Up", "Down", or ""
```

Function này dùng để cập nhật tổng điểm và hoạt động cho tất cả các sự kết hợp của old_vote/new_vote:

```jsx
var vote_changed = function (old_vote, new_vote) {
  var score = get_score();

  if (new_vote !== old_vote) {
    if (new_vote === 'Up') {
      score += (old_vote === 'Down' ? 2 : 1);
    } else if (new_vote === 'Down') {
      score -= (old_vote === 'Up' ? 2 : 1);
    } else if (new_vote === '') {
      score += (old_vote === 'Up' ? -1 : 1);
    }
  }

  set_score(score);
};
```

Mặc dù code khá ngắn, nhưng nó đang làm rất nhiều điều. Có rất nhiều chi tiết phức tạp và khó có thể biết ngay được liệu có bất kỳ lỗi sai sót, lỗi chính tả hay lỗi nào khác hay không.

Đoạn code có vẻ như chỉ làm một việc (cập nhật điểm số), nhưng thực tế có hai tasks được thực hiện cùng một lúc:

1. old_vote và new_vote đang được “parsed” thành 1 giá trị số.
2. Cập nhật điểm

Chúng tôi có thể làm cho code dễ đọc hơn bằng cách giải quyết từng task riêng biệt. Đoạn code sau giải quyết task đầu tiên, parsing the vote thành 1 giá trị số
