---
layout:     post
title:      Kenalan dengan Reactive Programming Bagian II
date:       2017-04-04
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
compile 'com.jakewharton:butterknife:8.5.1'
compile 'com.squareup.okhttp3:okhttp:3.6.0'
{% endhighlight %}

`CATATAN: Silahkan set lambda agar memudahkan kamu mengimplementasi rx ini, dan jangan lupa tambahkan permission Internet di Manifest`.

## Memulai

Struktur _project_ akhir.

![final](https://raw.githubusercontent.com/isfaaghyth/isfaaghyth.github.io/master/images/02.png?token=AGdhdwf17S5b_43962pWEKoUKI9P_o5Wks5Y7N58wA%3D%3D)

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

Lalu, setelah itu buat sebuah class RxGetDatas.java. Class ini yang akan menangani proses **request** menggunakan OkHttp dan mengimplementasikan observable terhadap **response** yang diterima.

```java
public class RxGetDatas extends OkHttpTimeOut {

    public static Observable<String> getDatas() {
        Request request = new Builder().build(URLs.getUrlMain());
        return OkHttp.streamStrings(client, request);
    }

    public static Observable<String> getDataById(int id) {
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
NOTE: Untuk class OkHttp.java, kamu bisa dapatkan di [OkHttp.java](https://github.com/isfaaghyth/RxIdentitasku/blob/master/app/src/main/java/app/isfaaghyth/identitasku/network/OkHttp.java). Class tersebut saya sudah _bundle_ menjadi satu untuk beberapa _method_ yang kita butuhkan untuk rx.

Selanjutnya, buatlah sebuah layout seperti ini.

![gambar](https://raw.githubusercontent.com/isfaaghyth/isfaaghyth.github.io/master/images/01.png?token=AGdhd7-lV5EHvREGPfobNXuhdxGJ3-LVks5Y7NypwA%3D%3D)

activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="app.isfaaghyth.identitasku.MainActivity">

    <TextView
        android:id="@+id/txt_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Name"
        android:textSize="17sp"
        android:textStyle="bold"/>

    <TextView
        android:id="@+id/txt_company"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Position." />

</LinearLayout>
```

Di class MainActivity.java, kita akan melakukan subscriber terhadap dua buah observable yang sudah kita buat sebelumnya di class RxGetDatas.java.

Silahkan binding kedua TextView terlebih dahulu.
```java
@BindView(R.id.txt_name) TextView txtName;
@BindView(R.id.txt_company) TextView txtCompany;

@Override protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ButterKnife.bind(this);
}
```

Lalu, deklarasikan `subscriber()`, `gson()`, dan `USER_ID`. `subscriber()` berfungsi untuk menerima pembaharuan data dari observable, `gson()` untuk melakukan parsing data json yang kita _request_, sedangkan USER_ID sebuah variabel statik untuk id untuk request data detail pada API [www.isfaaghyth.ga/detail/{id}](www.isfaaghyth.ga/detail/).

```java
private Subscription subscriber = new CompositeSubscription();
private Gson gson = new GsonBuilder().create();
private static int USER_ID = 2;
```

Setelah itu, kita akan menggunakan operator **zip()** untuk menggabungkan 2 observable, yaitu `getDatas()` dan `getDataById()`.
```java
Observable<List<String>> ambilSemuaData = Observable.zip(
                RxGetDatas.getDatas(),
                RxGetDatas.getDataById(USER_ID), (s, s1) -> {
                    List<String> results = new ArrayList<>();
                    results.add(s);
                    results.add(s1);
                    return results;
                }
        );
```
`ambilSemuaData()` akan mengembalikan semua nilai berbentuk `List()`. Dimana index ke 0 untuk hasil request dari `getDatas()` dan index ke 1 untuk `getDataById()`.

Selanjutnya, kita akan melakukan `subscriber` terhadap `ambilSemuaData()` untuk mengelola data yang diterima atau kesalahan yang ada.

```java
subscriber = ambilSemuaData.subscribeOn(Schedulers.io())
            .observeOn(AndroidSchedulers.mainThread())
            .subscribe(this::onSuccess, this::onError);
```

Data yang diterima akan di kelola oleh _method_ `onSuccess(T data)`, sedangkan kesalahan yang ada akan di kelola oleh _method_ `onError(Throwable err)`.

method onSuccess()

```java
private void onSuccess(List<String> hasil) {
     String semuaData = hasil.get(0);
     String detailData = hasil.get(1);

     DataModel data = gson.fromJson(semuaData, DataModel.class);
     txtName.setText(data.getDatas().get(USER_ID - 1).getName());

     DetailDataModel dataa = gson.fromJson(detailData, DetailDataModel.class);
     txtCompany.setText(dataa.getData().getPosition());
}
```

method onError()

```java
private void onError(Throwable err) {
     Log.d("ERR", err.getMessage());
}
```

maka di class MainActivity.java akan seperti ini

```java
public class MainActivity extends AppCompatActivity {

    @BindView(R.id.txt_name) TextView txtName;
    @BindView(R.id.txt_company) TextView txtCompany;

    private Subscription subscriber = new CompositeSubscription();
    private Gson gson = new GsonBuilder().create();
    private static int USER_ID = 2;

    @Override protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ButterKnife.bind(this);

        Observable<List<String>> ambilSemuaData = Observable.zip(
                RxGetDatas.getDatas(),
                RxGetDatas.getDatasById(USER_ID), (s, s1) -> {
                    List<String> results = new ArrayList<>();
                    results.add(s);
                    results.add(s1);
                    return results;
                }
        );

        subscriber = ambilSemuaData.subscribeOn(Schedulers.io())
                    .observeOn(AndroidSchedulers.mainThread())
                    .subscribe(this::onSuccess, this::onError);
    }

    private void onSuccess(List<String> strings) {
        String semuaData = strings.get(0);
        String detailData = strings.get(1);

        DataModel data = gson.fromJson(semuaData, DataModel.class);
        txtName.setText(data.getDatas().get(USER_ID - 1).getName());

        DetailDataModel dataa = gson.fromJson(detailData, DetailDataModel.class);
        txtCompany.setText(dataa.getData().getPosition());
    }

    private void onError(Throwable err) {
        Log.d("ERR", err.getMessage());
    }

    @Override protected void onDestroy() {
        super.onDestroy();
        subscriber.unsubscribe();
    }
}
```

Pada method `onSuccess()`, kita akan mengelola data dari `List<String> hasil`. Data yang ada pada `List<> hasil` akan disesuaikan dengan data yang kita deklarasikan diawal terhadap index ke 0 dan index 1. Lalu, lakukan parsing terhadap data tersebut dengan gson lalu tampilkan kedalam TextView.

Silahkan jalankan, dan tadaa. Kedua data dari API yang berbeda dapat ditampilkan dalam waktu yang sama.

**[Project on Github](https://github.com/isfaaghyth/RxIdentitasku)**

Implementasi reactive ini saya tulis secara _traditional code_ atau 'kurang efisien' yang diharapkan kamu bisa memahami lebih mudah tentang paradigma ini. Tapi tenang, pembahasan selanjutnya, kita akan coba belajar tentang MVP pattern. Di pembahasan nanti, kode yang sudah kita buat ini akan di _refactor_ kedalam MVP pattern.

Semoga bermanfaat :)
