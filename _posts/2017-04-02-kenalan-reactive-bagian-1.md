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

Di ReactiveX, ada banyak operator yang bisa kamu terapkan untuk _Reactive paradigm_ ini. Operator yang paling sering saya gunakan, yaitu:

- SubscribeOn
- ObserveOn
- Subscribe
- Map

Untuk operator lainnya, kamu bisa lihat [disini](http://reactivex.io/documentation/operators.html).

## Kasus Sederhana

Nah, sebenarnya pada saat kapan sih kamu gunakan dan menerapkan **Reactive** ini?

Kasus sederhana yang pernah saya alami, yaitu:

**Multiple Request**

Kasusnya seperti ini, saya memiliki 2 endpoint yang berbeda:

**`GET`** /dataku

```json
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
```

**`GET`** /dataku_lengkap/1

```json
{
   id: 1,
   name: "data B",
   type: "Document",
   {...}
}
```

Di dalam aplikasinya, saya harus menampilkan atribut `name` lengkap dengan `type` nya, sedangkan untuk mendapatkan `type` nya, kita harus melakukan _request_ lagi di endpoint yang berbeda. Dari kasus tersebut, kita dapat mengimplementasikan paradigma ini agar dapat melakukan _multiple request_ secara periodik atau _sequence_.

Contoh kasus kedua, ketika saya melakukan `POST` dan setelah itu data yang ada pada _list_ harus diperbaharui dengan data yang baru `GET` di satu waktu yang sama.

## Implementasi

Sebelum memulai, silahkan tambahkan ReactiveX kedalam _project_ kamu:

{% highlight java %}
compile 'io.reactivex:rxjava:1.1.6'
compile 'io.reactivex:rxandroid:1.2.1'
{% endhighlight %}

Pertama, ita harus tahu dahulu bagaimana cara mendeklarasikannya. Ada beberapa cara untuk pendeklarasian ReactiveX ini.

Cara pertama.

```java
Observable<String> helloWorldObservable = Observable.just("Hai, Selamat Datang.");
```

Cara kedua seperti ini.

```java
Observable<Integer> integerListObservable = Observable.create(new Observable.OnSubscribe<Integer>() {
    @Override public void call(Subscriber<? super Integer> subscriber) {
        subscriber.onNext(1); //data nya
        subscriber.onCompleted();
    }
});
```

Atau dengan object yang kita buat sendiri. Misalkan seperti ini.

```java
public class Pegawai {
   private String nama;
   private String jenisKelamin;

   public Pegawai(String nama, String jenisKelamin) {
      this.nama = nama;
      this.jenisKelamin = jenisKelamin;
   }
   // getter
}
```

Implementasi pada object diatas seperti ini.

```java
Pegawai pegawai = new Pegawai("Isfa", "L");
Observable<Pegawai> pegawaiObservable = Observable.just(pegawai);
```

Sekarang kita coba menggunakan salah satu operator dari ReactiveX ini. Kasus sederhana dengan menampilkan bilangan ganjil dari sebuah data `List()` integer. Kasus tersebut menggunakan operator **map()** untuk melakukan _filtering_ berdasarkan data yang ada dalam menetukan bilangan ganjil atau bukan. Contohnya seperti ini.

```java
StringBuilder stringBuilder = new StringBuilder();

ArrayList<Integer> datas = new ArrayList<>();
datas.add(1);
datas.add(2);
datas.add(3);
datas.add(4);
datas.add(5);

Observable<ArrayList> myObservable = Observable.just(datas);

myObservable.map(new Func1<ArrayList, String>() {
    @Override
    public String call(ArrayList s) {
        for (int i = 0; i < datas.size(); i++) {
            if (datas.get(i)%2 == 1) {
                stringBuilder.append(datas.get(i) + "\n");
            }
        }
        return stringBuilder.toString();
    }
}).subscribe(new Action1<String>() {
    @Override
    public void call(String ganjil) {
        System.out.println(ganjil);
    }
});
```

Dalam penggunaan operator **map()** tersebut, kita memiliki sebuah contoh data dari _variable_ `datas()` dengan tipe data ArrayList(). Setelah itu, `datas()` dimasukkan kedalam observable:

```java
Observable<ArrayList> myObservable = Observable.just(datas);
```

Lalu, `datas()` akan di proses kedalam operator **map()** untuk menentukan bilangan ganjil atau bukan, dan di masukkan kedalam stringBuilder(). Perlu diperhatikan bahwa, operator **map()** akan mengembalikan sebuah nilai (_return value_) yang sesuai dengan tipe data yang diterapkan di `new Func1<>`.

```java
myObservable.map(new Func1<ArrayList, String>() {
   ...
}
```

Data yang dikembalikan oleh **map()** kita akan `subscribe()` dan ditampilkan di `new Action1<>`. Maka hasilnya seperti ini.

```js
1
3
5
```

Itu adalah salah satu contoh penggunaan operator di ReactiveX. Ada banyak operator yang bisa kamu manfaatkan di paradigma ini. Untuk pengenalan ReactiveX cukup sampai disini, pembahasan selanjutnya saya akan mencoba menerapkan ReactiveX untuk request sebuah data di server. Nanti, ReactiveX ini akan dikombinasikan dengan okhttp. Yang belum tahu tentang okhttp, bisa di lihat [disini](https://github.com/square/okhttp).

Ok, cukup sekian. Semoga bermanfaat. :)
