# Chương 12: Biến suy nghĩ trở thành code

![image1](https://raw.githubusercontent.com/n2c1996/images/main/1.png)

> Bạn không thực sự hiểu điều gì đó trừ khi bạn có thể giải thích nó cho
> bà của bạn.  
> Albert Einstein

Khi giải thích một ý tưởng phức tạp cho ai đó, rất dễ để khiến họ bối rối bằng những tiểu tiết.
Một trong những kỹ năng vô cùng quý giá là có thể giải thích một ý tưởng bằng ngôn ngữ mẹ đẻ đơn giản cho một người ít hiểu biết hơn bạn có thể hiểu. Nó đòi hỏi phải chắt lọc ý tưởng ở mức tối đa các khái niệm quan trọng.
Việc làm này không chỉ giúp đối phương hiểu mà còn giúp bạn suy nghĩ về ý tưởng của mình 1 cách rõ ràng hơn.

Kỹ năng tương tự cũng nên được sử dụng khi trình bày code cho người khác. Chúng tôi cho rằng
code là cách chính thống nhất để giải thích chương trình đang làm gì. Vì vậy, code phải được viết bằng tiếng Anh đơn giản.
Trong chương này, chúng ta sẽ áp dụng 1 vài bước đơn giản để giúp bạn viết code rõ rãng hơn:

1.  Mô tả code cần phải hoạt động như nào, bằng tiếng Anh đơn giản như là với đồng nghiệp của bạn
2.  Chú ý tới những từ khóa và cụm từ được dùng trong việc mô tả
3.  Viết mã của bạn để phù hợp với mô tả bên trên

**_I) Mô tả code một cách rõ ràng_**

Đây là đoạn code từ một trang web viết bằng PHP. Mã code này được đặt ở đầu trang vì tính bảo mật. Nó kiểm tra xem người dùng có được phép truy cập vào trang hay không và nếu không thì ngay lập tức thông báo với người dùng rằng cô ấy không được phép truy cập:

    $is_admin = is_admin_request();
    if ($document) {
    	if (!$is_admin && ($document['username'] != $_SESSION['username'])) {
    		return not_authorized();
    	}
    } else {
    	if (!$is_admin) {
    		return not_authorized();
    	}
    }
    // continue rendering the page ...

Có khá nhiều logic trong đoạn code này. Như bạn đã thấy ở chương 2: Đơn giản hóa vòng lặp và logic, phần lớn logic như thế này không dễ hiểu. Logic trong đoạn code này có thể được đơn giản hóa bằng cách nào? Hãy bắt đầu bằng việc mô tả nó bằng tiếng Anh theo cách đơn giản nhất:
Có 2 cách để bạn được xác thực :

1.  Bạn là quản trị viên
2.  Bạn sở hữu tài liệu hiện tại (nếu có)

Ngoài ra thì bạn không được quyền truy cập, và dưới đây là một cách viết khác dựa vào mô tả bên trên

    if (is_admin_request()) {
        // authorized
    } elseif ($document && ($document['username'] == $_SESSION['username'])) {
    	// authorized
    } else {
    	return not_authorized();
    }
    // continue rendering the page ...

Phiên bản này hơi lạ vì nó có hai phần thân trống. Nhưng code đã ngắn hơn và logic đơn giản hơn, bởi vì không có điều kiện phủ định trong mã code. (Giải pháp trước đó có tới 3 lần chúng ta sử dụng toán tử phủ định "!".). Và điểm mấu chốt là nó dễ hiểu hơn.

**_II) Hiểu những thứ thư viện có thể làm_**

Chúng ta có một trang web, trong đó chứa các "Tip box" để hiển thị những đề xuất hữu ích cho người dùng theo kiểu:

> **Mẹo**: Đăng nhập để xem các truy vấn trước đây của bạn. _[Chỉ cho tôi một mẹo khác!]_

