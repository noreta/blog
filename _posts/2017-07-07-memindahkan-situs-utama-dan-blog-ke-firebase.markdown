---
layout: post
title:  "Memindahkan Situs Utama dan Blog ke Firebase"
date:   2017-07-07
category: blog
tags:
- Blog
- Tutorial
comments: true
---

**TL;DR:** Tutorial deploy Jekyll ke Firebase menggunakan CI (Travis-CI) dengan repository Github.

---

Setelah saya menimbang dengan segala kelebihan dan kekurangan, saya putuskan membangun kembali halaman utama [https://sasono.web.id](https://sasono.web.id) menggunakan [Jekyll](http://jekyllrb.org) setelah sebelumnya saya menggunakan satu halaman sedarhana dengan HTML dan CSS murni.

Sedangkan untuk blog ini, saya sempat beberapa kali memindahkan tempat, kalian bisa baca [di sini](https://blog.sasono.web.id/2015/02/25/hello-world/) dan [di sini](https://blog.sasono.web.id/2016/05/06/pindah-ke-openshift/). Bagi saya rasanya perlu sekali membedakan antara halaman utama dengan blog, karena blog lebih bersifat dinamis dan kan lebih sering mengalami perubahan. Oleh karena itu, meskipun saat ini terlihat sama, halaman utama dan blog ini sebenarnya memiliki domain yang terpisah. `sasono.web.id` dan `blog.sasono.web.id`. Platform blog ini juga tercatat telah tiga kali mengalami perubahan, dari mulai Blogger, Jekyll, Ghost, Wordpress. Dan saat ini kembali menggunakan Jekyll dengan [Firebase](https://firebase.google.com/products/hosting/) untuk tempat hosting.

## Mengapa Firebase?

Firebase sejatinya milik Google. Dan jika berbicara soal kelebihan dan kekurangan, saya akan memberi batasan pada beberapa fitur yang berhubungan dengan hosting.

## Kelebihan
1. Support custom domain + SSL secara default.
2. Support [Custom rules](https://firebase.google.com/docs/hosting/url-redirects-rewrites). Saya gunakan untuk *cache browser*.
3. Google CDN. *You know* lah, mereka salah satu yang terbaik.

## Kekurangan

1. Space 1GB dengan Bandwidth 10GB
2. Hanya file statis.

# Source
Skema yang saya gunakan adalah dengan menempatkan *source code* pada *repository* Github dan menghubungkannya dengan Continuous Integration (CI), lebih spesifiknya saya menggunakan [Travis-CI](http://travis-ci.org). Kira-kira seperti inilah alur yang digunakan.

`Jekyll di kompeter lokal` **>** `Push ke Github` **>** `CI (Travis-CI)` **>** `Firebase`

---
# Setup
Silahkan buka [Firebase Console](https://console.firebase.google.com/) dan buat proyek baru dan lakukan pemasangan Firebase CLI.

Firebase menggunakan *tool* [Firebase CLI](https://firebase.google.com/docs/cli/) yang membutuhkan [Node](https://nodejs.org/) (tutorial pemasangan dapat dibaca [di sini](https://nodejs.org/en/download/package-manager/)) untuk dapat digunakan.

```
npm install -g firebase-tools
```

lalu lakukan login menggunakan

```
firebase login
```

Masuk pada direktori jekyll dan lakukan

```
cd lokasi/jekyll/berada
```

lalu

```
firebase init
```

pilih `hosting` dan proyek yang telah dibuat di awal.

Akan terdapat tiga file yang muncul `firebase.json`, `.firebaserc`, dan `database.rules.json`.
# Hosting 
Secara umum terdapat dua langkah yang dapat digunakan untuk menggunakan hosting pada Firebase.

## Pertama
Deploy secara langsung file hasil *generate* dari Jekyll ( `_site` ). 

```
firebase deploy
```

Ini merupakan cara termudah yang dapat dilakukan karena kita hanya perlu deploy file pada folder `_site` (untuk Jekyll) atau `public` (untuk default setup). Folder dapat dirubah dengan setting file `firebase.json` seperti di bawah.

```
{
  "hosting": {
    "public": "_site",
    "ignore": [
      "**/.*",
      "firebase.json"
    ],
  }
}
```

## Kedua
Menggunakan skema Continuous Integration dengan menempatkan *source code* ada repository semisal Github, Gitlab, Bitbucket atau yag lainnya. 

`Jekyll di kompeter lokal` **>** `Push ke Github` **>** `CI (Travis-CI)` **>** `Firebase`

- Buat akun Travis-CI menggunakan ID Github/Gitlab dan pilih repository yang akan dihubungkan dengan Travis-CI.
- Hubungkan Firebase dengan CI menggunakan `firebase login:ci` untuk mendapatkan Firebase token.
- Masukkan token pada Travis-CI setting dan beri nama `FIREBASE_TOKEN`

![](https://3.bp.blogspot.com/-kSBTyutS5So/WKq0D19KNqI/AAAAAAAAC0Q/7qK2QkZLjNMA7E24x8DUwsH3ATh4bZ13ACLcB/s1600/firebase%2Btoken.png)
- Buat file `.travis.yml` pada direktori *root* Jekyll. Berikut adalah konfigurasi yang saya gunakan.

```
language: node_js
node_js: '7.9.0'

before_install:
  - rvm install 2.3.1
  - rvm use 2.3.1
  - . $HOME/.nvm/nvm.sh && nvm install 6.1 && nvm use 6.1
  - gem install bundler
  - bundle check || bundle install

install:
  - npm install -g firebase-tools

before_script:
  - chmod +x ./script/cibuild

script: ./script/cibuild

after_success:
  - firebase deploy --token $FIREBASE_TOKEN

branches:
  only:
  - master

env:
  global:
  - NOKOGIRI_USE_SYSTEM_LIBRARIES=true 

sudo: false
```
- Buat folder `script` dan buat file bernama `cibuild`. Berikut configurasi milik saya.

```
#!/usr/bin/env bash
set -e # halt script on error

bundle exec jekyll build

# script/cibuild
```

# Test Deploy
Lakukan perubahan atau buat postingan baru lalu push ke repository Github/Gitlab dan tunggu beberapa menit sampai proses selesai. Lalu buka halaman 

---

Saya menggunakan skema kedua karena cara ini lebih menghemat kuota. Bayangkan jika kita harus mengoreksi beberapa tulisan/posting dan mendeploy ulang seluruh file hasil *generate*, tentunya akan lebih besar daripada mem-push hasil koreksi ke repository Github/Gitlab dan biarkan Travis-CI melakukan build dan deploy ke Firebase.

Cheers .. :)
