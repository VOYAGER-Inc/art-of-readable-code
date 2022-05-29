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

## Nhiệm vụ có thể nhỏ

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

## Trích xuất giá trị từ một đối tượng
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

## Áp dụng “Một nhiệm vụ tại một thời điểm”
Thay vì bẻ cong code này theo ý muốn của chúng tôi, chúng tôi đã dừng lại và nhận ra rằng nó đã thực hiện nhiều tasks cùng một lúc:
1. Trích xuất giá trị từ location_info
2. Thực hiện một thứ tự ưu tiên cho "City", và nếu không tìm thấy “City” nào thì sẽ lấy giá trị default là “Middle-of-Nowhere”
3. Lấy “Country”, và nếu không có “Country” thì sẽ lấy giá trị default là “Planet Earth”
4. Cập nhật place

Vì vậy, chúng tôi đã viết lại code này để giải quyết từng tasks này một cách độc lập.
Nhiệm vụ đầu tiên (trích xuất các giá trị từ location_info)

```jsx
  var town = location_info["LocalityName"]; // e.g. "Santa Monica"
  var city = location_info["SubAdministrativeAreaName"]; // e.g. "Los Angeles"
  var state = location_info["AdministrativeAreaName"]; // e.g. "California"
  var country = location_info["CountryName"]; // e.g. "USA"
```

Tại thời điểm này, chúng tôi đã sử dụng xong location_info và không phải nhớ những keys dài dòng và không trực quan đó. Thay vào đó, chúng tôi đã có bốn biến đơn giản hơn để làm việc.

Tiếp theo, chúng tôi phải tìm ra "second half" của giá trị trả về sẽ là gì:
```jsx
// Bắt đầu với giá trị mặc định và tiếp tục ghi đè bằng giá trị cụ thể nhất.
var second_half = "Planet Earth";
if (country) {
  second_half = country;
}
if (state && country === "USA") {
  second_half = state;
}
```

Tương tự, chúng tôi có thể tìm ra “first half”:
```jsx
var first_half = "Middle-of-Nowhere";
if (state && country !== "USA") {
  first_half = state;
}
if (city) {
  first_half = city;
}
if (town) {
  first_half = town;
}
```

Cuối cùng, chúng tôi kết hợp thông tin với nhau:
```jsx
 return first_half + ", " + second_half;
```

Hình minh họa về “defragmentation” ở đầu chương đã thể hiện giải pháp này.

Đây là hình minh họa tương tự, với nhiều chi tiết hơn được điền vào:

