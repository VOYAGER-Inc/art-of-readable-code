# Chapter 6: Making Comments Precise and Compact

<img width="529" alt="image" src="https://user-images.githubusercontent.com/9583479/171091564-307cff1b-7e67-482f-8f80-c70ae48b657f.png">


Ở chương trước đã đề cập về những gì bạn nên viết comment. Trong chương này sẽ nói về cách viết comment sao cho đúng và ngắn gọn.

Nếu bạn định viết một comment, nó càng cụ thể và chi tiết càng tốt. Mặt khác, comment sẽ chiếm thêm không gian trên màn hình và mất thêm thời gian để đọc. Vì vậy comments cũng nên ngắn gọn.

> **KEY IDEA**
>

**Comment phải chứa tỉ lệ thông tin cao.**

Phần còn lại của chương này sẽ đưa ra một vài ví dụ về cách comment.

## Keep Comments Compact (Giữ cho Comment luôn ngắn gọn)

Dưới đây là một ví dụ comment trong C++

```jsx
// The int is the CategoryType.
// The first float in the inner pair is the 'score', 
// the second is the 'weight'.
typedef hash_map<int, pair<float, float> > ScoreMap;
```

Tại sao phải sử dụng đến 3 dòng comment để giải thích nó trong khi bạn có thể mô tả chỉ với 1 line?

```jsx
// CategoryType -> (score, weight)
typedef hash_map<int, pair<float, float> > ScoreMap;
```

Một số trường hợp đặt biệt có thể sử dụng đến 3 dòng comment, nhưng trường hợp trên thật sự là không cần thiết. 

## Avoid Ambiguous Pronouns (Tránh sử dụng Đại từ)

Như câu chuyện cổ điển “Who’s on First?” là một minh họa, đại từ có thể khiến mọi thứ trở nên rất khó hiểu.

Người đọc phải mất thêm thời gian để “
ra” một đại từ. Và trong một số trường hợp, không rõ “it” hoặc “this” đang đề cập đến điều gì. Đây là một ví dụ:

```jsx
// Insert the data into the cache, but check if it's too big first.
```

Trong comment này, "it" có thể đề cập đến dữ liệu hoặc bộ nhớ cache. Để bạn tìm ra được điều đó thì có thể phải đọc thêm phần còn lại của đoạn code. 

Để tốt hơn ta nên thay thế đại từ nếu có với một đối tượng cụ thể. Trong ví dụ trước, giả sử “it” là “data”:

```jsx
// Insert the data into the cache, but check if the data is too big first.
```

Đây là cách thay đổi đơn giản nhất. Bạn cũng có thể thay đổi lại cấu trúc câu để làm cho “it” rõ ràng hơn:

```jsx
// If the data is small enough, insert it into the cache.
```

## Polish Sloppy Sentences

Trong nhiều trường hợp, việc thêm một comment phù hợp đi đôi với việc làm cho nó ngắn gọn hơn. 

Đây là một ví dụ từ web crawler:

```jsx
// Depending on whether we've already crawled this URL before, give it a different priority.
```

Câu trên có vẻ ổn, nhưng hãy so sánh nó với câu bên dưới:

```jsx
// Give higher priority to URLs we've never crawled before.
```

Thực sự nó đã đơn giản hơn, ngắn gọn và đúng trọng tâm hơn. Nó cũng giải thích rằng mức độ ưu tiên cao hơn được dành cho các URL chưa được thu thập thông tin — comment trước đó không chứa thông tin đó.

## Describe Function Behavior Precisely (Mô tả đầy đủ hành vi của chức năng)

Hãy tưởng tượng bạn vừa viết một hàm đếm số dòng trong một tệp:

```jsx
// Return the number of lines in this file.
int CountLines(string filename) { ... }
```

Comment này không chính xác lắm — có nhiều cách để xác định “line”. Dưới đây là một số trường hợp:

* "" (an empty file)—0 or 1 line?
* "hello"—0 or 1 line?
* "hello\n"—1 or 2 lines?
* "hello\n world"—1 or 2 lines?
* "hello\n\r cruel\n world\r"—2, 3, or 4 lines?

