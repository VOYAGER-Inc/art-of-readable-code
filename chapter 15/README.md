# Chapter 15: Designing and Implementing a “Minute/Hour Counter”

![img_0](https://user-images.githubusercontent.com/18412533/171006382-cfc92676-bea0-426b-8259-cb3d28bcb734.png)

Hãy cùng xem cấu trúc dữ liệu được sử dụng trong code thực: “bộ đếm phút / giờ”. Chúng tôi sẽ đưa bạn qua quy trình suy nghĩ tự nhiên mà một kỹ sư có thể phải trải qua, trước tiên cố gắng giải quyết vấn đề này, sau đó cải thiện hiệu suất và thêm các tính năng. Quan trọng nhất, chúng tôi cũng sẽ cố gắng giữ cho code dễ đọc, sử dụng các nguyên tắc xuyên suốt cuốn sách này. Chúng ta có thể phạm phải một số sai lầm trên đường đi hoặc mắc những sai lầm khác. Hãy xem bạn có thể theo dõi và bắt chúng không.

## The Problem

Chúng ta cần theo dõi bao nhiêu byte mà máy chủ web đã chuyển trong phút qua và trong giờ qua. Dưới đây là minh họa về cách các tổng số này được duy trì:

![img_1](https://user-images.githubusercontent.com/18412533/171006486-65691c46-2811-48a0-9030-5662aa9d893e.png)

Đây là một vấn đề khá đơn giản, nhưng như bạn sẽ thấy, giải quyết nó một cách hiệu quả là một thử thách thú vị. Hãy bắt đầu bằng cách xác định class interface.

## Defining the Class Interface

Đây là phiên bản đầu tiên của class interface trong C++:

```
class MinuteHourCounter {
  public:
    // Add a count
    void Count(int num_bytes);

    // Return the count over this minute
    int MinuteCount();

    // Return the count over this hour
    int HourCount();
};
```

Trước khi chúng tôi triển khai class này, hãy xem qua tên và nhận xét để xem có điều gì chúng tôi muốn thay đổi không.

### Improving the Names

Tên lớp MinuteHourCounter khá hay. Nó rất riêng biệt, cụ thể và dễ nói.

Với tên class, tên phương thức MinuteCount() và HourCount() cũng hợp lý. Bạn có thể đã gọi chúng là GetMinuteCount() và GetHourCount(), nhưng điều này không giúp ích được gì. Như chúng ta đã nói trong Chương 3, Những cái tên không thể hiểu sai, “get” ngụ ý “công cụ truy cập nhẹ” đối với nhiều người. Và như bạn sẽ thấy, việc triển khai sẽ không hề nhẹ, vì vậy tốt nhất là bạn nên bỏ "ra ngoài".

Tuy nhiên, tên phương thức Count() có vấn đề. Chúng tôi đã hỏi đồng nghiệp của mình rằng họ nghĩ Count() sẽ làm gì và một số cho rằng nó có nghĩa là “trả về tổng số lần đếm trong mọi thời điểm”. Tên hơi phản trực giác (không có ý định chơi chữ). Vấn đề là Count vừa là danh từ vừa là động từ và có thể có nghĩa là “Tôi muốn đếm số lượng mẫu bạn đã xem” hoặc “Tôi muốn bạn đếm mẫu này”.

Dưới đây là các tên thay thế cần xem xét thay cho Count():

- Increment()
- Observe()
- Record()
- Add()

Increment() gây hiểu lầm vì nó ngụ ý rằng có một giá trị chỉ tăng lên. (Trong trường hợp của chúng tôi, số giờ dao động theo thời gian.)

Observe() là được, nhưng hơi mơ hồ.

Record() cũng có vấn đề về danh từ / động từ, do đó, điều đó không tốt.

Add() rất thú vị vì nó có thể có nghĩa là “thêm cái này bằng số” hoặc “thêm vào danh sách dữ liệu” - trong trường hợp của chúng tôi, nó là một phần của cả hai, vì vậy nó hoạt động. Vì vậy, chúng tôi sẽ đổi tên phương thức thành void Add (int num_bytes).

Nhưng tên đối số num_bytes quá riêng biệt. Đúng, trường hợp sử dụng chính của chúng tôi là đếm byte, nhưng MinuteHourCounter không cần biết điều này. Ai đó có thể sử dụng class này để đếm truy vấn hoặc giao dịch cơ sở dữ liệu. Chúng tôi có thể sử dụng một tên chung chung hơn như delta, nhưng thuật ngữ delta thường được sử dụng ở những nơi mà giá trị có thể âm mà chúng tôi không muốn. Tên phải hoạt động — nó đơn giản, chung chung và ngụ ý "không âm". Ngoài ra, nó cho phép chúng ta tìm hiểu từ “count” trong một ngữ cảnh ít mơ hồ hơn.

### Improving the Comments

Đây là class interface mà chúng tôi có cho đến nay:

```
class MinuteHourCounter {
  public:
    // Add a count
    void Add(int count);

    // Return the count over this minute
    int MinuteCount();

    // Return the count over this hour
    int HourCount();
}
```

Hãy xem qua từng comment về method này và cải thiện chúng. Hãy xem xét điều đầu tiên:

```
// Add a count
void Add(int count);
```

Comment này hiện hoàn toàn dư thừa — cần xóa hoặc cải thiện comment này. Đây là một phiên bản cải tiến:

```
// Add a new data point (count >= 0).
// For the next minute, MinuteCount() will be larger by +count.
// For the next hour, HourCount() will be larger by +count.
void Add(int count);
```

Bây giờ chúng ta hãy xem xét comment cho MinuteCount():

```
// Return the count over this minute
int MinuteCount();
```

Khi chúng tôi hỏi đồng nghiệp của mình nhận xét này có nghĩa là gì, có hai cách giải thích trái ngược nhau:

1. Trả lại số đếm trong giờ-phút hiện tại, chẳng hạn như 12:13 p.m.
2. Trả lại số đếm trong 60 giây qua, bất kể ranh giới đồng hồ-phút.

Cách giải thích thứ hai là cách nó thực sự hoạt động. Vì vậy, hãy làm rõ sự nhầm lẫn này bằng ngôn ngữ chính xác và chi tiết hơn:

```
// Return the accumulated count over the past 60 seconds.
int MinuteCount();
```

(Tương tự, chúng ta nên cải thiện nhận xét cho HourCount().)

Đây là định nghĩa class với tất cả các thay đổi cho đến nay, cùng với class-level comment:

```
// Track the cumulative counts over the past minute and over the past hour.
// Useful, for example, to track recent bandwidth usage.
class MinuteHourCounter {
     // Add a new data point (count >= 0).
     // For the next minute, MinuteCount() will be larger by +count.
     // For the next hour, HourCount() will be larger by +count.
     void Add(int count);

     // Return the accumulated count over the past 60 seconds.
     int MinuteCount();

     // Return the accumulated count over the past 3600 seconds.
     int HourCount();
```

(Để ngắn gọn, chúng tôi sẽ để lại các comments trong danh sách code từ bây giờ.)

- - -
#### <center>GETTING AN OUTSIDE PERSPECTIVE</center>

Bạn có thể nhận thấy rằng đã có một vài trường hợp chúng tôi chạy mọi thứ bởi đồng nghiệp của mình. Yêu cầu góc nhìn bên ngoài là một cách tuyệt vời để kiểm tra xem mã code của bạn có “thân thiện với người dùng” hay không. Cố gắng cởi mở với ấn tượng đầu tiên của họ, vì những người khác có thể đưa ra kết luận tương tự. Và những “người khác” có thể bao gồm bạn trong 6 tháng.
- - -

### Attempt 1: A Naive Solution

Hãy chuyển sang giải quyết vấn đề. Chúng tôi sẽ bắt đầu với một giải pháp đơn giản: chỉ cần giữ một danh sách các “sự
kiện” được đánh dấu thời gian:

```
class MinuteHourCounter {
    struct Event {
        Event(int count, time_t time) : count(count), time(time) {}
        int count;
        time_t time;
    };

    list<Event> events;

  public:
    void Add(int count) {
        events.push_back(Event(count, time()));
    }

    ...
};
```

Sau đó, chúng tôi có thể đếm các sự kiện gần đây nhất nếu cần:

```
class MinuteHourCounter {
    ...

    int MinuteCount() {
        int count = 0;
        const time_t now_secs = time();
        for (list<Event>::reverse_iterator i = events.rbegin();
            i != events.rend() && i->time > now_secs - 60; ++i) {
            count += i->count;
        }
        return count;
    }

    int HourCount() {
        int count = 0;
        const time_t now_secs = time();
        for (list<Event>::reverse_iterator i = events.rbegin();
            i != events.rend() && i->time > now_secs - 3600; ++i) {
            count += i->count;
        }
        return count;
    }
};
```

#### Is the Code Easy to Understand?

Mặc dù giải pháp này là "đúng", có một số vấn đề về khả năng đọc:

- **Vòng lặp for hơi khó đọc.** Hầu hết người đọc chậm lại đáng kể khi họ đang đọc phần này của mã code (ít nhất họ nên đọc, nếu họ đảm bảo không có bất kỳ lỗi nào).
- **MinuteCount() và HourCount() gần như giống hệt nhau.** Nó sẽ làm cho mã code ít hơn nếu họ có thể chia sẻ mã code trùng lặp. Chi tiết này đặc biệt quan trọng vì mã code dư thừa tương đối phức tạp. (Tốt hơn là nên giới hạn tất cả các mã khó ở một nơi.)

#### An Easier-to-Read Version

Code cho MinuteCount() và HourCount() chỉ khác nhau một hằng số duy nhất (60 so với 3600). Tái cấu trúc rõ ràng là giới thiệu một phương thức trợ giúp để xử lý cả hai trường hợp:

```
class MinuteHourCounter {
    list<Event> events;

    int CountSince(time_t cutoff) {
        int count = 0;
        for (list<Event>::reverse_iterator rit = events.rbegin();
            rit != events.rend(); ++rit) {
            if (rit->time <= cutoff) {
                break;
            }
            count += rit->count;
        }
        return count;
    }

  public:
    void Add(int count) {
        events.push_back(Event(count, time()));
    }

    int MinuteCount() {
        return CountSince(time() - 60);
    }

    int HourCount() {
        return CountSince(time() - 3600);
    }
};
```

Có một vài điều đáng chỉ ra về mã code mới này.

Đầu tiên, hãy lưu ý rằng CountSince() nhận tham số ngưỡng tuyệt đối, thay vì giá trị secs_ago tương đối (60 hoặc 3600). Dù theo cách nào thì cũng sẽ hiệu quả, nhưng cách này thì CountSince() có một công việc dễ thực hiện hơn một chút.

Thứ hai, chúng tôi đổi tên trình lặp từ i thành rit. Tên i thường được sử dụng hơn cho các chỉ mục số nguyên. Chúng tôi đã cân nhắc việc sử dụng tên nó, đặc trưng cho các trình vòng lặp. Nhưng trong trường hợp này, chúng ta có một trình lặp ngược và thực tế này rất quan trọng đối với tính đúng đắn của mã. Bằng cách đặt một tên biến có tiền tố là r, nó bổ sung thêm tính đối xứng dễ chịu cho các câu lệnh như rit != events.rend().

Cuối cùng, chúng tôi trích xuất điều kiện rit->time <= cutoff ra khỏi vòng lặp for và biến nó thành một câu lệnh if riêng biệt. Tại sao? Bởi vì các vòng lặp "truyền thống" cho dạng for ((begin; end; advance) dễ đọc nhất. Người đọc có thể hiểu ngay nó là “đi qua tất cả các yếu tố” và không cần phải suy nghĩ thêm về nó.

#### Performance Problems

Mặc dù chúng tôi đã cải thiện giao diện mã code, nhưng thiết kế này có hai vấn đề nghiêm trọng về hiệu suất:

1. **Nó cứ thế phát triển và lớn mạnh.**

   Lớp lưu giữ tất cả các sự kiện mà nó từng thấy — nó sử dụng một lượng bộ nhớ không giới hạn! Tốt nhất, MinuteHourCounter nên tự động xóa các sự kiện cũ hơn một giờ vì chúng không còn cần thiết nữa.

3. **MinuteCount() và HourCount() quá chậm.**

   Phương thức CountSince() sử dụng thời gian O(n), trong đó n là số điểm dữ liệu trong khoảng thời gian liên quan. Hãy tưởng tượng một máy chủ hiệu suất cao được gọi là Add() hàng trăm lần mỗi giây. Mỗi cuộc gọi đến HourCount() sẽ phải tính đến một triệu điểm dữ liệu! Lý tưởng nhất, MinuteHourCounter nên giữ các biến số minute_count và số hour_count riêng biệt được cập nhật với mỗi lần gọi Add().

### Attempt 2: Conveyor Belt Design

Chúng tôi cần một thiết kế giải quyết được cả hai vấn đề trước đây:

1. Xóa dữ liệu chúng ta không cần nữa.
2. Cập nhật tổng số minute_count và hour_count đã tính toán trước.

Đây là cách chúng tôi sẽ thực hiện: chúng tôi sẽ sử dụng danh sách của mình như một băng chuyền. Khi dữ liệu mới đến ở một đầu, chúng tôi sẽ thêm vào tổng số của chúng tôi. Và khi dữ liệu quá cũ, nó sẽ "rơi ra" đầu kia và chúng tôi trừ vào tổng số của chúng tôi.

Có một số cách chúng tôi có thể thực hiện thiết kế băng tải này. Một cách là duy trì hai danh sách độc lập, một danh sách cho các sự kiện trong phút qua, một cho các sự kiện trong giờ qua. Khi có sự kiện mới, hãy thêm bản sao vào cả hai danh sách.

![img_2](https://user-images.githubusercontent.com/18412533/171006556-5479262d-9cb5-4d91-acaa-85a95795648c.png)

Cách này khá đơn giản nhưng không hiệu quả vì nó tạo ra hai bản sao của mọi sự kiện.

Một cách khác là duy trì hai danh sách, trong đó các sự kiện ban đầu đi vào danh sách đầu tiên (“sự kiện phút cuối”), sau đó danh sách này chuyển vào danh sách thứ hai (sự kiện “giờ cuối [nhưng không phải phút cuối]”).

![img_3](https://user-images.githubusercontent.com/18412533/171006667-f12dfa89-d65c-46ff-9026-5edda319bab6.png)

Thiết kế băng chuyền “hai giai đoạn” này có vẻ hiệu quả hơn, vì vậy chúng ta hãy triển khai thiết kế này.

#### Implementing the Two-Stage Conveyor Belt Design

Hãy bắt đầu bằng cách liệt kê các thành viên trong class của chúng ta:

```
class MinuteHourCounter {
    list<Event> minute_events;
    list<Event> hour_events; // only contains elements NOT in minute_events

    int minute_count;
    int hour_count; // counts ALL events over past hour, including past minute
};
```

Điểm mấu chốt của thiết kế băng chuyền này là có thể "thay đổi" các sự kiện khi thời gian trôi qua, để các sự kiện di chuyển từ minute_events sang hour_events và số minute_count và số hour_count được cập nhật tương ứng. Để thực hiện việc này, chúng tôi sẽ tạo một phương thức trợ giúp có tên ShiftOldEvents(). Khi chúng ta có phương thức đó, phần còn lại của class khá dễ thực hiện:

```
void Add(int count) {
    const time_t now_secs = time();
    ShiftOldEvents(now_secs);

    // Feed into the minute list (not into the hour list--that will happen later)
    minute_events.push_back(Event(count, now_secs));

    minute_count += count;
    hour_count += count;
}

int MinuteCount() {
    ShiftOldEvents(time());
    return minute_count;
}

int HourCount() {
    ShiftOldEvents(time());
    return hour_count;
}
```

Rõ ràng, chúng tôi đã trì hoãn tất cả công việc bẩn thỉu cho ShiftOldEvents():

```
// Find and delete old events, and decrease hour_count and minute_count accordingly.
void ShiftOldEvents(time_t now_secs) {
    const int minute_ago = now_secs - 60;
    const int hour_ago = now_secs - 3600;

    // Move events more than one minute old from 'minute_events' into 'hour_events'
    // (Events older than one hour will be removed in the second loop.)
    while (!minute_events.empty() && minute_events.front().time <= minute_ago) {
        hour_events.push_back(minute_events.front());

        minute_count -= minute_events.front().count;
        minute_events.pop_front();
    }

    // Remove events more than one hour old from 'hour_events'
    while (!hour_events.empty() && hour_events.front().time <= hour_ago) {
        hour_count -= hour_events.front().count;
        hour_events.pop_front();
    }
}
```

#### Are We Done?

Chúng tôi đã giải quyết được hai vấn đề về hiệu suất mà chúng tôi đã đề cập trước đó và giải pháp của chúng tôi hoạt động. Đối với nhiều ứng dụng, giải pháp này sẽ đủ tốt. Nhưng cũng có một số thiếu sót.

Đầu tiên, thiết kế rất không linh hoạt. Giả sử chúng tôi muốn giữ số lượng trong 24 giờ qua. Điều đó sẽ yêu cầu thực hiện rất nhiều thay đổi đối với mã code. Và như bạn có thể nhận thấy, ShiftOldEvents() là một hàm khá dày đặc, với sự tương tác tinh tế giữa dữ liệu phút và giờ.

Thứ hai, class này có bộ nhớ khá lớn. Giả sử bạn có một máy chủ lưu lượng truy cập cao gọi Add() 100 lần mỗi giây. Bởi vì chúng tôi lưu giữ tất cả dữ liệu trong giờ qua, mã này sẽ yêu cầu khoảng 5MB bộ nhớ.

Nói chung, Add() được gọi thường xuyên hơn, chúng ta càng sử dụng nhiều bộ nhớ hơn. Trong môi trường sản xuất, các thư viện sử dụng một lượng lớn bộ nhớ không thể đoán trước là không tốt. Lý tưởng nhất, MinuteHourCounter sẽ sử dụng một lượng bộ nhớ cố định cho dù Add() được gọi thường xuyên như thế nào.

### Attempt 3: A Time-Bucketed Design

Có thể bạn không nhận thấy, nhưng cả hai lần triển khai trước đều có một lỗi nhỏ. Chúng tôi đã sử dụng time_t để lưu trữ dấu thời gian, dấu thời gian lưu trữ một số giây tích phân. Do làm tròn này, MinuteCount() thực sự trả về một nơi nào đó dữ liệu có giá trị từ 59 đến 60 giây, tùy thuộc vào thời điểm chính xác bạn gọi nó.

Ví dụ, nếu một sự kiện xảy ra tại thời điểm = 0,99 giây, thời gian đó sẽ được làm tròn thành t=0 giây. Và nếu bạn gọi MinuteCount() tại thời điểm = 60,1 giây, nó sẽ trả về tổng số cho các sự kiện trong đó t=1,2,3,...60. Vì vậy, sự kiện đầu tiên đó sẽ bị bỏ lỡ, mặc dù về mặt kỹ thuật, nó diễn ra chưa đầy một phút trước.

Trung bình, MinuteCount() sẽ trả về dữ liệu có giá trị 59,5 giây. Và HourCount() sẽ trả về dữ liệu có giá trị 3599,5 giây (một lỗi không đáng kể).

Chúng tôi có thể khắc phục tất cả điều này bằng cách sử dụng thời gian có độ chi tiết dưới giây. Nhưng thú vị là, hầu hết các ứng dụng sử dụng MinuteHourCounter ngay từ đầu không cần mức độ chính xác đó. Chúng tôi sẽ khai thác thực tế này để thiết kế MinuteHourCounter mới nhanh hơn nhiều và sử dụng ít dung lượng hơn. Đó là sự đánh đổi của độ chính xác cho hiệu suất sẽ rất xứng đáng.

Ý tưởng chính là gộp tất cả các sự kiện trong một khoảng thời gian nhỏ lại với nhau và tóm tắt các sự kiện đó với một tổng số duy nhất. Ví dụ: các sự kiện trong một phút qua có thể được chèn vào 60 nhóm rời rạc, mỗi nhóm rộng 1 giây. Các sự kiện trong giờ qua cũng có thể được chèn vào 60 nhóm rời rạc, mỗi nhóm rộng 1 phút.

![img_4](https://user-images.githubusercontent.com/18412533/171006741-07e6fcbf-ae2b-4ea6-a910-cb0675992b00.png)

Sử dụng các nhóm như được hiển thị, các phương thức MinuteCount() và HourCount() sẽ chính xác đến 1 phần trên 60, điều này là hợp lý.*

Nếu cần độ chính xác cao hơn, có thể sử dụng nhiều nhóm hơn để đổi lấy dung lượng bộ nhớ lớn hơn. Nhưng điều quan trọng là thiết kế này có mức sử dụng bộ nhớ cố định, có thể đoán trước được.

\* Tương tự như các giải pháp trước, nhóm cuối cùng sẽ chỉ đầy trung bình một nửa. Với thiết kế này, chúng tôi có thể khắc phục sự đánh giá thấp bằng cách giữ 61 nhóm thay vì 60 và bỏ qua nhóm “đầu vào” hiện tại. Nhưng điều này khiến dữ liệu bị “cũ” một phần. Cách khắc phục tốt hơn là kết hợp nhóm đang xử lý với một phần bổ sung của nhóm cũ nhất để có được số lượng vừa không sai lệch vừa cập nhật. Việc thực hiện này được để lại như một bài tập cho người đọc.

#### Implementing the Time-Bucketed Design

Việc triển khai thiết kế này chỉ với một class sẽ tạo ra rất nhiều mã code phức tạp mà bạn khó có thể hiểu được. Thay vào đó, chúng tôi sẽ làm theo lời khuyên của chúng tôi từ Chương 11, Mỗi nhiệm vụ một lần, và tạo các class riêng biệt để xử lý các phần khác nhau của vấn đề này.

Đối với người mới bắt đầu, hãy tạo một class riêng biệt để theo dõi số lượng trong một khoảng thời gian duy nhất (như giờ cuối). Chúng tôi sẽ gọi nó là TrailingBucketCounter. Về cơ bản, nó là một phiên bản chung của MinuteHourCounter chỉ xử lý một khoảng thời gian. Đây là interface:

```
// A class that keeps counts for the past N buckets of time.
class TrailingBucketCounter {
  public:
    // Example: TrailingBucketCounter(30, 60) tracks the last 30 minute-buckets of time.
    TrailingBucketCounter(int num_buckets, int secs_per_bucket);

    void Add(int count, time_t now);
    // Return the total count over the last num_buckets worth of time
    int TrailingCount(time_t now);
};
```

Bạn có thể thắc mắc tại sao Add() và TrailingCount() lại yêu cầu thời gian hiện tại (time_t now) làm đối số — sẽ không dễ dàng hơn nếu các phương pháp đó chỉ tính thời gian hiện tại time() phải không?

Mặc dù nó có vẻ kỳ lạ, nhưng đi qua trong thời điểm hiện tại có một số lợi ích. Đầu tiên, nó làm cho TrailingBucketCounter trở thành một class “clockless”, nói chung dễ kiểm tra hơn và ít bị lỗi hơn. Thứ hai, nó giữ tất cả các cuộc gọi đến time() bên trong MinuteHourCounter. Với các hệ thống time-sensitive, sẽ rất hữu ích nếu bạn có thể đặt tất cả các cuộc gọi để xem thời gian ở một nơi.

Giả sử TrailingBucketCounter đã được triển khai, MinuteHourCounter rất dễ triển khai:

```
class MinuteHourCounter {
    TrailingBucketCounter minute_counts;
    TrailingBucketCounter hour_counts;

  public:
    MinuteHourCounter() :
        minute_counts(/* num_buckets = */ 60, /* secs_per_bucket = */ 1),
        hour_counts(  /* num_buckets = */ 60, /* secs_per_bucket = */ 60) {
    }

    void Add(int count) {
        time_t now = time();
        minute_counts.Add(count, now);
        hour_counts.Add(count, now);
    }

    int MinuteCount() {
        time_t now = time();
        return minute_counts.TrailingCount(now);
    }

    int HourCount() {
        time_t now = time();
        return hour_counts.TrailingCount(now);
    }
};
```

Mã này dễ đọc hơn nhiều và cũng linh hoạt hơn — nếu chúng tôi muốn tăng số lượng nhóm (để cải thiện độ chính xác nhưng tăng mức sử dụng bộ nhớ), điều đó sẽ dễ dàng thực hiện.

#### Implementing TrailingBucketCounter

Bây giờ tất cả những gì còn lại là triển khai class TrailingBucketCounter. Một lần nữa, chúng tôi sẽ tạo một lớp trợ giúp để giải quyết vấn đề này thêm.

Chúng tôi sẽ tạo một cấu trúc dữ liệu có tên ConveyorQueue có công việc là xử lý các số lượng cơ bản và tổng số của chúng. Lớp TrailingBucketCounter có thể tập trung vào nhiệm vụ di chuyển ConveyorQueue theo khoảng thời gian đã trôi qua.

Đây là giao diện ConveyorQueue:

```
// A queue with a maximum number of slots, where old data "falls off" the end.
class ConveyorQueue {
    ConveyorQueue(int max_items);

   // Increment the value at the back of the queue.
   void AddToBack(int count);

   // Each value in the queue is shifted forward by 'num_shifted'.
   // New items are initialized to 0.
   // Oldest items will be removed so there are <= max_items.
   void Shift(int num_shifted);

   // Return the total value of all items currently in the queue.
   int TotalSum();
};
```

Giả sử class này đã được triển khai, hãy xem TrailingBucketCounter dễ triển khai như thế nào:

```
class TrailingBucketCounter {
    ConveyorQueue buckets;
    const int secs_per_bucket;
    time_t last_update_time; // the last time Update() was called

    // Calculate how many buckets of time have passed and Shift() accordingly.
    void Update(time_t now) {
        int current_bucket = now / secs_per_bucket;
        int last_update_bucket = last_update_time / secs_per_bucket;

        buckets.Shift(current_bucket - last_update_bucket);
        last_update_time = now;
    }
  public:
    TrailingBucketCounter(int num_buckets, int secs_per_bucket) :
        buckets(num_buckets),
        secs_per_bucket(secs_per_bucket) {
    }

    void Add(int count, time_t now) {
        Update(now);
        buckets.AddToBack(count);
    }

    int TrailingCount(time_t now) {
        Update(now);
        return buckets.TotalSum();
    }
};
```

Sự phân chia này thành hai lớp (TrailingBucketCounter và ConveyorQueue) là một ví dụ khác về những gì chúng ta đã thảo luận trong *Chương 11, Mỗi lúc một nhiệm vụ*. Chúng tôi cũng có thể đã thực hiện mà không có ConveyorQueue và triển khai mọi thứ trực tiếp bên trong TrailingBucketCounter. Nhưng bằng cách này, mã dễ tiêu hóa hơn.

#### Implementing ConveyorQueue

Bây giờ tất cả những gì còn lại là triển khai lớp ConveyorQueue:

```
// A queue with a maximum number of slots, where old data gets shifted off the end.
class ConveyorQueue {
    queue<int> q;
    int max_items;
    int total_sum; // sum of all items in q
  public:
    ConveyorQueue(int max_items) : max_items(max_items), total_sum(0) {
    }

    int TotalSum() {
        return total_sum;
    }

    void Shift(int num_shifted) {
        // In case too many items shifted, just clear the queue.
        if (num_shifted >= max_items) {
            q = queue<int>(); // clear the queue
            total_sum = 0;
            return;
        }

        // Push all the needed zeros.
        while (num_shifted > 0) {
            q.push(0);
            num_shifted--;
        }

        // Let all the excess items fall off.
        while (q.size() > max_items) {
            total_sum -= q.front();
            q.pop();
        }
    }

    void AddToBack(int count) {
        if (q.empty()) Shift(1); // Make sure q has at least 1 item.
        q.back() += count;
        total_sum += count;
    }
};
```

Bây giờ chúng ta đã hoàn thành! Chúng tôi có MinuteHourCounter nhanh và tiết kiệm bộ nhớ, cùng với TrailingBucketCounter linh hoạt hơn có thể dễ dàng sử dụng lại. Ví dụ: sẽ khá dễ dàng để tạo một Bộ đếm gần đây linh hoạt hơn có thể đếm một loạt các khoảng thời gian, chẳng hạn như ngày cuối cùng hoặc mười phút qua.

### Comparing the Three Solutions

Hãy so sánh các giải pháp mà chúng ta đã xem xét trong chương này. Bảng sau đây cho thấy kích thước mã code và thống kê hiệu suất (giả sử trường hợp sử dụng có lưu lượng truy cập cao là 100 Add() / giây):

| Giải pháp                         | Dòng code | Phí trên HourCount()             | Sử dụng bộ nhớ                   | Lỗi trong HourCount()
| --------------------------------- | --------- | -------------------------------- | -------------------------------- | ---------------------
| Naive solution                    | 33        | O(#events-per-hour) (~3.6 triệu) | vô hạn                           | 1 phần trên 3600
| Conveyor belt design              | 55        | O(1)                             | O(#events-per-hour) (~5MB)       | 1 phần trên 3600
| Time-bucketed design (60 buckets) | 98        | O(1)                             | O(#events-per-hour) (~500 bytes) | 1 phần trên 60

Lưu ý rằng tổng số lượng mã code cho giải pháp ba lớp cuối cùng của chúng tôi nhiều hơn bất kỳ nỗ lực nào khác. Tuy nhiên, hiệu suất vượt trội hơn nhiều, và thiết kế linh hoạt hơn. Ngoài ra, mỗi lớp riêng lẻ sẽ dễ đọc hơn nhiều. Đây luôn là một thay đổi tích cực: có 100 dòng dễ đọc sẽ tốt hơn 50 dòng không.

Đôi khi, việc chia nhỏ một vấn đề thành nhiều lớp có thể tạo ra sự phức tạp giữa các lớp (điều mà giải pháp một lớp sẽ không có). Tuy nhiên, trong trường hợp này, có một chuỗi sử dụng “tuyến tính” đơn giản từ lớp này sang lớp khác và chỉ một trong các lớp được hiển thị cho người dùng cuối. Nhìn chung, những lợi ích của việc chia nhỏ vấn đề này làm cho điều này trở thành một chiến thắng.

### Summary

Hãy xem lại các bước chúng tôi đã trải qua để đi đến thiết kế MinuteHourCounter cuối cùng. Quá trình này là điển hình của cách các đoạn mã code khác phát triển.

Đầu tiên, chúng tôi bắt đầu bằng cách viết một giải pháp ngờ nghệch (naive solution). Điều này đã giúp chúng tôi nhận ra hai thách thức trong thiết kế: tốc độ và sử dụng bộ nhớ.

Tiếp theo, chúng tôi thử thiết kế "băng chuyền (conveyor belt)". Thiết kế này đã cải thiện tốc độ và việc sử dụng bộ nhớ nhưng vẫn chưa đủ tốt cho các ứng dụng hiệu suất cao. Ngoài ra, thiết kế này rất không linh hoạt: việc điều chỉnh mã code để xử lý các khoảng thời gian khác sẽ tốn rất nhiều công sức.

Thiết kế cuối cùng của chúng tôi đã giải quyết các vấn đề trước đó bằng cách chia nhỏ mọi thứ thành các vấn đề con. Dưới đây là ba lớp chúng tôi đã tạo, theo thứ tự từ dưới lên và bài toán con mỗi lớp đã được giải quyết:

- ConveyorQueue

  Hàng đợi có độ dài tối đa có thể được “thay đổi” và duy trì tổng của nó

- TrailingBucketCounter

  Di chuyển ConveyorQueue theo khoảng thời gian đã trôi qua và duy trì số lượng của một khoảng thời gian duy nhất (gần nhất), với độ chính xác nhất định

- MinuteHourCounter

  Chỉ cần chứa hai TrailingBucketCounters, một cho số phút và một cho số giờ