Có rất nhiều mẹo và tất cả chúng đều được ẩn bên trong các thẻ HTML

    <div id="tip-1" class="tip">Tip: Log in to see your past queries.</div>
    <div id="tip-2" class="tip">Tip: Click on a picture to see it close up.</div>

Khi người dùng truy cập vào trang web, một vài mẹo sẽ được hiển thị ngẫu nhiên và phần còn lại đều sẽ được ẩn.
Nếu nhấn vào “Chỉ cho tôi một mẹo khác!”, mẹo tiếp theo sẽ được xuất hiện. Đây là mã code để triển khai tính năng đó bằng thư viện JQuery:

    var show_next_tip = function () {
        var num_tips = $('.tip').size();
        var shown_tip = $('.tip:visible');
        var shown_tip_num = Number(shown_tip.attr('id').slice(4));
        if (shown_tip_num === num_tips) {
    	    $('#tip-1').show();
    	} else {
    		$('#tip-' + (shown_tip_num + 1)).show();
    	}
    	shown_tip.hide();
    }

Đoãn mã trên chạy ổn, nhưng chúng ta có thể làm nó tốt hơn. Hãy bắt đầu bằng việc mô tả bằng từ ngữ những gì đoạn mã đang cố gắng thực hiện:

1.  _Tìm mẹo đang được hiển thị và ẩn nó đi_
2.  _Tìm mẹo kế tiếp đó và hiển thị nó_
3.  _Nếu tất cả mẹo đã được sử dụng thì quay lại mẹo đầu tiên_

Dựa vào mô tả bên trên thì dưới đây là 1 cách khác để hiện thực hóa dưới dạng mã

    var show_next_tip = function () {
        var cur_tip = $('.tip:visible').hide(); // find the currently visible tip and hide it
        var next_tip = cur_tip.next('.tip'); // find the next tip after it
        if (next_tip.size() === 0) { // if we've run out of tips,
    	    next_tip = $('.tip:first'); // cycle back to the first tip
    	}
    	next_tip.show(); // show the new tip
    }

Giải pháp này chứa ít code hơn và không phải thao tác trực tiếp với số nguyên. Nó phù hợp hơn với cách mà một người sẽ nghĩ.
Trong trường hợp này, JQuery đã trở nên hữu ích vì có một phương thức .next () mà chúng ta có thể sử dụng. Phần mã bên trên đã tận dụng được những gì thư viện của bạn có thể cung cấp.

**_III) Áp dụng phương pháp bên trên cho những vấn đề lớn hơn_**

Ở những ví dụ trước, chúng ta đã cố gắng để làm code ngắn hơn. Trong ví dụ sau đây, chúng ta sẽ áp dụng nó vào 1 hàm lớn hơn. Như bạn có thể thấy, phương pháp này giúp bạn chia nhỏ mã code của mình ra bằng cách giúp bạn xác định những phần có thể tách nhỏ.
Hãy tưởng tượng chúng ta có một hệ thống ghi lại các giao dịch mua cổ phiếu. Mỗi giao dịch có bốn phần
dữ liệu:

- Thời gian (ngày và giờ giao dịch chính xác)
- Mã giao dịch
- Giá
- Số lượng cổ phiếu

