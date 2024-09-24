---


title:  "Chuyện bounty ở công ty X"

date:   2019-12-09 21:10:58

author: severus

categories: security

tags: privacy security bugbounty atm card finance

---

Một buổi sáng đẹp trời cậu em Phiêu Lãng gửi tôi một đường dẫn về một trang bugbounty mới của Việt Nam, tôi thấy thật tuyệt vì đây là nơi nhiều người anh em xã hội của tôi đang làm, và họ làm một cách nghiêm túc về an toàn thông tin. Tôi đã đi dạo và phát hiện ra nhiều thứ, trong bài blog này tôi chỉ tập trung về vấn đề bảo mật ứng dụng android của công ty X.

## Vấn đề bảo mật trong android
Không như những lầm tưởng rằng bảo mật trên ứng dụng android chỉ tập trung vào các api endpoint, chính bản thân các ứng dụng android cũng tiềm ẩn nhưng nguy cơ bị tấn công local từ các ứng dụng khác hoặc physical attack. Tuy nhiên, các kịch bản tấn công ứng dụng android ngoài rce thì hầu hết tất cả đều cần user interactive - nghĩa là ít nhất cần chạy môt malicious app hoặc physical access vào thiết bị. Do bề mặt attack hạn chế như vậy, các best practices hầu như chỉ tập trung vào vấn đề data local storage và unauthorized access từ một ứng dụng khác cũng như chống dịch ngược các hằng số.

## Bảo mật trong ứng dụng của công ty X
Tôi thường hay report cho các anh em xã hội một phần vì tình cảm, một phần muốn giao lưu quan hệ, với công ty X tôi cũng nghĩ thế.

Ứng dụng của họ khá hay, nhưng tôi không quan tâm tới phần endpoint vì đây là lần đầu tôi quyết tâm xem ứng dụng một cách riêng biệt với các thành phần khác của nó như web server, user interactive... Mục tiêu của tôi là mở ứng dụng mà không cần vân tay pincode.

Tôi đạt được mục đích đó bằng cách analyze các activity với công cụ rất thân thiện [objection](https://github.com/sensepost/objection). Thú thật tôi không có nhiều kinh nghiệm mobile, đây là lần đầu tiên tôi nghiêm túc xem ứng dụng là thành phần cần đánh giá thay vì các đầu endpoint như trước đây.

Tôi thử tay từng cái activity thì phát hiện ra có 9 activities có thể gọi mà không cần đăng nhập, lúc này ứng dụng sẽ load lên giao diện cùng với session đang có trong local storage. Trong 9 activities đó có 2 activities cho ra kết quả của người dùng với dữ liệu thật.

### Activity 1: Leak số thẻ của ứng dụng X và 4 số cuối thẻ của người dùng thêm vào
Activity này rất hay, không cần đăng nhập chỉ cần chạy adb shell am start app-X/activity-Y sẽ hiện ra màn hình với số dư thực trong tài khoản. Ở đây cần nói rõ rằng dù ứng dụng X có valid session hay không thì vẫn sẽ hiện ra số dư tại thời điểm sync tài khoản gần nhất, nếu có valid session thì thì có thể thêm cả thẻ khác. Tôi chỉ dừng lại ở đó, và tất nhiên đối với tôi, đó là lỗi khá lớn nhưng với đội ngũ triaged của công ty X thì không, nó chỉ là low. Tôi phát hiện ra rằng sang tab kế tiếp tôi có thể nhìn thấy cả số thẻ của ứng dụng X và 4 số cuối thẻ của tôi. Ngay lập tức tôi report cho công ty X mà không viết thêm app poc remote.

### Activity 2: Leak yêu cầu chia sẻ tiền và lịch sử chia sẻ tiền
Tôi khá là ngạc nhiên vì activity này hoàn toàn hiện ra mà không có bất cứ một yêu cầu authentication nào. Nếu valid session nó sẽ khiến tôi có thể yêu cầu chuyển tiền lẫn xem lịch sử chia sẻ tiền. Đối với bug này tôi cho rằng nó chung một root cause nên tôi không submit thành 2 bug, đối với tôi, các bug có chung root cause hay solution tôi chỉ submit vào 1 bug, đó là đạo làm security của tôi (tôi học của google).

### Các yêu cầu tấn công thành công
- Cần một malicious app được cài trên máy nạn nhân hoặc physical access để gọi activities của app X.
- Đối với app vẫn đang còn session valid, sẽ full control tính năng, nếu cần thêm otp từ đt thì malicious app request contact permission hoặc attacker control điện thoại với sim là đủ. Nếu không valid session thì vẫn xem được số dư, số thẻ của ứng dụng X và 4 số cuối thẻ của nạn nhân với tên ngân hàng ứng với số thẻ.

### Câu trả lời từ triaged members của công ty X
Tôi không muốn phán xét hay đưa ra nhận định nào, nhưng với lương tâm nghề nghiệp tôi không thể chấp nhận một lỗi có thể bypass được pincode như vậy lại có thể đánh giá là low serverity. Vì sao?
- Với một tổ chức tài chính, anh để lộ thẻ người dùng, số dư, số cuối thẻ cá nhân rồi anh nói trường hợp hi hữu đó vẫn chỉ xảy ra ở một số user cho mượn/mất đt mà app của anh vẫn không thể protect được. Như vậy tổ chức của anh có đáng tin cậy để người dùng tiếp tục sử dụng sau khi anh thờ ơ với một bug như vậy.

Tôi không muốn tranh luận với công ty X nữa, tôi đã gửi email đề nghị xóa tài khoản và không nhận bounty cho bug android của công ty X. Có lẽ đây là lần cuối cùng tôi submit bug cho một chương trình bugbounty của Việt Nam. Tôi trích một comment trong flow bug này và close bugs của công ty X tại đây.

- Việc kết luận về serverity của lỗ hổng được đưa ra sau khi toàn bộ team đã họp chứ không phải ý kiến chủ quan của 1 cá nhân. Về việc data mã số thẻ: mình đính chính đây mã số thẻ mềm dùng để nhận tiền của [app X], mã thẻ X cứng là một số khác. Hơn nữa số thẻ X không được coi là dữ liệu mức high (trừ trường hợp có cách tấn công leak số lượng lớn data dữ liệu thẻ X).

## Vài lời kết
Tôi tranh cãi không phải vì tiền mà vì tôi không thể chấp nhận một đơn vị tài chính lộ rõ lỗi rồi lại đổ cho người dùng bất cẩn để mất/mượn điện thoại dẫn đến lộ thông tin, lại càng không thể chấp nhận với lý thuyết đó tất cả các ứng dụng hiện tại trong mobile ngoài rce thì chỉ cài app thứ ba hoặc user tự exploit không được tính là lỗi. Lộ một người cũng là lộ, không thể viện cớ lộ ít, lộ nhiều mà bỏ qua mức độ nhạy của dữ liệu.

Tôi rất ngưỡng mộ anh quyết định bounty, nhưng không vì thế mà tôi chấp nhận một lỗi lộ data tài chính người dùng là low do cách khai thác rất hạn chế trong mobile attack surface.

Lỗi của tôi rất lớn là không thể code một ứng dụng và đọc kĩ code của ứng dụng để làm chain attack với khả năng người dùng cài app chứa mã độc và bị tấn công lấy thông tin. Sau lần này, tôi sẽ nghiêm túc viết các poc như [bagipro](https://hackerone.com/bagipro) thần thánh, hay ít nhất là không bị cứng họng khi yêu cầu poc từ app khác chứ không phải adb command :).
