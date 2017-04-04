---
layout:     post
title:      Kenalan dengan Reactive Programming Bagian II
date:       2017-04-02
summary:    
categories: Java, Android
---
Melanjutkan [artikel sebelumnya](http://isfaa.me/ghiyath/kenalan-reactive-bagian-1), kali ini kita akan mencoba mempelajari **reactive** programming lebih dalam lagi dengan mencoba menyelesaikan contoh kasus yang ada pada bagian pertama. Contoh kasus nya yaitu _**Multiple Request**_.

Untuk API nya, sudah saya siapkan. Kamu bisa akses disini:
```text
BASE URL:
GET http://isfaaghyth.ga/
GET http://isfaaghyth.ga/detail/{id}
```

Tambahkan ini di gradle.

{% highlight java %}
compile 'io.reactivex:rxjava:1.1.6'
compile 'io.reactivex:rxandroid:1.2.1'
compile 'com.google.code.gson:gson:2.8.0'
compile 'com.squareup.okhttp3:okhttp:3.6.0'
{% endhighlight %}


## Memulai

Pertama, tambahkan _**BASEURL**_ di build.gradle (module: app).

```java
android {
   ...
   defaultConfig {
      ...
      buildConfigField("String", "BASE_URL", "\"http://isfaaghyth.ga\"")
   }
}
```

Lalu setelah itu, buat sebuah class URLs.java
```java
public class URLs {
    private static String URL_MAIN = BuildConfig.URI;
    private static String URL_DETAIL = "/detail/";

    public static String getUrlMain() {
        return URL_MAIN;
    }

    public static String getUrlDetail(int id) {
        return URL_MAIN + URL_DETAIL + id;
    }
}
```

Dari JSON yang akan kita coba di [isfaaghyth.ga](http://isfaaghyth.ga/), buatlah sebuah model untuk DataModel dan DataDetailModel.

Untuk DataModel.java
```java
public class DataModel {
    @SerializedName("data") private List<Data> datas;

    public List<Data> getDatas() {
        return datas;
    }

    public static class Data {
        @SerializedName("id") private int id;
        @SerializedName("name") private String name;

        public int getId() {
            return id;
        }

        public String getName() {
            return name;
        }
    }
}
```

Sedangkan untuk DetailDataModel.java, seperti ini.
```java
public class DetailDataModel {
    @SerializedName("status") private String status;
    @SerializedName("data") private Data data;

    public String getStatus() {
         return status;
    }

    public Data getData() {
         return data;
    }

    public static class Data {
        @SerializedName("id") private int id;
        @SerializedName("name") private String name;
        @SerializedName("position") private String position;

        public int getId() {
            return id;
        }

        public String getName() {
            return name;
        }

        public String getPosition() {
            return position;
        }
    }
}
```

Untuk menangani _Request Time Out_ pada OkHttp, buat sebuah class OkHttpTimeOut.java
```java
public class OkHttpTimeOut {
    protected static final OkHttpClient client = new OkHttpClient.Builder()
            .connectTimeout(10000L, TimeUnit.MILLISECONDS)
            .readTimeout(10000L, TimeUnit.MILLISECONDS)
            .build();
}
```

Lalu, setelah itu buat sebuah class RxGetDatas.java. Class ini yang akan menangani proses `request()` menggunakan OkHttp dan mengimplementasikan observable terhadap `response()` yang diterima.

```java
public class RxGetDatas extends OkHttpTimeOut {

    public static Observable<String> getDatas() {
        Request request = new Builder().build(URLs.getUrlMain());
        return OkHttp.streamStrings(client, request);
    }

    public static Observable<String> getDatasById(int id) {
        Request request = new Builder().build(URLs.getUrlDetail(id));
        return OkHttp.streamStrings(client, request);
    }

    public static class Builder {
        public Request build(String URL) {
            return new Request.Builder().get().url(URL).build();
        }
    }

}
```
