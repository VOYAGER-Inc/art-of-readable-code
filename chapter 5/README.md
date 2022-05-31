Mục tiêu của chương này là giúp bạn nhận ra những gì bạn nên bình luận. Bạn có thể nghĩ rằng mục đích của bình luận là để “giải thích code hoạt động gì”, nhưng đó chỉ là một phần nhỏ của nó.
## Ý TƯỞNG CHÍNH
**Mục đích của bình luận là giúp người đọc biết được nhiều điều như người viết đã làm**.

Khi bạn đang viết code, bạn có rất nhiều thông tin có giá trị trong đầu. Khi người khác đọc code của bạn, thông tin đó sẽ bị mất — tất cả những gì họ có là code trước mặt họ.

Trong chương này, chúng tôi sẽ chỉ cho bạn nhiều ví dụ về thời điểm ghi thông tin đó vào đầu. Chúng tôi đã bỏ qua những điểm trần tục hơn về bình luận; thay vào đó, chúng tôi đã tập trung vào các khía cạnh thú vị hơn và "chưa được phục vụ" của nhận xét.
Chúng tôi đã tổ chức chương thành các lĩnh vực sau:

* Biết gì không bình luận
* Ghi lại suy nghĩ của bạn khi bạn viết code
* Đặt mình vào vị trí của độc giả, để tưởng tượng những gì họ cần biết

## Không nên bình luận gì

Đọc nhận xét mất nhiều thời gian so với đọc code thực và mỗi nhận xét chiếm không gian trên màn hình. Đó là, nó tốt hơn là giá trị nó. Vậy bạn vẽ ranh giới giữa bình luận vô giá trị và bình luận tốt ở đâu?

Tất cả các bình luận trong đoạn code này đều vô giá trị:

```ruby
 #The class definition for Account
    class Account {
      public:
        #Constructor
    Account();
        # Set the profit member to a new value
        void SetProfit(double profit);
        # Return the profit from this Account
        double GetProfit();
    };
  ```

Mục tiêu của chương này là giúp bạn nhận ra những gì bạn nên bình luận. Bạn có thể nghĩ rằng mục đích của bình luận là để “giải thích code hoạt động gì”, nhưng đó chỉ là một phần nhỏ của nó.

## Ý TƯỞNG CHÍNH

**Mục đích của bình luận là giúp người đọc biết được nhiều điều như người viết đã làm.**

  Tuy nhiên, từ “nhanh chóng” là một sự khác biệt quan trọng. Hãy xem xét nhận xét cho code Python này:
  
  ```ruby
   #remove everything after the second '*'
    name = '*'.join(line.split('*')[:2])
  ```

Về mặt kỹ thuật, bình luận này cũng không đưa ra bất kỳ “thông tin mới” nào. Nếu bạn nhìn vào chính code, cuối cùng bạn sẽ hiểu nó đang làm gì. Nhưng đối với hầu hết các lập trình viên, việc đọc code được nhận xét nhanh hơn nhiều so với việc hiểu code mà không có nó.

**Đừng bình luận chỉ vì thích bình luận**
Một số giáo sư yêu cầu sinh viên của họ có một nhận xét cho mỗi chức năng trong code bài tập về nhà của họ. Do đó, một số lập trình viên cảm thấy tội lỗi khi để một hàm không có chú thích và cuối cùng phải viết lại tên và các đối số của hàm dưới dạng câu:

```ruby
# Find the Node in the given subtree, with the given name, using the given depth.
Node* FindNodeInSubtree(Node* subtree, string name, int depth);
```

Cái này thuộc danh mục "nhận xét vô giá trị" — khai báo của hàm và nhận xét hầu như giống nhau. Nhận xét này nên được xóa hoặc cải thiện.
Nếu bạn muốn có một bình luận ở đây, nó cũng có thể giải thích thêm về các chi tiết quan trọng:

```ruby
  #Find a Node with the given 'name' or return NULL.
  #If depth <= 0, only 'subtree' is inspected.
  #If depth == N, only 'subtree' and N levels below are inspected.
  Node* FindNodeInSubtree(Node* subtree, string name, int depth);
```

**Đừng bình luận tên xấu — Thay vào đó hãy sửa tên**
Một bình luận không nên tạo ra một cái tên xấu. Ví dụ: đây là một nhận xét có vẻ ngây thơ cho một hàm có tên CleanReply ():

```ruby
  # Enforce limits on the Reply as stated in the Request,
  # such as the number of items returned, or total byte size, etc.
  void CleanReply(Request request, Reply reply);
```

