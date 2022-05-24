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

Chúng tôi có thể làm cho code dễ đọc hơn bằng cách giải quyết từng task riêng biệt. Đoạn code sau giải quyết task đầu tiên, parsing the vote thành 1 giá trị số:

```jsx
var vote_value = function (vote) {
  if (vote === 'Up') {
    return +1;
  }
  if (vote === 'Down') {
    return -1;
  }
  return 0;
};
```

Và bây giờ phần còn lại của code có thể giải quyết task thứ hai, là cập nhật điểm:

```jsx
var vote_changed = function (old_vote, new_vote) {
  var score = get_score();

  score -= vote_value(old_vote); // remove the old vote
  score += vote_value(new_vote); // add the new vote

  set_score(score);
}
```

Như bạn có thể thấy, với phiên bản code này thì nó giúp cho chúng ta dễ hiểu hơn, giảm bớt sự suy nghĩ của chúng ta về logic của code. Đây chính là một phần quan trọng trong việc làm cho code “easy to understand.”

## Extracting Values from an Object
Chúng tôi có code JavaScript dùng để format location của user thành một chuỗi String thân thiện với format như sau “City, Country”. Ví dụ như là “Santa Monica, USA” hoặc “Paris, France.” Chúng tôi đã được cung cấp một từ điển location_info với nhiều thông tin có cấu trúc. Tất cả những gì chúng tôi phải làm là chọn “City” và “Country” từ tất cả các fields và nối chúng lại với nhau.

![Image from 003_the_art_of_readable_code, page 136](https://user-images.githubusercontent.com/47113232/170071190-ef2e8e7d-e1d3-4b49-9f4f-116b4d2e864f.png)

Cho đến nay, nó có vẻ dễ dàng, nhưng phần khó khăn là bất kỳ hoặc tất cả bốn giá trị này có thể bị thiếu. Đây là cách chúng tôi giải quyết vấn đề đó:

- Khi chọn “City,” chúng tôi ưu tiên sử dụng “LocalityName” (city/town) nếu nó có, sau đó mới tới “SubAdministrativeAreaName” (larger city/county), và cuối cùng mới là “AdministrativeAreaName” (state/territory).
- Nếu cả ba đều bị thiếu thì “City” sẽ được gán giá trị default là “Middle-of-Nowhere”.
- Nếu “CountryName” bị thiếu thì nó sẽ được gán giá trị default là “Planet Earth”.

Hình sau đây cho thấy hai ví dụ về việc xử lý các giá trị bị thiếu.

![Image from 003_the_art_of_readable_code, page 137](https://user-images.githubusercontent.com/47113232/170071469-f5a9bc29-4426-4a44-b63a-d340a288a92c.png)

Đây là code mà chúng tôi đã viết để thực hiện task này:

```jsx
var place = location_info["LocalityName"]; // e.g. "Santa Monica"
if (!place) {
  place = location_info["SubAdministrativeAreaName"]; // e.g. "Los Angeles"
}
if (!place) {
  place = location_info["AdministrativeAreaName"]; // e.g. "California"
}
if (!place) {
  place = "Middle-of-Nowhere";
}
if (location_info["CountryName"]) {
  place += ", " + location_info["CountryName"]; // e.g. "USA"
} else {
  place += ", Planet Earth";
}

return place;
```

Đoạn code nhìn hơi lộn xộn, nhưng nó đã hoàn thành đúng công việc của mình.

Nhưng vài ngày sau, chúng tôi cần cải thiện chức năng: đối với các locations ở United States, ví dụ như chúng tôi muốn hiển thị state thay thế cho country (nếu có thể). Vì vậy, thay vì "Santa Monica, USA", nó sẽ trả về "Santa Monica, California.”

Thêm tính năng này vào code trước đó sẽ làm cho nó xấu hơn nhiều.

## Applying “One Task at a Time”
