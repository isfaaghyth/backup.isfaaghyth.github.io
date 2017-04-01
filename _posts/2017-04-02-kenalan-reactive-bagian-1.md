---
layout:     post
title:      Kenalan dengan Reactive Programming Bagian I
date:       2017-04-02
summary:    
categories: Java, Android
---
**Reactive Programming ?**, sebelum kita belajar cara implementasinya, kita kenalan dulu yuk dengan Reactive ini. Menurut wikipedia:

`In computing, reactive programming is an asynchronous programming paradigm oriented around data streams and the propagation of change. This means that it should be possible to express static (e.g. arrays) or dynamic (e.g. event emitters) data streams with ease in the programming languages used, and that the underlying execution model will automatically propagate the changes through the data flow.`

Singkatnya, Reactive merupakan sebuah paradigma pemrograman yang berorientasi pada _**data-flow**_. Misalkan, kita orientasikan pada perubahan data, mengubah stream integer ke string ataupun sebaliknya, atau melakukan penyeleksian pada data (_filtering_). Dan juga, dapat melakukan eksekusi pengelolaan data secara dinamis dan _automatically_ secara [Background Thread](http://www.computerhope.com/jargon/b/backthre.htm). Untuk lebih lengkap, kamu bisa baca [disini](https://en.wikipedia.org/wiki/Reactive_programming).

Untuk pengimplementasiannya, kita menggunakan _library_ [ReactiveX](http://reactivex.io). Penjelasan sepintas mengenai _library_ ini.

`ReactiveX is a library for composing asynchronous and event-based programs by using observable sequences. It extends the observer pattern to support sequences of data and/or events and adds operators that allow you to compose sequences together declaratively while abstracting away concerns about things like low-level threading, synchronization, thread-safety, concurrent data structures, and non-blocking I/O.`

Yang hebat dari library ini, ReactiveX mendukung dibeberapa bahasa pemrograman. Kamu bisa cek di situs resminya disini [www.reactivex.io](http://www.reactivex.io).

Di ReactiveX ada beberapa operator yang wajib kita ketahui untuk mengimplementasi _Reactive paradigm_ ini. Ada banyak operator yang disediakan untuk ReactiveX ini. Operator yang paling sering saya gunakan, yaitu:

- SubscribeOn
- ObserveOn
- Subscribe
- Map

Untuk operator lainnya, kamu bisa lihat [disini](http://reactivex.io/documentation/operators.html).

### Kasus Sederhana

Nah, sebenarnya pada saat kapan sih kamu gunakan dan menerapkan **Reactive** ini?

Kasus sederhana yang pernah saya alami, yaitu:

Multiple Request

Kasusnya seperti ini, saya memiliki 2 endpoint yang berbeda:

GET /dataku

{% highlight json %}
[
   {
      id: 0,
      name: "data A",
   },
   {
      id: 1,
      name: "data B"
   },
   {...}
]
{% endhighlight %}

GET /dataku_lengkap/1

{% highlight json %}
{
   id: 1,
   name: "data B",
   type: "Document",
   {...}
}
{% endhighlight %}

Di aplikasi saya, saya harus menampilkan atribut `name` lengkap dengan `type` nya, sedangkan untuk mendapatkan `type` nya, kita harus melakukan _request_ lagi di endpoint yang berbeda. Dari kasus tersebut, kita dapat mengimplementasi paradigma ini agar dapat melakukan _multiple request_ secara periodik atau _sequence_.


### Implementasi

Sebelum memulai, silahkan tambahkan ReactiveX kedalam _project_ kamu:

{% highlight java %}
compile "io.reactivex.rxjava2:rxjava:2.0.8"
{% endhighlight %}

Pertama, ita harus tahu dahulu bagaimana cara mendeklarasikannya. Ada beberapa cara untuk pendeklarasian ReactiveX ini.

Cara pertama.

{% highlight java %}
Observable<String> belajarRxString = Observable.just("Hai, Selamat Datang.");
{% endhighlight %}

Atau seperti ini.

{% highlight java %}
Observable<Integer> integerListObservable = Observable.create(new Observable.OnSubscribe<Integer>() {
    @Override public void call(Subscriber<? super Integer> subscriber) {
        subscriber.onNext(1); //data nya
        subscriber.onCompleted();
    }
});
{% endhighlight %}
