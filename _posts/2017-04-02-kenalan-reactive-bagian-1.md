---
layout:     post
title:      Kenalan dengan Reactive Programming Bagian I
date:       2017-04-02
summary:    
categories: Java, Android
---
**Reactive Programming ?** sebelum kita belajar cara implementasinya, kita kenalan dulu yuk dengan ReactiveX ini. Menurut wikipedia:

`In computing, reactive programming is an asynchronous programming paradigm oriented around data streams and the propagation of change. This means that it should be possible to express static (e.g. arrays) or dynamic (e.g. event emitters) data streams with ease in the programming languages used, and that the underlying execution model will automatically propagate the changes through the data flow.`

singkatnya, Reactive merupakan sebuah paradigma pemrograman atau _pattern_ yang orientasinya pada data flow. Misalkan, kita orientasikan pada perubahan data, mengubah stream integer ke string ataupun sebaliknya, atau melakukan penyeleksian pada data (_filtering_). Dan juga, dapat melakukan eksekusi pengelolaan data secara dinamis dan _automatically_ secara [Background Thread](http://www.computerhope.com/jargon/b/backthre.htm). Untuk lebih lengkap, kamu bisa baca [disini](https://en.wikipedia.org/wiki/Reactive_programming).

Agar mudah dipahami, sekarang kita coba implementasikan dan belajar Reactive Programming. Untuk pengimplementasiannya, kita menggunakan _library_ [ReactiveX](http://reactivex.io). Penjelasan sepintas mengenai _library_ ini:

`ReactiveX is a library for composing asynchronous and event-based programs by using observable sequences.

It extends the observer pattern to support sequences of data and/or events and adds operators that allow you to compose sequences together declaratively while abstracting away concerns about things like low-level threading, synchronization, thread-safety, concurrent data structures, and non-blocking I/O.`

Yang hebat dari library ini, ReactiveX mendukung dibeberapa bahasa pemrograman. Kamu bisa cek di situs resminya disini [www.reactivex.io](www.reactivex.io).

Sebelum memulai, silahkan tambahkan ReactiveX kedalam _project_ kamu:

{% highlight xml %}
compile "io.reactivex.rxjava2:rxjava:2.x.y"
{% endhighlight %}


### Contoh Sederhana

Pertama, kita harus tahu dulu bagaimana cara mendeklarasinya. Ada beberapa cara untuk pendeklarasian ReactiveX ini.

Pertama.

{% highlight java %}
Observable<String> belajarRxString = Observable.just("Hai, Selamat Datang.");
{% endhighlight %}

Atau, seperti ini.

{% highlight java %}
Observable<Integer> integerListObservable = Observable.create(new Observable.OnSubscribe<Integer>() {
    @Override public void call(Subscriber<? super Integer> subscriber) {
        subscriber.onNext(1); //data nya
        subscriber.onCompleted();
    }
});
{% endhighlight %}
