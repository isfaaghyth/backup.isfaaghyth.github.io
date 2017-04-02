Yii2 adalah salah satu web framework yang sudah melegenda di bahasa pemrograman PHP. Yii2 pun cukup banyak diminati oleh perusahaan dan web developer di Indonesia. Lowongan kerja yang membutuhkan Yii2 pun tidak sedikit bermunculan. Sebagai web framework yang memiliki komunitas kuat, Yii2 lahir dengan sejumlah perbaikan dan penambahan fitur baru. Salah satu kekuatan Yii2 yang lain adalah, dokumentasi yang terstruktur dan lengkap sehingga pengguna Yii v1.x dapat menyesuaikan kembali source code-nya dengan v2.x. Bagi pemula Yii2 memiliki sebuah _cookbook_ yang cukup jelas dan lengkap namun masih dalam Bahasa Inggris.

## Instalasi

Instalasi Yii2 mempunyai 2 metode, yaitu metode menggunakan composer atau manual dari file arsip yang disediakan. Disini saya akan jelaskan bagaimana cara install yii2 melalui Composer.

Pertama, unduh composer di [Get Composer](https://getcomposer.org).

Atau melalui php, seperti ini.

{% highlight php %}
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '669656bab3166a7aff8a7506b8cb2d1c292f042046c5a994c43155c0be6190fa0355160742ab2e1c88d40d5be660b410') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
{% endhighlight %}

Setelah composer di install, silahkan cek apakah composer sudah bisa digunakan atau tidak.

`$ composer`

![First](https://raw.githubusercontent.com/isfaaghyth/blog-tech/gh-pages/images/01.png?token=AGdhd5jmGxC3oD7zLurlFnTDskbvWBNhks5YzLkwwA%3D%3D)

setelah itu, lakukan instalasi untuk Composer Asset Plugin, jalankan perintah ini:

`$ composer global require "fxp/composer-asset-plugin:^1.2.0"`

![Second](https://raw.githubusercontent.com/isfaaghyth/blog-tech/gh-pages/images/02.png?token=AGdhd8nLEztlYnQSbVt2VRnW6qdnAOrKks5YzLk2wA%3D%3D)

Jika Composer Asset Plugin sudah di instal, pindahkan direktori aktif ke direktori `/Library/WebServer/Documents/` lalu lakukan cloning yii2 advanced dengan menjalankan perintah dibawah ini:

`$ composer create-project yiisoft/yii2-app-advanced advanced 2.0.11`

![Third](https://raw.githubusercontent.com/isfaaghyth/blog-tech/gh-pages/images/03.png?token=AGdhd4Y9yrepy1W-rMl0OE0_BO-K49k9ks5YzLlmwA%3D%3D)

*NOTE: _Instalasi melalui composer membutuhkan waktu yang lama, tergantung seberapa cepat koneksi internet yang anda gunakan._*

Setelah proses instalasi, cek apakah folder advanced sudah ada atau tidak.

`$ ls -l`

![Fort](https://raw.githubusercontent.com/isfaaghyth/blog-tech/gh-pages/images/04.png?token=AGdhdzHdS06Saq1Vg0nDlF82kGxnNFIFks5YzLl2wA%3D%3D)

rename folder tersebut dengan nama website anda menggunakan perintah:

`$ mv advanced <nama_project>`

setelah itu, lakukan initialization untuk project anda, jalankan perintah berikut:

`$ php init`

pilih environment [0] Development, lalu ketik 'yes'.

![Five](https://raw.githubusercontent.com/isfaaghyth/blog-tech/gh-pages/images/05.png?token=AGdhd_9UnD8RQEU1IM5Rs7nm-DApOvPRks5YzLl5wA%3D%3D)

lalu akses di browser anda http://localhost/< nama_project >/frontend/web/

![Six](https://raw.githubusercontent.com/isfaaghyth/blog-tech/gh-pages/images/06.png?token=AGdhdy63TZb134ROwPJolp_4eoS933eIks5YzLl_wA%3D%3D)

semoga bermanfaat. :)