Cách triển khai đơn giản nhất là đếm số ký tự các line mới (\ n). (Đây là cách hoạt động của lệnh Unix.) Dưới đây là một comment tốt hơn để phù hợp với việc triển khai này:

```jsx
// Count how many newline bytes ('\n') are in the file.
int CountLines(string filename) { ... }
```

Comment này không dài hơn nhiều so với phiên bản đầu tiên, nhưng chứa nhiều thông tin hơn. Nó cho người đọc biết rằng hàm có thể trả về 0 nếu không có dòng mới. Nó cũng cho người đọc biết rằng các dấu xuống dòng (\ r) bị bỏ qua.

## Use Input/Output Examples That Illustrate Corner Cases (Sử dụng các ví dụ Input / Output để minh họa các trường hợp)

Khi nói đến comment, một ví dụ về input/output có thể có đến hàng nghìn từ để lựa chọn.

Ví dụ: đây là một hàm phổ biến xóa các phần của một chuỗi:

```jsx
// Remove the suffix/prefix of 'chars' from the input 'src'.
String Strip(String src, String chars) { ... }
```

Comment này không chính xác lắm vì nó không thể trả lời các câu hỏi như:

* Các ký tự có phải là toàn bộ chuỗi con cần được loại bỏ hay chỉ là một tập hợp các chữ cái không có thứ tự?
* Điều gì sẽ xảy ra nếu có nhiều ký tự ở cuối src?

Thay vào đó, một ví dụ này có thể trả lời những câu hỏi sau:

```jsx
// ...
// Example: Strip("abba/a/ba", "ab") returns "/a/" String Strip(String src, String chars) { ... }
```

Ví dụ “shows off” toàn bộ chức năng của Strip (). Lưu ý rằng một ví dụ đơn giản hơn sẽ không hữu ích nếu nó không trả lời được những câu hỏi đó:

```jsx
// Example: Strip("ab", "a") returns "b"
```

Dưới đây là một ví dụ khác về một hàm:

```jsx
// Rearrange 'v' so that elements < pivot come before those >= pivot;
// Then return the largest 'i' for which v[i] < pivot (or -1 if none are < pivot) int Partition(vector<int>* v, int pivot);
```

Comment này thực sự rất chính xác, nhưng hơi khó hình dung. Dưới đây là một ví dụ bạn có thể sử dụng:

```jsx
// ...
// Example: Partition([8 5 9 8 2], 8) might result in [5 2 | 8 9 8] and return 1
int Partition(vector<int>* v, int pivot);
```

Nội dung comment đề cập về input/output như sau:

* Pivot sẽ trả về các giá trị trong vector.
* Có đến 2 vectơ (8) để minh họa rằng đây là đầu vào có thể cho phép.
* Vector trả về không được sắp xếp theo thứ tự.

## State the Intent of Your Code (Mô tả mục đích xử lý của code)

Như đã đề cập trong chương trước, comment thường là cho người đọc biết bạn đang nghĩ gì khi code. Nhưng nhiều comment chỉ mô tả những gì code làm theo nghĩa đen, mà không có đầy đủ thông tin.

Ví dụ:

```jsx
void DisplayProducts(list<Product> products) { 
    products.sort(CompareProductByPrice);

    // Iterate through the list in reverse order
    for (list<Product>::reverse_iterator it = products.rbegin(); it != products.rend(); ++it)
    
    ...
}
```

Thay vì vậy, comment như sau sẽ tốt hơn:

```jsx
// Display each price, from highest to lowest
for (list<Product>::reverse_iterator it = products.rbegin(); ... )
```

Comment này giải thích những gì chương trình đang thực hiện ở cấp độ cao hơn.

Có một lỗi trong chương trình này! Chức năng **CompareProductByPrice** đã sắp xếp các product có giá cao hơn trước. Đoạn mã đang làm ngược lại với những gì tác giả dự định.

Đây là lý do tại sao comment thứ hai là tốt hơn. Mặc dù có lỗi, comment đầu tiên là đúng về mặt kỹ thuật (vòng lặp lặp lại theo thứ tự ngược lại). Nhưng với comment thứ hai, người đọc sẽ dễ hiểu mục đích của người code (trước tiên là hiển thị các product có giá cao hơn) mâu thuẫn với những gì code thực sự làm. Trên thực tế, comment hoạt động như một cách kiểm tra sự dư thừa.

