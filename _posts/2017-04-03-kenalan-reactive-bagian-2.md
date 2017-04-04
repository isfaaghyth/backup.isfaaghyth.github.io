---
layout:     post
title:      Kenalan dengan Reactive Programming Bagian II
date:       2017-04-02
summary:    
categories: Java, Android
---
Melanjuti [artikel sebelumnya](http://isfaa.me/ghiyath/kenalan-reactive-bagian-1), kali ini kita akan mencoba mempelajari **reactive** programming lebih dalam lagi dengan mencoba menyelesaikan contoh kasus yang ada pada bagian pertama. Contoh kasus nya yaitu _**Multiple Request**_.

Untuk API nya, sudah saya siapkan. Kamu bisa akses disini:
```text
BASE URL:
GET http://isfaaghyth.ga/
GET http://isfaaghyth.ga/detail/{id}
```

Pertama, tambahkan ini di gradle.

{% highlight java %}
compile 'io.reactivex:rxandroid:1.2.1'
compile 'io.reactivex:rxandroid:1.2.1'
{% endhighlight %}
