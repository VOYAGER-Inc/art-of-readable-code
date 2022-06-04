# Chương 14: Kiểm thử và khả năng đọc

![Image from 003_the_art_of_readable_code, page 149](https://user-images.githubusercontent.com/38417570/171209623-a6704452-f506-4f62-832b-4b1bf668b74a.jpg)

Trong chương này, chúng tôi sẽ chỉ cho bạn các kỹ thuật đơn giản để viết các bài kiểm thử gọn gàng và hiệu quả.
Kiểm thử có nghĩa là những điều khác nhau đối với những người khác nhau. Trong chương này, chúng tôi sử dụng "kiểm thử" có nghĩa là bất kỳ mã nào có mục đích duy nhất là kiểm thử hành vi của một đoạn mã ("thực") khác. Chúng tôi sẽ tập trung vào khía cạnh dễ đọc của các bài kiểm thử chứ không đi sâu vào việc liệu bạn có nên viết mã kiểm thử trước khi viết mã thực ("phát triển hướng kiểm thử") hay các khía cạnh triết học khác của phát triển hướng kiểm thử hay không.

## Làm cho các bài kiểm thử dễ đọc và dễ bảo trì

Điều quan trọng đối với mã kiểm thử là có thể dễ đọc như là mã không kiểm thử. Các lập trình viên khác thường xem mã kiểm thử như là tài liệu không chính thức về cách mã thực hoạt động và nên được sử dụng. Vì vậy, nếu các bài kiểm thử dễ đọc, người dùng sẽ hiểu rõ hơn về cách thức hoạt động của mã thực.

> **Ý TƯỞNG CHÍNH**
> 
>**Mã kiểm thử nên dễ đọc để những người lập trình khác có thể thoải mái thay đổi hoặc thêm các kiểm thử khác.**
>

Khi mã kiểm thử lớn và đáng sợ, sau đây là những gì sẽ xảy ra:
- Người viết mã sợ sửa đổi mã thực. Ồ, chúng tôi không muốn làm rối với mã đó — cập nhật tất cả các bài kiểm thử sẽ là một cơn ác mộng!
- Người viết mã không thêm kiểm thử mới khi họ thêm mã mới. Theo thời gian, ngày càng ít mô-đun của bạn được kiểm thử và bạn không còn tự tin rằng tất cả mô-đun đều hoạt động.
Thay vào đó, bạn muốn khuyến khích người dùng mã của bạn (đặc biệt là bạn!) Cảm thấy thoải mái với mã kiểm thử. Họ sẽ có thể chẩn đoán lý do tại sao một thay đổi mới phá vỡ một kiểm thử hiện có và cảm thấy như việc thêm các kiểm thử mới thật dễ dàng.

## Điều gì sai với bài kiểm thử này?

Trong cơ sở mã của chúng tôi, chúng tôi có một chức năng để sắp xếp và lọc danh sách các kết quả tìm kiếm được chấm điểm. Đây là khai báo hàm:
```python
  // Sắp xếp 'tài liệu' theo điểm (cao nhất trước tiên) và xóa tài liệu bị điểm âm.
  void SortAndFilterDocs(vector<ScoredDocument>* docs);
```
Kiểm thử chức năng này ban đầu trông giống như sau:
```python
  void Test1() {
    vector<ScoredDocument> docs;
    docs.resize(5);
    docs[0].url = "http://example.com";
    docs[0].score = -5.0;
    docs[1].url = "http://example.com";
    docs[1].score = 1;
    docs[2].url = "http://example.com";
    docs[2].score = 4;
    docs[3].url = "http://example.com";
    docs[3].score = -99998.7;
    docs[4].url = "http://example.com";
    docs[4].score = 3.0;
    SortAndFilterDocs(&docs);
    assert(docs.size() == 3);
    assert(docs[0].score == 4);
    assert(docs[1].score == 3.0);
    assert(docs[2].score == 1);
  }
```
Có ít nhất tám vấn đề khác nhau với mã kiểm thử này. Đến cuối chương, bạn sẽ có thể xác định và sửa chữa tất cả chúng.

## Làm cho bài kiểm thử này dễ đọc hơn

Theo nguyên tắc thiết kế chung, bạn nên **giấu những chi tiết ít quan trọng hơn từ phía người dùng, thay vào đó hãy để những chi tiết quan trọng hơn trở nên nổi bật nhất**.
Mã kiểm thử từ phần trước rõ ràng vi phạm nguyên tắc này. Mọi chi tiết của bài kiểm thử đều nằm ở phía trước và trung tâm, giống như những chi tiết nhỏ không quan trọng của việc thiết lập một vectơ `<ScoredDocument>`. Hầu hết mã ví dụ liên quan đến url, điểm số và tài liệu [], chỉ là thông tin chi tiết về cách các đối tượng C++ bên dưới được thiết lập, không phải về những gì kiểm thử này đang thực hiện ở cấp độ cao.
Bước đầu tiên trong quá trình làm rõ vấn đề này, chúng ta có thể tạo một hàm trợ giúp như:

```python
  void MakeScoredDoc(ScoredDocument* sd, double score, string url) {
    sd->score = score;
    sd->url = url;
  }
```
Sử dụng hàm này, mã kiểm thử của chúng tôi trở nên rút gọn hơn một chút:

```python
  void Test1() {
    vector<ScoredDocument> docs;
    docs.resize(5);
    MakeScoredDoc(&docs[0], -5.0, "http://example.com");
    MakeScoredDoc(&docs[1], 1, "http://example.com");
    MakeScoredDoc(&docs[2], 4, "http://example.com");
    MakeScoredDoc(&docs[3], -99998.7, "http://example.com");
    …
  }
```
Nhưng điều này vẫn chưa đủ tốt - vẫn còn những chi tiết không quan trọng trong vấn đề của chúng ta. Ví dụ: tham số "http://example.com" thật chướng mắt. Nó luôn giống nhau và URL chính xác thậm chí không quan trọng — bạn chỉ cần điền vào `ScoredDocument`. Một chi tiết không quan trọng khác mà chúng tôi buộc phải xem là docs.resize(5) và & docs[0], & docs[1], v.v. Hãy thay đổi hàm trợ giúp của chúng tôi để thực hiện nhiều công việc hơn và gọi nó là `AddScoredDoc()`:
```python
  void AddScoredDoc(vector<ScoredDocument>& docs, double score) {
    ScoredDocument sd;
    sd.score = score;
    sd.url = "http://example.com";
    docs.push_back(sd);
  }
```
Sử dụng hàm này, mã kiểm thử của chúng tôi thậm chí còn được rút gọn hơn:
```python
  void Test1() {
    vector<ScoredDocument> docs;
    AddScoredDoc(docs, -5.0);
    AddScoredDoc(docs, 1);
    AddScoredDoc(docs, 4);
    AddScoredDoc(docs, -99998.7);
    …
  }
```
Mã này tốt hơn, nhưng vẫn không vượt qua được bài kiểm tra "dễ đọc và dễ ghi ở mức độ cao". Nếu bạn muốn thêm một bài kiểm thử khác với một bộ tài liệu đã được chấm điểm mới, nó sẽ yêu cầu sao chép và dán rất nhiều.
Vì vậy, làm thế nào để chúng ta tiếp tục cải thiện nó hơn nữa?
## Tạo mệnh đề kiểm thử tối thiểu

Để cải thiện mã kiểm thử này, hãy sử dụng kỹ thuật từ *Chương 12, Biến suy nghĩ thành mã*.
Hãy mô tả những gì bài kiểm thử của chúng tôi đang cố gắng thực hiện bằng tiếng Anh đơn giản:
  Chúng tôi có một danh sách các tài liệu có điểm là [-5, 1, 4, -99998,7, 3]. 
  Sau `SortAndFilterDocs()`, các tài liệu còn lại phải có điểm là [4, 3, 1], theo thứ tự đó.
Như bạn thấy, không có chỗ nào trong phần mô tả đó mà chúng tôi đề cập đến một vectơ `<ScoredDocument>`. Mảng điểm số là điều quan trọng nhất ở đây. Lý tưởng nhất, mã kiểm thử của chúng tôi sẽ trông giống như sau:
```python
CheckScoresBeforeAfter("-5, 1, 4, -99998.7, 3", "4, 3, 1");
```
Chúng ta đã có thể tổng hợp bản chất của kiểm thử này thành một dòng mã!
Tuy nhiên, điều này không có gì lạ. Bản chất của hầu hết các bài kiểm thử là *đối với đầu vào / tình huống này, mong đợi hành vi / đầu ra kia*. Và nhiều khi mục tiêu này có thể được diễn đạt chỉ trong một dòng. Ngoài việc làm cho mã rất nhỏ gọn và dễ đọc, việc giữ các câu lệnh kiểm thử của bạn ngắn gọn giúp bạn dễ dàng thêm nhiều trường hợp kiểm thử hơn.
## Triển khai tùy chỉnh “ngôn ngữ nhỏ” 

*Ngôn ngữ miền chuyên biệt hay ngôn ngữ đặc tả chuyên biệt (tiếng Anh: domain-specific language hay DSL) là một ngôn ngữ máy tính chuyên dùng cho một miền ứng dụng. Các DSL đơn giản hơn, đặc biệt được dùng bởi chỉ một ứng dụng, đôi khi được gọi một cách không chính thức là ngôn ngữ nhỏ (mini-language)*
Lưu ý rằng `CheckScoresBeforeAfter()` nhận hai đối số chuỗi mô tả mảng điểm. Trong các phiên bản C++ mới hơn, bạn có thể chuyển vào các ký tự mảng như sau:
```c++
  CheckScoresBeforeAfter({-5, 1, 4, -99998.7, 3}, {4, 3, 1});
```
Bởi vì không thể làm điều này vào thời điểm đó, vì thế chúng ta đặt điểm số bên trong một chuỗi, phân tách bằng dấu phẩy.
Để cách làm này hoạt động, `CheckScoresBeforeAfter()` sẽ phải phân tích cú pháp các đối số chuỗi đó.
Nói chung, việc xác định một ngôn ngữ nhỏ tùy chỉnh có thể là một cách hiệu quả để thể hiện nhiều thông tin trong một khoảng không gian nhỏ. Các ví dụ khác bao gồm thư viện `printf()` và biểu thức chính quy.
Trong trường hợp này, việc viết một số hàm trợ giúp để phân tích cú pháp danh sách các số được phân tách bằng dấu phẩy sẽ không quá khó. Đây là giao diện của `CheckScoresBeforeAfter()`:
```c++
  void CheckScoresBeforeAfter(string input, string expected_output) {
    vector<ScoredDocument> docs = ScoredDocsFromString(input);
    SortAndFilterDocs(&docs);
    string output = ScoredDocsToString(docs);
    assert(output == expected_output);
  }
```
Và để đầy đủ, đây là các hàm trợ giúp chuyển đổi giữa chuỗi và vectơ `<ScoredDocument>`:
```c++
  vector<ScoredDocument> ScoredDocsFromString(string scores) {
    vector<ScoredDocument> docs;
    replace(scores.begin(), scores.end(), ',', ' ');
    // Điền 'tài liệu' từ một chuỗi điểm số được phân tách bằng dấu cách.
    istringstream stream(scores);
    double score;
    while (stream >> score) {
      AddScoredDoc(docs, score);
    }
    return docs;
  }
  string ScoredDocsToString(vector<ScoredDocument> docs) {
    ostringstream stream;
    for (int i = 0; i < docs.size(); i++) {
      if (i > 0) stream << ", ";
      stream << docs[i].score;
    }
    return stream.str();
  }
```
Thoạt nhìn, điều này có vẻ giống rất nhiều mã, nhưng những gì nó cho phép bạn làm là vô cùng mạnh mẽ.
Vì bạn có thể viết toàn bộ bài kiểm thử chỉ bằng một lệnh gọi tới `CheckScoresBeforeAfter()`, bạn sẽ có ý định thêm nhiều bài kiểm thử hơn (như chúng ta sẽ thực hiện ở phần sau của chương).
## Làm cho thông báo lỗi dễ đọc

![Image from 003_the_art_of_readable_code, page 154](https://user-images.githubusercontent.com/38417570/171209944-aa92af92-4a88-42d3-866c-84f1310d4000.jpg)

Mã trước đó rất hay, nhưng điều gì sẽ xảy ra khi dòng xác nhận (output == expected_output) đó không thành công? Nó tạo ra một thông báo lỗi như sau:
```python
  Assertion failed: (output == expected_output),
    function CheckScoresBeforeAfter, file test.cc, line 37.
```
Rõ ràng, nếu bạn đã từng gặp lỗi này, bạn sẽ tự hỏi, giá trị của đầu ra và đầu ra mong đợi là gì?

## Sử dụng các phiên bản tốt hơn của assert()

May mắn thay, hầu hết các ngôn ngữ và thư viện đều có các phiên bản phức tạp hơn của `assert()` mà bạn có thể sử dụng. Vì vậy, thay vì viết:
```python
assert(output == expected_output);
```
bạn có thể sử dụng thư viện Boost C ++:
```python
BOOST_REQUIRE_EQUAL(output, expected_output)
```
Bây giờ, nếu kiểm thử không thành công, bạn sẽ nhận được một thông báo chi tiết hơn như:
```python
  test.cc(37): fatal error in "CheckScoresBeforeAfter": critical check
    output == expected_output failed ["1, 3, 4" != "4, 3, 1"]
```
điều đó hữu ích hơn nhiều.
Bạn nên sử dụng các phương pháp xác nhận hữu ích hơn này khi chúng có sẵn. Nó sẽ trả kết quả mỗi khi kiểm thử của bạn không thành công.
***
<p align="center">`ASSERT()` TỐT HƠN TRONG CÁC NGÔN NGỮ KHÁC</p>
Trong Python, câu lệnh tích hợp khẳng định a == b tạo ra một thông báo lỗi đơn giản như:
```python
  File "file.py", line X, in <module>
    assert a == b
  AssertionError
```
Thay vào đó, bạn có thể sử dụng phương thức assertEqual() trong mô-đun đơn nhất:
```python
    import unittest
    class MyTestCase(unittest.TestCase):
      def testFunction(self):
        a = 1
        b = 2
        self.assertEqual(a, b)
    if __name__ == '__main__':
      unittest.main()
```
tạo ra một thông báo lỗi như:
```python
  File "MyTestCase.py", line 7, in testFunction
    self.assertEqual(a, b)
  AssertionError: 1 != 2
```
Cho dù bạn đang sử dụng ngôn ngữ nào, có thể có một thư viện/framework(ví dụ: XUnit) có sẵn để hỗ trợ bạn. Nó sẽ cho kết quả để biết các thư viện của bạn!
***

## Thông báo lỗi được tạo thủ công

Sử dụng `BOOST_REQUIRE_EQUAL()`, chúng tôi có thể nhận được thông báo lỗi đẹp hơn:
```python
  output == expected_output failed ["1, 3, 4" != "4, 3, 1"]
```
Tuy nhiên, thông báo này có thể được cải thiện hơn nữa. Ví dụ: sẽ hữu ích khi xem đầu vào ban đầu gây ra lỗi này. Thông báo lỗi lý tưởng sẽ giống như sau:
```python
  CheckScoresBeforeAfter() failed,
  Đầu vào: "-5, 1, 4, -99998.7, 3"
  Đầu ra mong đợi: "4, 3, 1"
  Đầu ra thực tế: "1, 3, 4"
```
Nếu đây là những gì bạn muốn, hãy tiếp tục và viết nó!
```python
void CheckScoresBeforeAfter(...) {
  …
  if (output != expected_output) {
    cerr << "CheckScoresBeforeAfter() failed," << endl;
    cerr << "Input: Ä"" << input << "Ä"" << endl;
    cerr << "Expected Output: Ä"" << expected_output << "Ä"" << endl;
    cerr << "Actual Output: Ä"" << output << "Ä"" << endl;
    abort();
  }
```
Đạo lý của câu chuyện là các thông báo lỗi phải hữu ích nhất có thể. Đôi khi, in thông điệp của riêng bạn bằng cách xây dựng một “tùy chỉnh xác nhận” là cách tốt nhất để làm điều này.
## Chọn đầu vào kiểm thử tốt

Có một nghệ thuật để chọn các giá trị đầu vào tốt cho các bài kiểm thử của bạn. Những cái chúng ta có ngay bây giờ có vẻ hơi lộn xộn:
```python
  CheckScoresBeforeAfter("-5, 1, 4, -99998.7, 3", "4, 3, 1");
```
Làm thế nào để chúng tôi chọn các giá trị đầu vào tốt? Đầu vào tốt nên kiểm thử kỹ mã. Nhưng chúng cũng phải đơn giản để dễ đọc.
> **Ý TƯỞNG CHÍNH**
> 
> **Nói chung, bạn nên chọn bộ đầu vào đơn giản nhất thực hiện hoàn toàn mã.**
>

Ví dụ: giả sử chúng ta vừa viết:
```python
CheckScoresBeforeAfter("1, 2, 3", "3, 2, 1");
```
Mặc dù kiểm thử này đơn giản nhưng nó không kiểm thử hành vi “lọc điểm âm” của `SortAndFilterDocs()`. Nếu có lỗi trong phần đó của mã, đầu vào này sẽ không kích hoạt lỗi đó.
Ở khía cạnh khác, giả sử chúng ta đã viết bài kiểm thử của mình như thế này:
```python
  CheckScoresBeforeAfter("123014, -1082342, 823423, 234205, -235235",
  "823423, 234205, 123014");
```
Những giá trị này không cần thiết phải phức tạp. (Và chúng thậm chí không kiểm tra mã kỹ lưỡng.)

## Đơn giản hóa các giá trị đầu vào

Vậy chúng ta có thể làm gì để cải thiện các giá trị đầu vào này?
```python
  CheckScoresBeforeAfter("-5, 1, 4, -99998.7, 3", "4, 3, 1");
```
Chà, điều đầu tiên bạn có thể nhận thấy là giá trị rất “lớn” -99998,7. Giá trị đó chỉ có nghĩa là "bất kỳ số âm nào", vì vậy giá trị đơn giản hơn chỉ là -1. (Nếu -99998,7 có nghĩa là "một số rất âm", thì giá trị tốt hơn sẽ là một cái gì đó rõ ràng như -1e100.)
> **Ý TƯỞNG CHÍNH**
> 
> **Ưu tiên các giá trị kiểm thử rõ ràng và đơn giản mà vẫn hoàn thành công việc.**
>

Các giá trị khác trong kiểm thử của chúng tôi không quá tệ, nhưng khi chúng tôi ở đây, chúng tôi có thể giảm chúng xuống các số nguyên đơn giản nhất có thể. Ngoài ra, chỉ cần một giá trị âm để kiểm tra xem các giá trị âm có bị loại bỏ hay không. Đây là phiên bản kiểm thử mới của chúng tôi:
```python
  CheckScoresBeforeAfter("1, 2, -1, 3", "3, 2, 1");
```
Chúng tôi đã đơn giản hóa các giá trị kiểm thử mà không làm cho chúng kém hiệu quả hơn.
* * *
<p align="center"> KIỂM THỬ "SMASHER" LỚN</p>

![Image from 003_the_art_of_readable_code, page 157](https://user-images.githubusercontent.com/38417570/171210546-a244fc1f-6d11-4aa2-83d7-57a05d732ec9.jpg)

Chắc chắn có giá trị trong việc kiểm thử mã của bạn với các đầu vào lớn, điên rồ. Ví dụ: bạn có thể bị cám dỗ để đưa vào một bài kiểm thử như:
```python
  CheckScoresBeforeAfter("100, 38, 19, -25, 4, 84, [nhiều giá trị khác] ...", "100, 99, 98, 97, 96, 95, 94, 93, ...");
```
Các đầu vào lớn như thế này thực hiện rất tốt việc phát hiện ra các lỗi như tràn bộ đệm hoặc những lỗi khác mà bạn có thể không ngờ tới.
Nhưng mã như thế này rất lớn và đáng sợ khi nhìn vào và không hoàn toàn hiệu quả trong việc kiểm tra mã.
Thay vào đó, sẽ hiệu quả hơn nếu tạo các đầu vào lớn theo chương trình, xây dựng một đầu vào lớn gồm (giả sử) 100.000 giá trị.
* * *

## Đa dạng các kiểm thử về chức năng

Thay vì xây dựng một đầu vào “hoàn hảo” duy nhất để thực hiện kỹ lưỡng mã của bạn, việc viết nhiều kiểm thử nhỏ hơn thường dễ dàng hơn, hiệu quả hơn và dễ đọc hơn.
Mỗi bài kiểm thử sẽ đẩy mã của bạn theo một hướng nhất định, cố gắng tìm ra một lỗi cụ thể. Ví dụ: đây là bốn bài kiểm tra cho `SortAndFilterDocs()`:
```python
CheckScoresBeforeAfter("2, 1, 3", "3, 2, 1"); // Sắp sếp cơ bản
CheckScoresBeforeAfter("0, -0.1, -10", "0"); // Tất cả giá trị < 0 đã xoá
CheckScoresBeforeAfter("1, -2, 1, -2", "1, 1"); // Trùng lặp không là vấn để
CheckScoresBeforeAfter("", ""); // Đầu vào rỗng OK
```
Thậm chí có nhiều bài kiểm thử bạn có thể viết nếu bạn muốn cực kỳ kỹ lưỡng. Có các trường hợp thử nghiệm riêng biệt cũng giúp người tiếp theo làm việc trên mã dễ dàng hơn. Nếu ai đó tình cờ giới thiệu một lỗi, kiểm thử thất bại sẽ xác định chính xác cụ thể kiểm thử đã không thành công.

## Đặt tên chức năng kiểm thử

Mã kiểm thử thường được tổ chức thành các chức năng — một cho mỗi phương thức và hoặc tình huống bạn đang kiểm thử. Ví dụ: mã kiểm thử `SortAndFilterDocs()` bên trong một hàm có tên `Test1()`:
```python
  void Test1() {
    …
  }
```
Chọn một cái tên hay cho một hàm kiểm thử có vẻ tẻ nhạt và không liên quan, nhưng đừng dùng đến những cái tên vô nghĩa như `Test1()`, `Test2()`, v.v.
Thay vào đó, bạn nên sử dụng tên để mô tả chi tiết về bài kiểm thử. Đặc biệt, sẽ rất hữu ích nếu người đọc mã kiểm thử có thể nhanh chóng tìm ra:
- Lớp đang được kiểm thử (nếu có)
- Chức năng đang được kiểm thử
- Tình huống hoặc lỗi đang được kiểm thử
Một cách tiếp cận đơn giản để xây dựng một tên hàm kiểm thử tốt là chỉ cần nối thông tin đó với nhau, có thể với tiền tố “Test_”.
Ví dụ: thay vì đặt tên là `Test1()`, chúng ta có thể sử dụng định dạng
`Test_<Tên Hàm>()`:
```python
  void Test_SortAndFilterDocs() {
…
}
```
Tùy thuộc vào mức độ phức tạp của kiểm thử này, bạn có thể xem xét một chức năng kiểm thử riêng biệt cho từng tình huống. Bạn có thể sử dụng định dạng: Test_<Tên Hàm>_<Tình Huống>():
```python
  void Test_SortAndFilterDocs_BasicSorting() {
  …
  }
  void Test_SortAndFilterDocs_NegativeValues() {
  ...
  }
  …
```
Đừng sợ có một cái tên dài hoặc khó hiểu ở đây. Đây không phải là một hàm sẽ được gọi trong toàn bộ cơ sở mã của bạn, vì vậy các lý do để tránh các tên hàm dài không áp dụng. Tên chức năng kiểm thử hoạt động hiệu quả giống như một nhận xét. Ngoài ra, nếu kiểm thử đó không thành công, hầu hết các frameworks kiểm thử sẽ in ra tên của hàm mà xác nhận không thành công, vì vậy một tên mô tả đặc biệt hữu ích.
Lưu ý rằng nếu bạn đang sử dụng frameworks kiểm thử, có thể đã có các quy tắc hoặc quy ước về cách đặt tên các phương thức. Ví dụ: mô-đun "unittest" Python mong đợi các tên phương pháp kiểm thử bắt đầu bằng “test”.
Khi nói đến việc đặt tên cho các hàm trợ giúp trong mã thử nghiệm của bạn, bạn nên làm nổi bật liệu hàm có tự xác nhận hay không hay chỉ là một trợ giúp thông thường “test-unaware”. Ví dụ, trong chương này, bất kỳ hàm trợ giúp nào gọi `assert()` được đặt tên là Check... (). Nhưng hàm `AddScoredDoc()` được đặt tên giống như một hàm trợ giúp thông thường.

## Điều gì sai với kiểm thử đó?

Ở đầu chương, chúng tôi khẳng định có ít nhất tám điều sai trong bài kiểm thử này:
```python
void Test1() {
  vector<ScoredDocument> docs;
  docs.resize(5);
  docs[0].url = "http://example.com";
  docs[0].score = -5.0;
  docs[1].url = "http://example.com";
  docs[1].score = 1;
  docs[2].url = "http://example.com";
  docs[2].score = 4;
  docs[3].url = "http://example.com";
  docs[3].score = -99998.7;
  docs[4].url = "http://example.com";
  docs[4].score = 3.0;
  SortAndFilterDocs(&docs);
  assert(docs.size() == 3);
  assert(docs[0].score == 4);
  assert(docs[1].score == 3.0);
  assert(docs[2].score == 1);
}
```
Bây giờ chúng ta đã học được một số kỹ thuật để viết các bài kiểm thử tốt hơn, hãy khám phá chúng:
1. Bài kiểm thử rất dài và đầy những chi tiết không quan trọng. Bạn có thể mô tả những gì bài kiểm thử này đang thực hiện trong một câu văn, do đó, câu lệnh kiểm thử không nên dài hơn nhiều.
2. Thêm một bài kiểm thử khác không phải là dễ dàng. Bạn muốn sao chép / dán / sửa đổi, điều này sẽ làm cho mã dài hơn và đầy sự trùng lặp.
3. Thông báo lỗi kiểm thử không hữu ích lắm. Nếu kiểm thử này không thành công, nó sẽ chỉ thông báo `Assertion failed: docs.size() == 3`, điều này không cung cấp cho bạn đủ thông tin để gỡ lỗi.
4. Bài kiểm thử cố gắng kiểm thử mọi thứ cùng một lúc. Nó đang cố gắng kiểm thử cả chức năng lọc số âm và chức năng sắp xếp. Sẽ dễ đọc hơn nếu chia nó thành nhiều bài kiểm thử.
5. Đầu vào kiểm thử không đơn giản. Đặc biệt, điểm số ví dụ -99998,7 là "lớn" và thu hút sự chú ý của bạn mặc dù không có bất kỳ ý nghĩa nào đối với giá trị cụ thể đó. Giá trị âm đơn giản hơn là đủ.
6. Các đầu vào kiểm thử không thực hiện kỹ lưỡng mã. Ví dụ: nó không kiểm tra khi điểm là 0. (Tài liệu đó có được lọc hay không?)
7. Nó không kiểm thử các đầu vào cực đoan khác, chẳng hạn như vectơ đầu vào trống, vectơ rất lớn hoặc một vectơ có điểm trùng lặp.
8. Tên `Test1()` là vô nghĩa - tên phải mô tả chức năng hoặc tình huống đang được kiểm thử.

## Phát triển độ thân thiện của kiểm thử

Một số mã dễ kiểm thử hơn các mã khác. Mã lý tưởng để kiểm thử có giao diện được xác định rõ, không có nhiều trạng thái hoặc "thiết lập" khác và không có nhiều dữ liệu ẩn để kiểm tra.
Nếu bạn viết mã của mình và biết rằng bạn sẽ viết một bài kiểm thử cho nó sau này, một điều thú vị sẽ xảy ra: **bạn bắt đầu thiết kế mã của mình sao cho dễ kiểm thử!** May mắn thay, viết mã theo cách này cũng có nghĩa là bạn tạo ra mã tốt hơn nói chung. Các thiết kế thân thiện với kiểm thử thường dẫn đến mã được tổ chức tốt một cách tự nhiên, với các phần riêng biệt để thực hiện những việc riêng biệt.

* * *
<p align="center">PHÁT TRIỂN HƯỚNG KIỂM THỬ</p>
Phát triển hướng kiểm thử (TDD) là một phong cách lập trình trong đó bạn viết các bài kiểm thử trước khi viết mã thực. Những người ủng hộ TDD tin rằng quá trình này cải thiện sâu sắc chất lượng của mã nontest, hơn nhiều so với việc bạn viết các bài kiểm thử sau khi viết mã.
Đây là một chủ đề được tranh luận sôi nổi mà chúng tôi sẽ không tham gia. Ít nhất, chúng tôi nhận thấy rằng chỉ cần ghi nhớ kiểm thử trong khi viết mã sẽ giúp mã tốt hơn.
Nhưng bất kể bạn có sử dụng TDD hay không, kết quả cuối cùng là bạn có mã kiểm thử mã khác. Mục tiêu của chương này là giúp bạn đọc và viết các bài kiểm thử của mình dễ dàng hơn.
Trong tất cả các cách để chia nhỏ một chương trình thành các lớp và phương thức, những cách được tách ra nhiều nhất thường là cách dễ kiểm thử nhất. Mặt khác, giả sử chương trình của bạn rất liên kết với nhau, với nhiều lệnh gọi phương thức giữa các lớp của bạn và rất nhiều tham số cho tất cả các phương thức. Chương trình đó không chỉ có mã khó hiểu, mà mã kiểm thử cũng xấu xí, khó đọc và khó viết.
* * *

Có nhiều thành phần “bên ngoài” (các biến toàn cục cần được khởi tạo, các thư viện hoặc tệp cấu hình cần được tải, v.v.) cũng làm cho việc viết các bài kiểm thử trở nên khó chịu hơn.
Nói chung, nếu bạn đang thiết kế mã của mình và nhận ra, Hmm, đây sẽ là một cơn ác mộng để kiểm thử, đó là lý do chính đáng để dừng lại và suy nghĩ lại về thiết kế. Bảng 14-1 cho thấy một số vấn đề kiểm thử và thiết kế điển hình.
*BẢNG 14-1. Đặc điểm của mã ít có thể kiểm thử hơn và cách điều này dẫn đến các vấn đề với thiết kế*

| **Đặc tính**  | **Vấn đề về khả năng kiểm thử**  |  **Vấn đề về thiết kế** |
|---|---|---|
| Sử dụng các biến toàn cục  | Tất cả các trạng thái toàn cục cần đặt lại cho mọi kiểm thử (nếu không, các thử nghiệm khác nhau có thể làm trở ngại lẫn nhau).  | Khó để hiểu các chức năng có ảnh hưởng gì. Không thể nghĩ về từng chức năng trong sự cô lập; cần phải xem xét toàn bộ chương trình để hiểu nếu tất cả hoạt động.|
|Mã phụ thuộc vào rất nhiều thành phần bên ngoài   | Khó hơn khi viết bất kỳ bài kiểm thử nào bởi vì có rất nhiều giàn giáo để thiết lập đầu tiên. Kiểm thử ít thú vị hơn để viết, vì vậy mọi người thường tránh các bài kiểm thử.| Hệ thống có nhiều khả năng bị lỗi khi một trong những phụ thuộc không thành công. Thật khó để hiểu tác động của bất kỳ thay đổi nào có thể tạo ra. Khó hơn để cấu trúc lại các lớp. Hệ thống có nhiều chế độ lỗi và đường dẫn khôi phục hơn để suy nghĩ.  |
| Mã có hành vi không xác định  | Các bài kiểm thử không ổn định và không đáng tin cậy. Các thử nghiệm đôi khi không thành công sẽ bị bỏ qua. | Chương trình có nhiều khả năng có các điều kiện chủng tộc hoặc các lỗi không thể tái tạo khác. Chương trình này khó lý giải hơn. Các lỗi trong production rất khó theo dõi và sửa chữa.|

Mặt khác, nếu bạn có một thiết kế dễ viết kiểm thử thì đó là một dấu hiệu tốt. Bảng 14-2 liệt kê một số đặc điểm thiết kế và kiểm thử có lợi.
*BẢNG 14-2. Đặc điểm của mã có thể kiểm thử nhiều hơn và cách điều này dẫn đến thiết kế tốt*

| **Đặc tính**  | **Lợi ích về khả năng kiểm thử**  | **Lợi ích khi thiết kế**  |
|---|---|---|
|Các lớp có ít hoặc không có trạng thái nội bộ   | Kiểm thử dễ viết hơn vì cần ít thiết lập hơn để kiểm thử một phương thức và ít trạng thái ẩn hơn để kiểm tra  | Các lớp có ít trạng thái sẽ đơn giản và dễ hiểu hơn  |
| Các lớp / hàm chỉ làm một việc  | Ít trường hợp kiểm thử hơn được yêu cầu để kiểm thử hoàn toàn nó.  | Các thành phần nhỏ hơn / đơn giản hơn có nhiều mô-đun hơn và hệ thống thường được tách rời nhiều hơn. |
| Các lớp phụ thuộc vào một số lớp khác; tách cao | Mỗi lớp có thể được kiểm thử độc lập (dễ hơn nhiều so với việc kiểm thử nhiều lớp cùng một lúc).| Hệ thống có thể được phát triển song song. Các lớp có thể dễ dàng sửa đổi hoặc loại bỏ mà không làm gián đoạn phần còn lại của hệ thống. | 
|Các hàm đơn giản, được xác định rõ ràng giao diện|Có những hành vi được xác định rõ ràng để
kiểm thử. Giao diện đơn giản mất ít hơn thời gian để kiểm tra.|Các giao diện dễ dàng hơn cho người lập trình học hỏi và có nhiều khả năng tái sử dụng.|

## Vượt quá giới hạn cho phép

Nó cũng có thể tập trung quá nhiều vào việc kiểm thử. Dưới đây là một số ví dụ:
- **Hy sinh khả năng đọc của mã thực của bạn, vì lợi ích của việc cho phép các kiểm thử**. Việc thiết kế mã thực của bạn để có thể kiểm thử phải là một tình huống đôi bên cùng có lợi: mã thực của bạn trở nên đơn giản hơn và được tách rời nhiều hơn, và các bài kiểm thử của bạn rất dễ viết. Nhưng nếu bạn phải chèn nhiều đường ống nước xấu xí vào mã thực của mình để bạn có thể kiểm thử nó, thì không còn đúng nữa.
- **Bị ám ảnh về phạm vi kiểm thử 100%**. Kiểm thử 90% mã đầu tiên của bạn thường ít công việc hơn kiểm tra 10% cuối cùng. 10% cuối cùng đó có thể liên quan đến giao diện người dùng hoặc các trường hợp lỗi ngớ ngẩn, trong đó chi phí của lỗi không thực sự cao và nỗ lực để kiểm thử nó là không đáng. Sự thật là bạn sẽ không bao giờ nhận được 100% mức độ phù hợp. Nếu đó không phải là một lỗi bị bỏ sót, nó có thể là một tính năng bị bỏ lỡ hoặc bạn có thể không nhận ra rằng thông số kỹ thuật nên được thay đổi.
Tùy thuộc vào mức độ tốn kém của các lỗi, có một điểm chính xác về thời gian phát triển mà bạn nên dành cho mã kiểm thử. Nếu bạn đang xây dựng một nguyên mẫu trang web, thì việc viết bất kỳ mã kiểm thử nào đều có thể không có giá trị. Mặt khác, nếu bạn đang viết bộ điều khiển cho tàu vũ trụ hoặc thiết bị y tế, thì kiểm thử có lẽ là việc trọng tâm chính của bạn.
- **Để kiểm thử cản trở quá trình phát triển sản phẩm**. Chúng tôi đã thấy các tình huống trong đó kiểm thử, vốn chỉ là một khía cạnh của dự án đã chi phối toàn bộ dự án. Kiểm thử trở thành một loại thần thánh nào đó được khuyên giải và các lập trình viên chỉ cần thực hiện các nghi lễ và cử động mà không nhận ra rằng thời gian kỹ thuật quý giá của họ có thể được dành cho nơi khác tốt hơn.

## Tổng kết

Trong mã kiểm thử, khả năng đọc vẫn rất quan trọng. Nếu các bài kiểm thử của bạn rất dễ đọc, chúng sẽ rất dễ ghi, vì vậy mọi người sẽ thêm nhiều bài kiểm thử hơn. Ngoài ra, nếu bạn thiết kế mã thực của mình để dễ kiểm thử, thì mã của bạn về tổng thể sẽ có thiết kế đẹp hơn.
Dưới đây là các điểm cụ thể về cách cải thiện các bài kiểm thử của bạn:
- Cấp độ cao nhất của mỗi bài kiểm thử phải càng ngắn gọn càng tốt; lý tưởng là mỗi đầu vào / đầu ra kiểm thử có thể được mô tả trong một dòng mã.
- Nếu kiểm thử của bạn không thành công, nó sẽ phát ra một thông báo lỗi để dễ dàng theo dõi và khắc phục lỗi.
- Sử dụng các đầu vào kiểm thử đơn giản nhất để thực hiện hoàn toàn mã của bạn.
- Đặt tên mô tả đầy đủ cho các chức năng kiểm thử của bạn để rõ ràng từng chức năng đang kiểm thử cái gì. Thay vì `Test1()`, hãy sử dụng tên như 
```Test_<Tên Hàm>_<Tình huống>```
Và trên hết, giúp bạn dễ dàng sửa đổi và thêm các bài kiểm thử mới.