Hầu hết các bình luận chỉ đơn giản là giải thích "sạch" nghĩa là gì. Thay vào đó, cụm từ “thực thi các giới hạn” nên được chuyển vào tên hàm:

  ```ruby
    # Make sure 'reply' meets the count/byte/etc. limits from the 'request'
      void EnforceLimitsFromRequest(Request request, Reply reply)
  ```
  
Tên hàm này mang tính chất “tự lập tài liệu” hơn. Một cái tên hay sẽ tốt hơn một bình luận hay bởi vì nó sẽ được nhìn thấy ở mọi nơi mà hàm được sử dụng.
Đây là một ví dụ khác về nhận xét cho một hàm được đặt tên kém:

```ruby
  # Releases the handle for this key. This doesn't modify the actual registry.
    void DeleteRegistry(RegistryKey* key);
```

Cái tên DeleteRegistry () nghe có vẻ như một hàm nguy hiểm (nó xóa sổ đăng ký ?!). Các
nhận xét "Điều này không sửa đổi sổ đăng ký thực tế" đang cố gắng làm rõ sự nhầm lẫn.

Thay vào đó, chúng tôi có thể sử dụng một tên tự ghi lại hơn như:

  ```ruby
    void ReleaseRegistryHandle(RegistryKey* key);
  ```

Nói chung, bạn không muốn “nhận xét khó hiểu” —các nhận xét cố gắng bù đắp cho tính không thể đọc được của code. Những người viết code thường nêu quy tắc này là code **tốt > code xấu + nhận xét tốt**

**Ghi lại suy nghĩ của bạn**

  Bây giờ bạn đã biết điều gì không nên bình luận, hãy thảo luận về điều gì nên bình luận (nhưng thường thì không).
  Rất nhiều nhận xét tốt có thể chỉ đơn giản là “ghi lại suy nghĩ của bạn” — nghĩa là, những suy nghĩ quan trọng mà bạn có khi viết code.

**Bao gồm "Bình luận của đạo diễn"**

Phim thường có phần "bình luận của đạo diễn", nơi các nhà làm phim đưa ra những hiểu biết sâu sắc của họ và kể những câu chuyện để giúp bạn hiểu cách thực hiện bộ phim. Tương tự, bạn nên bao gồm các nhận xét để ghi lại những hiểu biết có giá trị về code.

Đây là một ví dụ:

      `// Surprisingly, a binary tree was 40% faster than a hash table for this data.`

      `// The cost of computing a hash was more than the left/right comparisons.`

Nhận xét này dạy cho người đọc điều gì đó và ngăn chặn bất kỳ trình tối ưu hóa nào lãng phí thời gian của họ.
Đây là một ví dụ khác:

`// This heuristic might miss a few words. That's OK; solving this 100% is hard.`

Nếu không có nhận xét này, người đọc có thể nghĩ rằng có một lỗi và có thể lãng phí thời gian để tìm ra các trường hợp thử nghiệm khiến nó không thành công hoặc bắt đầu và cố gắng sửa lỗi.

Một nhận xét cũng có thể giải thích tại sao code không ở dạng tuyệt vời:

 `// This class is getting messy. Maybe we should create a 'ResourceNode' subclass to`
  `// help organize things.`

Nhận xét này thừa nhận rằng code là lộn xộn nhưng cũng khuyến khích người tiếp theo sửa nó (với các chi tiết cụ thể về cách bắt đầu). Nếu không có bình luận, nhiều độc giả sẽ cảm thấy sợ hãi bởi dòng code lộn xộn và sợ chạm vào nó.

**Nhận xét các sai sót trong code của bạn**

Code không ngừng phát triển và chắc chắn sẽ có những sai sót trên đường đi. Đừng xấu hổ khi ghi lại những sai sót đó. Ví dụ: lưu ý khi nào nên thực hiện các cải tiến:
` // TODO: use a faster algorithm
`
hoặc khi code không đầy đủ:
` // TODO(dustin): handle other image formats besides JPEG`

Nhóm của bạn có thể có các quy ước cụ thể về thời điểm / nếu sử dụng các điểm đánh dấu này.
Ví dụ: TODO: có thể được dành riêng cho các vấn đề dừng chiếu. Nếu vậy, đối với những sai sót nhỏ hơn, bạn có thể sử dụng một cái gì đó như todo: (chữ thường) hoặc có thể-sau: để thay thế.

