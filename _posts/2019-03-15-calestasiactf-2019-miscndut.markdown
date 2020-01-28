---
title: Callestasia CTF 2019 - Ndut (misc)
layout: post
date: '2019-03-15 17:00:00'
image: "/assets/images/markdown.jpg"
tag:
- CTF
- bash
star: true
category: blog
author: kosong
description: Code Challenge - Salesman Category
---

<p>Disini kita diberikan sebuah file.zip yang isinya sebagai berikut.</p>


<img src="/assets/images/post/callestasiactf/1.png" alt="" data-id="19" data-link="https://tukangsempol.systems/2019/03/15/hello-world/attachment/1/" class="wp-image-19"/>



<p>File yang pertama kali saya buka adalah howto.txt karena didalamnya pasti terdapat sedikit clue tentang bagaimana menyelesaikan challenge tersebut.Isi dari file howto.txt adalah sebagai berikut.</p>


{% highlight raw %}
gtw
jd md5(b64e(string))
cari tw ndiri
ak ngambek
mls
{% endhighlight %}


<p>Dapat kita lihat cluenya adalah md5(b64e(string)) , kita keep dulu cluenya kemudian saya buka file flag.zip dan ternyata isinya adalah flag.txt.</p>



<p>Selanjutnya saya buka folder <strong>fuckinguselessnumberifyouthinkinglikethat </strong>yang isinya adalah sebagai berikut.</p>


<img src="/assets/images/post/callestasiactf/2.png" alt="" data-id="20" data-link="https://tukangsempol.systems/2019/03/15/hello-world/attachment/2/" class="wp-image-20"/>



<p>Didalam setiap folder terdapat file image yang ketika saya buka isinya adalah hanya angka-angka,berikut contohnya.</p>


<img src="/assets/images/post/callestasiactf/0-1024x45.png" alt="" class="wp-image-21"/>



<p>Berdasarkan clue yang ada pada howto.txt yaitu <strong>md5(b64e(string))</strong> ,<strong> string</strong> yang dimaksud adalah string pada image tersebut.</p>



<p>Jadi disini saya membuat script bash untuk melakukan recognition terhadap string yang ada pada gambar lalu merubahnya ke base64 lalu md5 sekaligus melakukan dictionary attack terhadap file flag.zip yang terpassword.</p>



<p>Disini saya menggunakan tesseract untuk melakukan OCR (Optical Character Recognition).</p>


{% highlight bash %}
for cob in $(ls)
do 
	a=$(ls $cob)
	b=$(tesseract $cob/$a stdout)
	c=$(echo -n $b | base64 | tr -d " \t\n\r" | md5sum)
	unzip -P $c ../flag.zip
done
{% endhighlight %}


<p>Berikut adalah penampakan ketika script tersebut dijalankan</p>


<img src="https://tukangsempol.systems/wp-content/uploads/2019/04/Screenshot_2019-04-08_07-53-22.png" alt="" class="wp-image-61"/>



<p>Jika berhasil maka akan keluar output extracting:(nama file didalamnya).</p>



<p>Dengan demikian kita telah berhasil menyelesaikan soal ini karena kita sudah dapat mengetahui flagnya yaitu pada file flag.txt.</p>