![Image from 003_the_art_of_readable_code, page 139](https://user-images.githubusercontent.com/47113232/170294392-d1a35a10-4f51-4e28-9486-0d9db0a549ed.png)

Như bạn có thể thấy, bốn tasks trong giải pháp thứ hai đã được phân mảnh thành các vùng riêng biệt.

### Cách tiếp cận khác
Khi refactor code, thường có nhiều cách để thực hiện, và trường hợp này cũng không ngoại lệ. Khi bạn đã tách ra thành một số tasks riêng biệt, code sẽ dễ nghĩ hơn và bạn có thể nghĩ ra nhiều cách tốt hơn để refactor lại nó.

Ví dụ, chuỗi câu lệnh if trước đó yêu cầu bạn đọc cẩn thận để biết mọi trường hợp có hoạt động chính xác hay không. Trên thực tế, có hai tasks con đang diễn ra đồng thời trong đoạn code đó:

1. Xem qua danh sách các biến và chọn biến ưu tiên nhất có sẵn.
2. Sử dụng một danh sách khác, tùy thuộc vào quốc gia có phải là “USA” hay không.

Nhìn lại, bạn có thể thấy rằng đoạn mã trước đó có logic “if USA” đan xen với phần logic còn lại. Thay vào đó, chúng tôi có thể xử lý các trường hợp USA và không phải USA riêng biệt:

```jsx
var first_half, second_half;

if (country === "USA") {
  first_half = town || city || "Middle-of-Nowhere";
  second_half = state || "USA";
} else {
  first_half = town || city || state || "Middle-of-Nowhere";
  second_half = country || "Planet Earth";
}

return first_half + ", " + second_half;
```

Trong trường hợp bạn không quen với JavaScript, a || b || c là thành ngữ và được đánh giá là giá trị “true” đầu tiên (trong trường hợp này, là một chuỗi không có giá trị xác định). code này có lợi ích là rất dễ dàng kiểm tra danh sách ưu tiên và cập nhật nó. Hầu hết các câu lệnh if đã bị loại bỏ và logic nghiệp vụ được thể hiện bằng ít dòng code hơn.

### Một ví dụ lớn hơn
Trong hệ thống thu thập dữ liệu web mà chúng tôi đã xây dựng, một hàm có tên UpdateCounts () được gọi để tăng các thống kê khác nhau sau khi mỗi trang web được tải xuống:

```cpp
void UpdateCounts(HttpDownload hd) {
  counts["Exit State" ][hd.exit_state()]++; // e.g. "SUCCESS" or "FAILURE"
  counts["Http Response"][hd.http_response()]++; // e.g. "404 NOT FOUND"
  counts["Content-Type" ][hd.content_type()]++; // e.g. "text/html"
}
```

Chà, đó là cách chúng tôi ước mã trông như thế nào!

Trên thực tế, đối tượng HttpDownload không có phương thức nào được hiển thị ở đây. Thay vào đó, HttpDownload là một lớp rất lớn và phức tạp, với nhiều lớp lồng nhau và chúng tôi phải tự tìm ra các giá trị đó. Để làm cho vấn đề tồi tệ hơn, đôi khi những giá trị đó bị thiếu hoàn toàn — trong trường hợp đó, chúng tôi chỉ sử dụng "unknown" làm giá trị mặc định.

Vì tất cả những điều này, mã thực sự khá lộn xộn:
```cpp
// WARNING: DO NOT STARE DIRECTLY AT THIS CODE FOR EXTENDED PERIODS OF TIME.
void UpdateCounts(HttpDownload hd) {
  // Figure out the Exit State, if available.
  if (!hd.has_event_log() || !hd.event_log().has_exit_state()) {
    counts["Exit State"]["unknown"]++;
  } else {
    string state_str = ExitStateTypeName(hd.event_log().exit_state());
    counts["Exit State"][state_str]++;
  }

  // If there are no HTTP headers at all, use "unknown" for the remaining elements.
  if (!hd.has_http_headers()) {
    counts["Http Response"]["unknown"]++;
    counts["Content-Type"]["unknown"]++;
    return;
  }

  HttpHeaders headers = hd.http_headers();

  // Log the HTTP response, if known, otherwise log "unknown"
  if (!headers.has_response_code()) {
    counts["Http Response"]["unknown"]++;
  } else {
    string code = StringPrintf("%d", headers.response_code());
    counts["Http Response"][code]++;
  }

  // Log the Content-Type if known, otherwise log "unknown"
  if (!headers.has_content_type()) {
    counts["Content-Type"]["unknown"]++;
  } else {
    string content_type = ContentTypeMime(headers.content_type());
    counts["Content-Type"][content_type]++;
  }
}
```

Như bạn có thể thấy, có rất nhiều code, rất nhiều logic và thậm chí là một vài dòng code lặp đi lặp lại. Code này không thú vị để đọc.

Đặc biệt, code này chuyển đổi qua lại giữa các tasks khác nhau. Dưới đây là các tasks khác nhau được xen kẽ trong toàn bộ code:

1. Sử dụng "unknown" làm giá trị mặc định cho mỗi khóa
2. Phát hiện xem có thiếu thành viên của HttpDownload hay không
3. Trích xuất giá trị và chuyển đổi nó thành string
4. Cập nhật counts[]

Chúng tôi có thể cải thiện code bằng cách tách một số tasks này thành các vùng riêng biệt trong code:
```cpp
void UpdateCounts(HttpDownload hd) {
  // Task: define default values for each of the values we want to extract
  string exit_state = "unknown";
  string http_response = "unknown";
  string content_type = "unknown";

  // Task: try to extract each value from HttpDownload, one by one
  if (hd.has_event_log() && hd.event_log().has_exit_state()) {
    exit_state = ExitStateTypeName(hd.event_log().exit_state());
  }
  if (hd.has_http_headers() && hd.http_headers().has_response_code()) {
    http_response = StringPrintf("%d", hd.http_headers().response_code());
  }
  if (hd.has_http_headers() && hd.http_headers().has_content_type()) {
    content_type = ContentTypeMime(hd.http_headers().content_type());
  }

  // Task: update counts[]
  counts["Exit State"][exit_state]++;
  counts["Http Response"][http_response]++;
  counts["Content-Type"][content_type]++;
}
```

Như bạn có thể thấy, code có ba vùng riêng biệt với các mục đích sau:
1. Khai báo giá trị mặc định cho ba khóa mà chúng tôi quan tâm.
2. Trích xuất các giá trị, nếu có, cho mỗi khóa này và chuyển đổi chúng thành string.
3. Cập nhật counts[]cho mỗi key / value.

Điều tốt ở những vùng này là chúng tách biệt với nhau — trong khi bạn đang đọc một vùng, bạn không cần phải nghĩ về các vùng khác.

Lưu ý rằng mặc dù chúng tôi đã liệt kê bốn tasks, nhưng chúng tôi chỉ có thể tách biệt ba trong số chúng. Điều đó hoàn toàn ổn: những tasks bạn liệt kê ban đầu chỉ là điểm khởi đầu. Ngay cả việc tách một số trong số chúng cũng có thể giúp ích rất nhiều cho mọi thứ, như nó đã làm ở đây.

### Cải tiến hơn nữa
Phiên bản code mới này là một cải tiến rõ rệt so với phiên bản quái dị ban đầu. Và lưu ý rằng chúng tôi thậm chí không phải tạo các chức năng khác để thực hiện việc dọn dẹp này. Như chúng tôi đã đề cập trước đây, ý tưởng về “một nhiệm vụ tại một thời điểm” có thể giúp bạn làm sạch code bất kể ranh giới chức năng.

Tuy nhiên, chúng tôi cũng có thể cải thiện code này theo cách khác, bằng cách giới thiệu ba helper functions:
```cpp
void UpdateCounts(HttpDownload hd) {
  counts["Exit State"][ExitState(hd)]++;
  counts["Http Response"][HttpResponse(hd)]++;
  counts["Content-Type"][ContentType(hd)]++;
}
```

Các hàm này sẽ trích xuất giá trị tương ứng hoặc trả về “unknown”. Ví dụ:

```cpp
string ExitState(HttpDownload hd) {
  if (hd.has_event_log() && hd.event_log().has_exit_state()) {
    return ExitStateTypeName(hd.event_log().exit_state());
  } else {
    return "unknown";
  }
}
```

Lưu ý rằng giải pháp thay thế này thậm chí không xác định bất kỳ biến nào! Như chúng ta đã đề cập trong Chương 9, Biến và Khả năng đọc, các biến giữ kết quả trung gian thường có thể bị loại bỏ hoàn toàn.

Trong giải pháp này, chúng tôi chỉ đơn giản là "cắt" vấn đề theo một hướng khác. Cả hai giải pháp đều rất dễ đọc, vì chúng yêu cầu người đọc chỉ nghĩ về một nhiệm vụ tại một thời điểm.

### Tóm lại
Chương này minh họa một kỹ thuật đơn giản để tổ chức code của bạn: chỉ thực hiện một nhiệm vụ tại một thời điểm.

Nếu bạn có code khó đọc, hãy cố gắng liệt kê tất cả các công việc mà code đang thực hiện. Một số tác vụ này có thể dễ dàng trở thành các hàm (hoặc lớp) riêng biệt. Các phần còn lại có thể chỉ trở thành “đoạn văn” hợp lý trong một hàm duy nhất. Chi tiết chính xác về cách bạn phân tách các nhiệm vụ này không quan trọng bằng việc chúng được tách biệt. Phần khó là mô tả chính xác tất cả những điều nhỏ nhặt mà chương trình của bạn đang làm.