Điều quan trọng là bạn nên luôn bình luận về suy nghĩ của mình về cách code sẽ thay đổi trong tương lai. Những nhận xét như thế này cung cấp cho người đọc cái nhìn sâu sắc có giá trị về chất lượng và trạng thái của code và thậm chí có thể cung cấp cho họ một số hướng về cách cải thiện nó.

**Nhận xét về hằng số của bạn**

Khi xác định một hằng số, thường có một "câu chuyện" cho những gì hằng số đó làm hoặc tại sao nó có giá trị cụ thể đó. Ví dụ: bạn có thể thấy hằng số này trong code của mình:

`NUM_THREADS = 8`

Có vẻ như dòng này không cần nhận xét, nhưng có khả năng lập trình viên đã chọn dòng này biết thêm về nó:

`NUM_THREADS = 8 # as long as it's >= 2 * num_processors, that's good enough.`

Bây giờ người đọc code có một số hướng dẫn về cách điều chỉnh giá trị đó (ví dụ: cài đặt nó thành 1 có lẽ là quá thấp và đặt nó thành 50 là quá mức cần thiết).

Hoặc đôi khi giá trị chính xác của một hằng số không quan trọng chút nào. Một nhận xét cho hiệu ứng này vẫn hữu ích:

` // Impose a reasonable limit - no human can read that much anyway.`
  `  const int MAX_RSS_SUBSCRIPTIONS = 1000;`

Đôi khi đó là một giá trị được điều chỉnh nhiều và có lẽ không nên điều chỉnh nhiều:

`image_quality = 0.72; // users thought 0.72 gave the best size/quality tradeoff`

Trong tất cả các ví dụ này, bạn có thể không nghĩ đến việc thêm nhận xét, nhưng chúng khá hữu ích.

Có một số hằng số không cần chú thích, vì tên của chúng đủ rõ ràng (ví dụ: SECONDS_PER_DAY). Nhưng theo kinh nghiệm của chúng tôi, hầu hết các hằng số có thể được cải thiện bằng cách thêm nhận xét. Nó chỉ là một vấn đề ghi lại những gì bạn đã nghĩ về khi bạn quyết định về giá trị của hằng số đó.

**Đặt mình vào vị trí của độc giả**

Một kỹ thuật chung mà chúng tôi sử dụng trong cuốn sách này là **tưởng tượng code của bạn trông như thế nào đối với người ngoài** — một người không quen thuộc với dự án của bạn như bạn. Kỹ thuật này đặc biệt hữu ích để giúp bạn nhận ra những gì cần bình luận.
**Dự đoán các câu hỏi có thể xảy ra**

Khi ai đó đọc code của bạn, có những phần có thể khiến họ suy nghĩ, Hả? Chuyện này là sao? Việc của bạn là bình luận những phần đó.

Ví dụ: hãy xem định nghĩa của Clear ():

```ruby
 struct Recorder {
        vector<float> data;
        ...
        void Clear() {
vector<float>().swap(data); // Huh? Why not just data.clear()? }
};
```

Khi hầu hết các lập trình viên C ++ nhìn thấy code này, họ nghĩ, Tại sao anh ta không thực hiện data.clear () thay vì hoán đổi với một vector trống? Chà, hóa ra đây là cách duy nhất để buộc một vector thực sự chuyển giao bộ nhớ của nó cho bộ cấp phát bộ nhớ. Đó là một chi tiết C ++ không được biết đến nhiều. Điểm mấu chốt là nó nên được nhận xét:

```ruby
 # Force vector to relinquish its memory (look up "STL swap trick")
    vector<float>().swap(data);
```

**Những cạm bẫy có khả năng xảy ra với quảng cáo**

Khi ghi lại một hàm hoặc lớp, một câu hỏi hay để tự hỏi là, Điều gì đáng ngạc nhiên về đoạn code này? Làm thế nào nó có thể bị sử dụng sai? Về cơ bản, bạn muốn "suy nghĩ trước" và dự đoán những vấn đề mà mọi người có thể gặp phải khi sử dụng code của bạn.

Ví dụ: giả sử bạn đã viết một hàm gửi email đến một người dùng nhất định:

` void SendEmail(string to, string subject, string body);`

Việc triển khai chức năng này liên quan đến việc kết nối với dịch vụ email bên ngoài, có thể mất đến cả giây hoặc có thể lâu hơn. Ai đó đang viết ứng dụng web có thể không nhận ra điều này và gọi nhầm hàm này trong khi xử lý một yêu cầu HTTP. (Làm điều này sẽ khiến ứng dụng web của họ "treo" nếu dịch vụ email không hoạt động.)

