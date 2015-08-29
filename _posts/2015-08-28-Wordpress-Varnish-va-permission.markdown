---
layout: post
title: "Wordpress, Varnish và permission"
date: 2015-08-28 12:55:50
comments: true
catergories: jekyll update
---
# Wordpress chmod:

Wordpress có thể xem là một hệ thống con tron chính cấu trúc của nó. Ở
bài trước, tôi có viết về php-fpm và directory permission, tuy nhiên đối
với wordpress này sẽ có một thay đổi nhỏ ở thư mục uploads.

Kiểm tra đoạn code sau, ta thấy

{% highlight php startinline %}
// Set correct file permissions
$stat = @ stat( dirname( $new_file ) );
$perms = $stat['mode'] & 0007777;
$perms = $perms & 0000666;
@ chmod( $new_file, $perms );
{% endhighlight %}

Nếu thư mục upload là 701 kết quả chmod sau cùng sẽ trở thành 600, điểu
này hoàn toàn không đúng khi nginx bắt buộc phải đọc được static
content,
Đấy, wordpress có tư duy khá tốt khi chấp nhận kế thừa directory
permission, tuy nhiên nếu không để ý, một sysadmin sẽ cãi vã với
developer vì một cái này đấy :)

# Varnish cache vs Wordpress:

Làm gì thì laverage browser cache vẫn không đúng khi sử dụng đối với
wordpress, thêm nữa khi sử dụng cache của nginx thì varnish sẽ không
thấy được tập tin static, mã lỗi 404 trên toàn bộ static content.

# Nginx tweak problem:

Khi sử dụng nginx và varnish, việc routing là điểu cần thiết cho cả 2

{% highlight bash %}
if ($host !~ ^(example.com|www.example.com)$ ) {
          return 444;
}
{% endhighlight %}

Mã trên chống việc scan ip cho webserver khi không đúng host, tuy nhiên
chỉ cần quên định nghĩa thì varnish sẽ báo 503 ngay thay vì phải reject
request,
