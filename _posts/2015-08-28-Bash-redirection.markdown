---

title: "Bash redirection"

date: 2015-08-28 14:55:50

comments: true

categories: linux

tags: linux bash redirection
---

Hôm rồi tác giả có vá tập tin installer viết bằng bash với lewtds, thấy
vấn đề redirect trong linux khá thú vị với case như sau, chắc hẳn giờ sẽ
có câu trả lời cho khối thứ,


# sudo và redirect,


Ta có 2 trường hợp sau đây,

trong terminal user dragon

{% highlight bash linenos%} sudo -u git cat file1 > file2 {% endhighlight %} 
{% highlight bash linenos%} ls -l file2 : dragon:user {% endhighlight %}

Bọc trong một kịch bản
{% highlight bash linenos%} sudo sh testfile {% endhighlight%}

{% highlight bash linenos%} ls -l file2 : root:root {% endhighlight %}


Chuyện gì đã xảy ra trong quá trình redirect ?


# Câu chuyện bắt đầu,

Redirect là quá trình thay đổi đầu vào và ra của hệ thống linux, mặc
định là stdout, stderr, stdin. Trong các shell, ba tập tin kia có file
descriptor lần lượt là: 1, 2, 0 để đọc, trả kết quả, lỗi cho người dùng
trên shell,


Khi dùng '>' hoặc '<' sẽ thay đổi đầu vào ra của shell tới lệnh cần
chuyển;

vd: echo xxx > file1, lúc này thay vì đầu ra là stdout, các kết quả của
đầu ra sẽ ghi vào file1. 


Chi tiết hơn một chút, điều gì xảy ra đã thay đổi file owner của đầu ra
?


Xem xét lại môi trường shell đang dùng, rõ ràng redirect và uid của tiến
trình đang chạy không liên quan nhau, tiến trình chỉ tạo ra kết quả, còn
redirect sẽ tạo ra đích đến cùng với file attributes, nhưng redirect do
shell tạo ra nên kéo theo hệ quả file attributes cũng sẽ do shell quyết
định. Sudo một mã kịch bản sẽ đi vào shell của root trong khi sudo trên
terminal sẽ đi vào shell của user đang chạy terminal đó.


Rồi tới đây câu hỏi vì sao sudo echo xxx > /etc/xxx : permission denied
đã có câu trả lời.


Credits: Severus, lewtds