Vì một số lý do, dữ liệu được trải rộng trên 3 bảng cơ sở dữ liệu riêng biệt, như được minh họa dưới đây. Trong mỗi bảng, thời gian là khóa chính duy nhất.
![image2](https://raw.githubusercontent.com/n2c1996/images/main/2.png)

Bây giờ chúng ta cần viết một chương trình để Join 3 bảng lại với nhau (như một phép toán SQL JOIN).
Bước này sẽ dễ dàng vì các hàng đều được sắp xếp theo thời gian, nhưng thật không may một số hàng bị thiếu dữ liệu. Bạn muốn tìm tất cả các hàng mà cả ba thời điểm đều khớp với nhau, và bỏ qua bất kỳ hàng nào không thể, như trong hình minh họa trước đó, đây là một đoạn mã Python để làm việc đó:

    def PrintStockTransactions():
        stock_iter = db_read("SELECT time, ticker_symbol FROM ...")
        price_iter = ...
        num_shares_iter = ...

    	# Iterate through all the rows of the 3 tables in parallel.
    	while stock_iter and price_iter and num_shares_iter:
    		stock_time = stock_iter.time
    		price_time = price_iter.time
    		num_shares_time = num_shares_iter.time

    		# If all 3 rows don't have the same time, skip over the oldest row
    		# Note: the "<=" below can't just be "<" in case there are 2 tied-oldest.
    		if stock_time != price_time or stock_time != num_shares_time:
    			if stock_time <= price_time and stock_time <= num_shares_time:
    				stock_iter.NextRow()
    			elif price_time <= stock_time and price_time <= num_shares_time:
    				price_iter.NextRow()
    			elif num_shares_time <= stock_time and num_shares_time <= price_time:
    				num_shares_iter.NextRow()
    			else:
    				assert False # impossible
    			continue
    		assert stock_time == price_time == num_shares_time

    		# Print the aligned rows.
    		print "@", stock_time,
    		print stock_iter.ticker_symbol,
    		print price_iter.price,
    		print num_shares_iter.number_of_shares
    		stock_iter.NextRow()
    		price_iter.NextRow()
    		num_shares_iter.NextRow()

Đoạn mã ở trên hoạt động, nhưng có rất nhiều thứ xảy ra với việc vòng lặp bỏ qua những hàng không khớp. Một vài sự nghi ngờ đã bắt đầu xuất hiện trong đầu của bạn theo kiểu : _Liệu có hàng nào bị bỏ lỡ_, _Nó có thể đọc tới cuối luồng cho bất kì vòng lặp nào không_
Có cách nào đê làm nó dễ đọc hơn không ?

**MÔ TẢ VẤN ĐỀ**

Một lần nữa lùi lại và mô tả bằng tiếng Anh những gì chúng ta đang cố thực hiện

- Chúng ta đọc 3 hàng lặp song song
- **Bất cứ khi nào thời gian của các hàng không phù hợp, hãy tăng các hàng để chúng phù hợp**
- Sau đó, in các hàng đã căn chỉnh và tiếp tục tăng hàng
- Tiếp tục làm việc này cho tới khi không còn các hàng ở phía bên trái

Nhìn lại mã lúc đầu, phần lộn xộn nhất là đoạn xử lý tăng hàng để chúng khớp với nhau. Để thể hiện code dễ hiểu hơn, chúng ta có thể tách phần logic này thành một hàm và đặt tên là AdvanceToMatchingTime().
Dưới đây là mã code mới, sử dụng hàm bên trên:

    def PrintStockTransactions():
        stock_iter = ...
        price_iter = ...
        num_shares_iter = ...
        while True:
    	    time = AdvanceToMatchingTime(stock_iter, price_iter, num_shares_iter)
    	    if time is None:
    		    return
    		# Print the aligned rows.
    		print "@", time,
    		print stock_iter.ticker_symbol,
    		print price_iter.price,
    		print num_shares_iter.number_of_shares
    		stock_iter.NextRow()
    		price_iter.NextRow()
    		num_shares_iter.NextRow()

Như bạn có thể thấy, phần mã bên trên dễ hiểu hơn nhiều, vì chúng ta đã giấu đi tất cả những phần lộn xộn của việc xếp các hàng.

**ÁP DỤNG PHƯƠNG PHÁP ĐỆ QUY**

Thật dễ để tưởng tượng cách bạn sẽ viết hàm AdvanceToMatchingTime() .
Trong trường hợp tệ nhất, nó sẽ trông rất giống với các đoạn mã ở phiên bản đầu tiên

    def AdvanceToMatchingTime(stock_iter, price_iter, num_shares_iter):
        # Iterate through all the rows of the 3 tables in parallel.
        while stock_iter and price_iter and num_shares_iter:
    	    stock_time = stock_iter.time
    	    price_time = price_iter.time
    	    num_shares_time = num_shares_iter.time
    	# If all 3 rows don't have the same time, skip over the oldest row
    	if stock_time != price_time or stock_time != num_shares_time:
    		if stock_time <= price_time and stock_time <= num_shares_time:
    			stock_iter.NextRow()
    		elif price_time <= stock_time and price_time <= num_shares_time:
    			price_iter.NextRow()
    		elif num_shares_time <= stock_time and num_shares_time <= price_time:
    			num_shares_iter.NextRow()
    		else:
    			assert False # impossible
    		continue
    		assert stock_time == price_time == num_shares_time
    		return stock_time

Hãy cải thiện mã bằng cách áp dụng hàm AdvanceToMatchingTime (). Đây là mô tả về những gì chức năng này cần thực hiện:

- Nhìn vào thời gian của hàng hiện tại, nếu chúng đã được căn chỉnh thì mọi thứ đã hoàn tất
- Nếu không thì hãy tăng bất kì hàng nào ở phía sau
- Tiếp tục làm điều này cho đến khi các hàng được căn chỉnh (hoặc một trong các vòng lặp được kết thúc)

Mô tả này rõ ràng hơn rất nhiều so với phần mã trước đó. Một điều cần lưu ý là mô tả không bao giờ đề cập đến stock_iter hoặc các chi tiết khác cụ thể cho vấn đề của chúng ta. Nghĩa là chúng ta cũng có thể đổi tên các biến để đơn giản và tổng quát hơn. Đây là kết quả:

    def AdvanceToMatchingTime(row_iter1, row_iter2, row_iter3):
        while row_iter1 and row_iter2 and row_iter3:
    	    t1 = row_iter1.time
    	    t2 = row_iter2.time
    	    t3 = row_iter3.time
    	    if t1 == t2 == t3:
    		    return t1
    		tmax = max(t1, t2, t3)
    		# If any row is "behind," advance it.
    		# Eventually, this while loop will align them all.
    		if t1 < tmax: row_iter1.NextRow()
    		if t2 < tmax: row_iter2.NextRow()
    		if t3 < tmax: row_iter3.NextRow()
    	return None # no alignment could be found

Như bạn có thể thấy, phần mã này dễ đọc hơn. Thuật toán trở nên đơn giản hơn với ít sự so sánh hơn, chúng ta sử dụng các tên ngắn như t1 và không còn cần phải nghĩ về các cột cơ sở dữ liệu cụ thể có liên quan.

**KẾT LUẬN**

Chương này thảo luận về kỹ thuật đơn giản để mô tả chương trình của bạn bằng tiếng Anh và sử dụng mô tả đó để giúp bạn viết mã tự nhiên hơn. Kỹ thuật này đơn giản, nhưng rất mạnh mẽ. Nhìn vào các từ và cụm từ được sử dụng trong mô tả của bạn cũng có thể giúp bạn xác định những vấn đề phụ nào cần giải quyết.

Quy trình “nói mọi thứ bằng tiếng Anh thuần túy” có thể áp dụng vượt ra ngoài việc viết mã.
Ví dụ: chính sách của 1 phòng máy tính tại một trường đại học quy định rằng khi một sinh viên cần trợ giúp gỡ lỗi, trước tiên anh ấy phải giải thích vấn đề với một chú gấu bông chuyên dụng ở góc phòng. Đáng ngạc nhiên là chỉ cần mô tả vấn đề thì đã có thể giúp học sinh tìm ra giải pháp. Kỹ thuật này được gọi là “Rubber ducking”.

Một cách khác để xem xét vấn đề, nếu bạn không thể mô tả vấn đề hoặc thiết kế của mình bằng lời khi vẫn còn một cái gì đó có thể bị thiếu hoặc không xác định thì đưa ra một chương trình (hoặc bất kỳ ý tưởng nào) thành lời có thể thực sự biến nó thành hình dạng cụ thể.
