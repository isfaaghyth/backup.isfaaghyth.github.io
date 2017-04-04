---
layout:     post
title:      Cara Instalasi RBAC pada Yii2
date:       2017-04-01
summary:    
categories: Web
---
Kali ini saya akan menjelaskan cara menambah RBAC pada _project_ yii2. Sebelum itu, pastikan composer dan web server anda berjalan dengan baik.

**RBAC?** menurut wikipedia:
`In computer systems security, role-based access control (RBAC) is an approach to restricting system access to authorized users. It is used by the majority of enterprises with more than 500 employees, and can implement mandatory access control (MAC) or discretionary access control (DAC). RBAC is sometimes referred to as role-based security.`

Intinya, RBAC merupakan pusat pengaturan terhadap user yang berperan di aplikasi kita. Contoh sederhananya, user sebagai _guest_ atau sebagai _admin_ yang memiliki peran masing-masing.

## Memulai

### Instalasi rbac-mdmsoft

Pertama, jalankan perintah berikut.

Untuk pengguna windows & linux:
```bash
php composer.phar require mdmsoft/yii2-admin "~2.0"
```

Sedangkan untuk macos:
```bash
composer require mdmsoft/yii2-admin "~2.0"
```

![gambar](https://raw.githubusercontent.com/isfaaghyth/isfaaghyth.github.io/master/images/Screen%20Shot%202017-04-05%20at%202.58.16%20AM.png?token=AGdhd4cIjZ2w3II8z6eOKu3tprCahIKJks5Y7TqrwA%3D%3D)

setelah itu, aktifkan library tersebut dengan cara konfigurasi di `yii2-app/common/config/main.php`.

yii2-app/common/config/main.php

```php
<?php
return [
    'vendorPath' => dirname(dirname(__DIR__)) . '/vendor',
    'components' => [
        'cache' => [
            'class' => 'yii\caching\FileCache',
        ],
        'authManager' => [
            'class' => 'yii\rbac\PhpManager', // or use 'yii\rbac\DbManager'
        ]
    ],
    'as access' => [
      'class' => 'mdm\admin\components\AccessControl',
      'allowActions' => [
         'site/*',
         'admin/*',
         'user/*'
      ]
    ],
    'modules' => [
       'admin' => [
          'class' => 'mdm\admin\Module'
       ],
       'user' => [
          'class' => 'dektrium\user\Module',
          'admins' => ['username_admin_kamu_1', 'username_admin_kamu_2_jika_lebih_satu'],
          'enableUnconfirmedLogin' => true,
          'confirmWithin' => 21600,
          'cost' => 12,
       ],
    ],
];
```

Setelah itu, lakukan migrate terhadap project kamu.

```bash
./yii migrate --migrationPath=@mdm/admin/migrations
```

Lalu masuk lagi ke `yii2-app/common/config/main.php`, dan ganti konfigurasi user.
Seperti ini.

```php
'components' => [
    ...
    'user' => [
        'identityClass' => 'mdm\admin\models\User',
        'loginUrl' => ['admin/user/login'],
    ]
]
```

Ketika semua sudah di instal dengan baik, silahkan masuk ke `http://websitemu.dev/backend/web/index.php?r-admin`.

![finish](https://raw.githubusercontent.com/isfaaghyth/isfaaghyth.github.io/master/images/Screen%20Shot%202017-04-05%20at%203.19.24%20AM.png?token=AGdhd_SyIODjDYiv9zX8TqcdHubtybDKks5Y7TquwA%3D%3D).

semoga bermanfaat :)