Để ngăn chặn sự cố có thể xảy ra này, bạn nên nhận xét về "chi tiết triển khai" này:

``` 
#Calls an external service to deliver email.  (Times out after 1 minute.)
void SendEmail(string to, string subject, string body);
```

Đây là một ví dụ khác: giả sử bạn có một hàm FixBrokenHtml () cố gắng viết lại

HTML bị hỏng bằng cách chèn các thẻ đóng bị thiếu và những thứ tương tự:

` def FixBrokenHtml(html): ...`

Chức năng hoạt động tuyệt vời, ngoại trừ cảnh báo rằng thời gian chạy của nó sẽ tăng lên khi có các thẻ lồng nhau sâu và không khớp. Đối với các đầu vào HTML xấu, chức năng này có thể mất vài phút để thực thi.
Thay vì để người dùng tự khám phá điều này sau đó, tốt hơn nên thông báo trước điều này:

`// Runtime is O(number_tags * average_tag_depth), so watch out for badly nested inputs.

    def FixBrokenHtml(html): ...`

**Nhận xét về "Ảnh lớn"**

Một trong những điều khó nhất đối với một thành viên mới trong nhóm là “bức tranh toàn cảnh” —cách các lớp tương tác, cách dữ liệu chảy qua toàn bộ hệ thống và vị trí của các điểm nhập. Người thiết kế hệ thống thường quên bình luận về những thứ này vì anh ta có liên quan mật thiết đến nó.

Hãy xem xét thử nghiệm suy nghĩ này: ai đó mới gia nhập nhóm của bạn, cô ấy đang ngồi bên cạnh bạn và bạn cần cho cô ấy làm quen với cơ sở code.
Khi cho cô ấy tham quan cơ sở code, bạn có thể chỉ ra các tệp hoặc lớp nhất định và nói những điều như:

* “Đây là code keo giữa logic kinh doanh của chúng tôi và cơ sở dữ liệu. Không có code ứng dụng nào nên sử dụng trực tiếp code này. ”

* “Lớp này trông có vẻ phức tạp, nhưng nó thực sự chỉ là một bộ nhớ đệm thông minh. Nó không biết bất cứ điều gì về phần còn lại của hệ thống. ”

Sau một phút trò chuyện thông thường, thành viên mới trong nhóm của bạn sẽ biết nhiều hơn những gì cô ấy có thể tự đọc nguồn.

**Đây chính xác là loại thông tin nên được đưa vào các bình luận cấp cao.**

Dưới đây là một ví dụ đơn giản về nhận xét cấp tệp:

`// Tệp này chứa các hàm trợ giúp cung cấp giao diện thuận tiện hơn cho`
`// hệ thống tập tin. Nó xử lý các quyền đối với tệp và các chi tiết phức tạp khác.`

Đừng để bị choáng ngợp bởi suy nghĩ rằng bạn phải viết những tài liệu chính thức và phong phú. Một vài câu được chọn tốt hơn là không có gì cả.

**Nhận xét tóm tắt**

Ngay cả khi ở sâu bên trong một chức năng, bạn nên nhận xét về “bức tranh lớn hơn”. Dưới đây là ví dụ về một nhận xét tóm tắt gọn gàng code cấp thấp bên dưới nó:

```ruby
 # Find all the items that customers purchased for themselves.
    for customer_id in all_customers:
        for sale in all_sales[customer_id].sales:
            if sale.recipient == customer_id:
                ...
```

Nếu không có bình luận này, đọc từng dòng code là một chút bí ẩn. (“Tôi thấy chúng ta đang lặp lại qua all_customers ... nhưng để làm gì?”)
Đặc biệt hữu ích nếu có những nhận xét tóm tắt này trong các chức năng dài hơn, nơi có một vài "phần" lớn bên trong:

```ruby
 def GenerateUserReport():
        # Acquire a lock for this user
        ...
        # Read user's info from the database
        ...
        # Write info to a file
        ...
        # Release the lock for this user
```

Những nhận xét này cũng đóng vai trò như một bản tóm tắt có dấu đầu dòng về chức năng hoạt động, vì vậy người đọc có thể biết được ý chính của chức năng trước khi đi sâu vào chi tiết. (Nếu các phần này có thể dễ dàng tách rời, bạn có thể chỉ cần làm cho chúng hoạt động theo chức năng của riêng chúng. Như chúng tôi đã đề cập trước đây, code tốt sẽ tốt hơn code xấu với nhận xét tốt.)

**BẠN NÊN BÌNH LUẬN CÁI GÌ, TẠI SAO HAY CÁCH NÀO?**

