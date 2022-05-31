# Chapter 4: Aesthetics - Tính thẩm mỹ

![Picture1](https://user-images.githubusercontent.com/86230054/170808420-88c7c4d7-d5a5-4c91-9fef-bc586ab5fe15.png)

Chúng ta sẽ dễ đọc và hiểu các đoạn code có tính thẩm mỹ cao. Nếu bạn có thể dễ dàng đọc lướt qua đoạn code của chính mình thì mọi người sẽ càng dễ sử dụng chúng.

> **KEY IDEA**
> 
**Tính nhất quán trong một source code là quan trọng nhất**

## Có 3 nguyên tắc như sau:

- Sử dụng bố cục nhất quán theo các quy định, khuôn mẫu chung.
- Những đoạn code giống nhau về cấu trúc nên nhìn trông giống nhau. 
- Nhóm các dòng code có liên quan thành 1 khối.

## Sau đây là một số phương pháp được chúng tôi tổng hợp
### 1. Ngắt dòng một cách nhất quán và gọn gàng
- Thay vì viết như vậy
```sh
public class PerformanceTester {
   public static final TcpConnectionSimulator wifi = new TcpConnectionSimulator(
     500, /* Kbps */
     80, /* millisecs latency */
     200, /* jitter */
     1 /* packet loss % */);
   public static final TcpConnectionSimulator t3_fiber =
     new TcpConnectionSimulator(
      45000, /* Kbps */
       10, /* millisecs latency */
       0, /* jitter */
       0 /* packet loss % */);
   public static final TcpConnectionSimulator cell = new TcpConnectionSimulator(
     100, /* Kbps */
     400, /* millisecs latency */
     250, /* jitter */
     5 /* packet loss % */);
}
```
-  Thì chúng ta sẽ ngắt dòng để các instance được khai báo trông gọn gàng và nhất quán hơn
```sh
public class PerformanceTester {
   public static final TcpConnectionSimulator wifi =
     new TcpConnectionSimulator(
       500, /* Kbps */
       80, /* millisecs latency */
       200, /* jitter */
       1 /* packet loss % */);
   public static final TcpConnectionSimulator t3_fiber =
     new TcpConnectionSimulator(
      45000, /* Kbps */
       10, /* millisecs latency */
       0, /* jitter */
       0 /* packet loss % */);
   public static final TcpConnectionSimulator cell =
     new TcpConnectionSimulator(
       100, /* Kbps */
       400, /* millisecs latency */
       250, /* jitter */
       5 /* packet loss % */);
}
```

### 2. Sử dụng hàm để nhóm các đoạn code giống nhau để:
- Tránh lặp code.
- Dễ dàng thay đổi.
- Dễ test hơn.

### 3. Căn chỉnh theo dạng cột
* Ví dụ:
```sh
CheckFullName("Doug Adams"  , "Mr. Douglas Adams"   , "");
CheckFullName(" Jake Brown ", "Mr. Jake Brown III"  , "");
CheckFullName("No Such Guy" , ""                    , "no match found");
CheckFullName("John"        , ""                    , "more than one result");
```
- Cách này giúp chúng ta đọc và hiểu code nhanh hơn. Dễ tìm ra lỗi typo.
- _Nhưng có nhiều lập trình viên không thích nó vì cần nhiều thời gian để triển khai. Khi thay đổi một chỗ nhỏ dẫn đến diff khá lớn trong git. Lời khuyên của chúng tôi là cứ thử phương pháp này. Theo kinh nghiệm của chúng tôi, việc này không tốn nhiều công sức như những gì mà các lập trình viên lo sợ. Và nếu thấy thật sự tốn thời gian thì không cần thiết dùng cách này._


### 4. Chọn một kiểu sắp xếp danh sách phù hợp và dùng nó một cách nhất quán
**Có 2 kiểu là:**
1. Sắp xếp theo mức độ quan trọng
2. Sắp xếp theo alphabet
   
### 5. Tổ chức các đoạn code thành nhiều khối.
- Bộ não chúng ta suy nghĩ theo mô hình nhóm và thứ bậc, vì vậy bạn có thể giúp người đọc nhanh chóng hiểu code của bạn bằng cách sắp xếp nó theo cách đó.

- Ví dụ: Thay vì viết như vậy:

```sh
class FrontendServer {
 public:
   FrontendServer();
   void ViewProfile(HttpRequest* request);
   void OpenDatabase(string location, string user);
   void SaveProfile(HttpRequest* request);
   string ExtractQueryParam(HttpRequest* request, string param);
   void ReplyOK(HttpRequest* request, string html);
   void FindFriends(HttpRequest* request);
   void ReplyNotFound(HttpRequest* request, string error);
   void CloseDatabase(string location);
   ~FrontendServer();
};
```
- Thì hãy viết thành:

```sh
class FrontendServer {
 public:
   FrontendServer();
   ~FrontendServer();
   
   // Handlers
   void ViewProfile(HttpRequest* request);
   void SaveProfile(HttpRequest* request);
   void FindFriends(HttpRequest* request);
   
   // Request/Reply Utilities
   string ExtractQueryParam(HttpRequest* request, string param);
   void ReplyOK(HttpRequest* request, string html);
   void ReplyNotFound(HttpRequest* request, string error);
   
   // Database Helpers
   void OpenDatabase(string location, string user);
   void CloseDatabase(string location);
};
```

### 6. Tách code thành nhiều khối nhỏ
- Để nhóm các dòng code của một chức năng lại với nhau và phân biệt chúng với các chức năng khác.
- Giúp người đọc trực quan và tập trung hơn.
- Giúp liên kết chức năng từ đoạn này sang đoạn khác.

- Ví dụ:

```sh
def suggest_new_friends(user, email_password):
   # Get the user's friends' email addresses.
   friends = user.friends()
   friend_emails = set(f.email for f in friends)
   
   # Import all email addresses from this user's email account.
   contacts = import_contacts(user.email, email_password)
   contact_emails = set(c.email for c in contacts)
   
   # Find matching users that they aren't already friends with.
   non_friend_emails = contact_emails - friend_emails
   suggested_friends = User.objects.select(email__in=non_friend_emails)
   
   # Display these lists on the page.
   display['user'] = user
   display['friends'] = friends
   display['suggested_friends'] = suggested_friends
   
   return render("suggested_friends.html", display)
```

## Tóm lại
- Mọi người đều thích đọc source code có tính thẩm mỹ cao. Bằng cách viết code một
một cách nhất quán, có ý nghĩa, giúp chúng ta đọc dễ dàng và nhanh hơn.
- Dưới đây là các kỹ thuật cụ thể mà chúng tôi đã đưa ra:
1. Nếu nhiều đoạn code đang làm những việc tương tự nhau, hãy đưa chúng về một class hoặc function chung.
2. Căn chỉnh đoạn code thành dạng cột, có thể giúp code dễ đọc và dễ hiểu hơn.
3. Chọn một phong cách nhất định và dùng nó xuyên suốt dự án.
4. Sử dụng các dòng trống để chia các khối lớn thành các đoạn nhỏ hợp lý.    
    