Cuối cùng, cách kiểm tra dự phòng tốt nhất là kiểm tra unit test (see Chapter 14, Testing and Readability). 

## “Named Function Parameter” Comments (Comment cho các đối số)

Ví dụ:

```jsx
Connect(10, false);
```

Hàm trên các đối số được truyền vào không rõ chức năng (magic number).

Trong các ngôn ngữ như Python, bạn có thể gán các đối số theo tên:

```jsx
def Connect(timeout, use_encryption): ... 

// Call the function using named parameters
Connect(timeout = 10, use_encryption = False)
```

Trong các ngôn ngữ như C ++ và Java, bạn không thể làm điều này. Tuy nhiên, bạn có thể sử dụng comment để có chức năng tương tự:

```jsx
void Connect(int timeout, bool use_encryption) { ... } 

// Call the function with commented parameters
Connect(/* timeout_ms = */ 10, /* use_encryption = */ false);
```

Lưu ý rằng chúng tôi đã “named” cho tham số đầu tiên là timeout_ms thay vì timeout. Lý tưởng nhất, đối số thực của hàm sẽ là timeout_ms, nhưng nếu vì lý do nào đó mà chúng tôi không thể thực hiện thay đổi này, thì đây là một cách hữu ích để "improve" tên.

Khi nói đến đối số boolean, điều đặc biệt quan trọng là phải đặt / * name = * / trước value. Đặt comment sau value sẽ rất khó hiểu:

```jsx
// Don't do this!
Connect( ... , false /* use_encryption */);

// Don't do this either!
Connect( ... , false /* = use_encryption */);
```

Trong ví dụ này, không rõ liệu false nghĩa là “use encryption” hay “don’t use encryption”.

Hầu hết các lệnh gọi hàm không cần những bình luận như thế này, nhưng đó là một cách tiện dụng (và nhỏ gọn) để giải thích các đối số bí ẩn.

## Use Information-Dense Words (Sử dụng từ ngữ quá dài dòng)

Khi bạn là một lập trình viên có nhiều năm kinh nghiệm. Bạn sẽ biết được một vài vấn đề và giải pháp khi gặp thường xuyên. Thông thường, có những từ hoặc cụm từ cụ thể đã được chia sẻ để mô tả cho patterns/idioms. Sử dụng những từ này sẽ làm cho comment của bạn trở nên ngắn gọn hơn. 

Ví dụ:

```jsx
// This class contains a number of members that store the same information as in the
// database, but are stored here for speed. When this class is read from later, those
// members are checked first to see if they exist, and if so are returned; otherwise the // database is read from and that data stored in those fields for next time.
```

Thay vì vậy, bạn chỉ cần:

```jsx
// This class acts as a caching layer to the database.
```

Một ví dụ khác:

```jsx
// Remove excess whitespace from the street address, and do lots of other cleanup
// like turn "Avenue" into "Ave." This way, if there are two different street addresses // that are typed in slightly differently, they will have the same cleaned-up version and // we can detect that these are equal.
```

Có thể thay thế bằng:

```jsx
// Canonicalize the street address (remove extra spaces, "Avenue" -> "Ave.", etc.)
```

Có nhiều từ có thể tóm gọn và chứa nhiều thông tin, như là “heuristic,” “brute- force,” “naive solution”. Nếu comment quá dài dòng, hãy xem xét và mô tả sao cho ngắn gọn nhất có thể.

### Tóm lại
Chương này nói về việc viết các comment đóng gói càng nhiều thông tin vào một không gian càng nhỏ càng tốt. Dưới đây là các mẹo cụ thể:

* Tránh dùng các đại từ như “it” và “this” khi chúng có thể ám chỉ nhiều thứ.
* Mô tả hành vi của một chức năng với độ phù hợp càng cao càng tốt.
* Minh họa comment của bạn bằng các ví dụ input/output.
* Nêu mục đích chính chức năng các mã của bạn.
* Sử dụng các chú thích inline (e.g., Function(/* arg = */ ... )) để giải thích các đối số của hàm.
* Giữ cho comment của bạn ngắn gọn bằng cách sử dụng các từ đầy đủ ý nghĩa.