_Bạn có thể đã nghe những lời khuyên như, "Hãy bình luận lý do tại sao, không phải điều gì (hoặc cách thức)." Mặc dù hấp dẫn, chúng tôi cảm thấy những tuyên bố này quá đơn giản và có ý nghĩa khác nhau đối với những người khác nhau.

Lời khuyên của chúng tôi là làm bất cứ điều gì giúp người đọc hiểu code dễ dàng hơn. Điều này có thể liên quan đến việc bình luận cái gì, bằng cách nào hoặc tại sao (hoặc cả ba).

**Suy nghĩ cuối cùng — Vượt qua khối của người viết**

Rất nhiều lập trình viên không thích viết bình luận vì cảm thấy viết một bình luận hay có vẻ tốn nhiều công sức. Khi người viết có kiểu “nhà văn” như vậy, giải pháp tốt nhất là chỉ cần bắt đầu viết. Vì vậy, lần tới khi bạn do dự khi viết bình luận, hãy tiếp tục và bình luận những gì bạn đang nghĩ, dù nó có thể là nửa vời.

Ví dụ: giả sử bạn đang làm việc trên một chức năng và tự nghĩ rằng, Ồ, công cụ này sẽ trở nên phức tạp nếu có bao giờ trùng lặp trong danh sách này. Chỉ cần viết ra:

`// Ôi thật vô nghĩa, công cụ này sẽ trở nên phức tạp nếu có bao giờ trùng lặp trong danh sách này.`

Thấy chưa, khó quá phải không? Nó thực sự không tệ về một nhận xét - chắc chắn tốt hơn là không có gì. Ngôn ngữ là hơi mơ hồ. Để khắc phục, chúng ta có thể xem qua từng cụm từ và thay thế bằng cụm từ nào đó cụ thể hơn:
* Thay vì "oh crap,", bạn có thể viết là "Careful: this is something to watch out for."

* Thay vì "this stuff,", bạn có thể viết là "the code that’s handling this input."

* Thay vì "will get tricky", bạn có thể viết là will be hard to implement.”

 Nhận xét mới có thể là: 
 ```
  // Cẩn thận: code này không xử lý các bản sao trong danh sách (vì điều đó khó thực hiện)
 ```

Lưu ý rằng chúng tôi đã chia nhỏ nhiệm vụ viết nhận xét thành các bước đơn giản hơn sau:

1. Viết ra bất cứ nhận xét nào trong tâm trí bạn.

2. Đọc nhận xét, và xem những gì (nếu có) cần được cải thiện.

3. Thực hiện các cải tiến.

Khi bạn nhận xét thường xuyên hơn, bạn sẽ thấy rằng chất lượng của các nhận xét từ bước 1 ngày càng tốt hơn và cuối cùng có thể không cần sửa gì cả. Và bằng cách bình luận sớm và thường xuyên, bạn sẽ tránh được tình huống khó chịu khi phải viết một đống bình luận ở cuối bài.

**Tóm lược**

Mục đích của bình luận là giúp người đọc biết người viết đã biết gì khi viết code. Toàn bộ chương này là về việc nhận ra tất cả những thông tin không quá rõ ràng mà bạn có về code và viết chúng ra.

Có gì không bình luận:

	•	Các dữ kiện có thể nhanh chóng rút ra từ chính code.

	•	Các nhận xét của trợ giúp" làm đẹp phần code không hợp lệ (chẳng hạn như tên hàm không hợp lệ) - hãy sửa code để thay thế.
  
  Những điều bạn nên ghi lại bao gồm:

	•	Những hiểu biết sâu sắc về lý do tại sao code là cách này mà không phải là cách khác (“bài bình luận của đạo diễn”).

	•	Sai sót trong code của bạn, bằng cách sử dụng các điểm đánh dấu như TODO: hoặc XXX :

	•	“Câu chuyện” về cách một hằng số có giá trị như thế nào. Hãy đặt bạn vào vị trí của người đọc:

	•	Dự đoán phần nào trong code của bạn sẽ khiến người đọc thốt lên "Hả?" và nhận xét những.

	•	Ghi lại bất kỳ hành vi đáng ngạc nhiên nào mà một người đọc bình thường sẽ không mong đợi.

	•	Sử dụng nhận xét "bức tranh lớn" ở cấp tệp / lớp để giải thích cách tất cả các phần khớp với nhau.

	•	Tóm tắt các khối code với các nhận xét để người đọc không bị lạc vào các chi tiết.

