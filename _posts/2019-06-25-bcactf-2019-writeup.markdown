---
title: BCA CTF 2019 - Writeup
layout: post
date: '2019-06-25 17:00:00'
image: "/assets/images/markdown.jpg"
tag:
- CTF
- Cryptography
- Reverse Engineering
- Binary Exploitation
- Web Exploitation
- Forensic
star: true
category: blog
author: kosong
description: BCA CTF 2019 - Writeup Category
---

<p>Disini saya akan membagikan writeup beberapa soal BCA CTF 2019 yang saya berhasil selesaikan .</p>



<h2 id="mce_2">Category : Welcome</h2>



<h4 id="mce_6">hello-world  50 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-06-30_21-05-32.png" alt="" class="wp-image-70"/>



<p>Flag : bcactf{hello!}</p>



<h4 id="mce_6">net-cat  50 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-06-30_21-08-30.png" alt="" class="wp-image-71"/>



<p>Jalankan commandnya dan muncullah flagnya</p>



<p>Flag : bcactf{5urf1n_7h3_n37c47_c2VydmVyc2lkZQ</p>



<h4 id="mce_13">wuphf  50 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-06-30_21-10-24.png" alt="" class="wp-image-72"/>



<p>Buka setiap sosmed dari BCACTF dan terdapat pecahan dari flag pada setiap sosmednya .</p>



<p>Flag : bcactf{h17_u5_uP_d3VwaGYuY29t}</p>



<h2>Category : Binary Exploitation</h2>



<h4>executable  150 Pts </h4>

<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_18-04-30.png" alt="" class="wp-image-155"/>



<p>Disini saya menggunakan radare2 untuk melakukan debugging dan ghidra untuk melakukan decompiling.</p>



<p>Berikut hasil decompile file executable</p>


{% highlight c %}
undefined8 main(void)

{
  uint uVar1;
  long lVar2;
  undefined8 *puVar3;
  undefined8 *puVar4;
  long in_FS_OFFSET;
  int local_49c;
  undefined8 local_498 [48];
  char local_318 [754];
  undefined local_26;
  long local_20;
  
  local_20 = *(long *)(in_FS_OFFSET + 0x28);
  lVar2 = 0x2f;
  puVar3 = &amp;DAT_004008c0;
  puVar4 = local_498;
  while (lVar2 != 0) {
    lVar2 = lVar2 + -1;
    *puVar4 = *puVar3;
    puVar3 = puVar3 + 1;
    puVar4 = puVar4 + 1;
  }
  *(undefined *)puVar4 = *(undefined *)puVar3;
  puts("Welcome to the lottery!");
  puts("So now we\'re going to pick a ginormous number!");
  puts("If it\'s 1, you win!");
  uVar1 = rand();
  printf("Your number is %d!\n",(ulong)uVar1);
  if (uVar1 == 1) {
    puts("Congratulations, you\'re our lucky winner!");
    local_49c = 0;
    while (local_49c < 0x179) {
      local_318[(long)(local_49c * 2)] = *(char *)((long)local_498 + (long)local_49c);
      local_318[(long)(local_49c * 2 + 1)] = '\n';
      local_49c = local_49c + 1;
    }
    local_26 = 0;
    printf(local_318);
  }
  else {
    puts("Try again next time!");
  }
  if (local_20 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
   stack_chk_fail();
  }
  return 0;
}


{% endhighlight %}


<p>Jadi disini puVar3 mengambil data dari section .rodata pada address 004008c0 yang mana data tersebut nantinya disimpan pada local_498. Dan kemudian terdapat while looping yang mengisi local_318 yang mana setiap index genap merupakan value dari local_498, misal ketika local_49c=0 maka local_318[0*2] =local_498[0] dan untuk index ganjil diisi dengan "\n" sampai memenuhi local_49c < 377 . Nantinya local_318 ini lah yang diprint jika uVar1 == 1. Namun bagaimana caranya menjadikan uVar1 = 1 sedangkan uVar1 sendiri memiliki value rand() ? . Disini kita dapat menggunakan radare2 untuk mengubah value register yang nantinya di bandingkan dengan 1 .</p>


{% highlight raw %}
r2 -d executable-ubuntu
[0x7fcf6a482c30]> aa
[0x7fcf6a482c30]> pdf @main
{% endhighlight %}

<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_18-24-17.png" alt="" class="wp-image-156"/>



<p>Perbandingan tadi terdapat pada address 0x004006a4 , yaitu cmp ebx,0x1 . Jadi disini kita cukup mengubah value dari register ebx menjadi 1 agar hasilnya True. </p>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_18-29-49.png" alt="" class="wp-image-157"/>



<p>Karena ini file binary 64 bit maka yang kita ubah adalah register rbx (ebx untuk 32 bit)</p>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_18-30-49.png" alt="" class="wp-image-158"/>



<p>Dan berhasil , dibawah tulisan Congratulations terdapat string hasil encode dari brainfuck sebagai berikut.</p>


{% highlight raw %}
--[----->+<]>----.+.--.++.-[--->+<]>--.+++[->+++<]>+.+[----->+<]>.>-[----->+<]>.+[--->++<]>.[++>---<]>-.-[->++<]>-.-[--->+<]>-.-.>-[----->+<]>+.---[->++<]>.++++++++++.[-->+<]>---.--[--->++<]>---.++[->+++<]>.[--->+<]>---.+++[->+++<]>.+++++++.-[--->+<]>--.-------.---------------.+[-->+<]>+.+.++.+[->++<]>.--.---.+++++++++++++.--[->+++++<]>.++++++++.+.-------.++.+.>--[-->+++<]>.
{% endhighlight %}


<p>Yang jika didecode hasilnya adalah sebuah flag.</p>



<p>Flag : bcactf{3x3cut4bl3s_r_fun_124jher089245}</p>



<h4>executable-2  250 Pts</h4>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_18-33-58.png" alt="" class="wp-image-159"/>



<p>Disini saya menggunakan radare2 untuk melakukan debugging dan ghidra untuk melakukan decompiling.</p>



<p>Berikut hasil decompile file executable-2</p>



{% highlight c %}
undefined8 main(void)

{
  int iVar1;
  long lVar2;
  undefined8 *puVar3;
  undefined8 *puVar4;
  long in_FS_OFFSET;
  int local_11fc;
  undefined8 local_11f8 [380];
  char local_618 [1517];
  undefined local_2b;
  undefined local_2a;
  long local_20;
  
  local_20 = *(long *)(in_FS_OFFSET + 0x28);
  lVar2 = 0x17b;
  puVar3 = &amp;DAT_004008e0;
  puVar4 = local_11f8;
  while (lVar2 != 0) {
    lVar2 = lVar2 + -1;
    *puVar4 = *puVar3;
    puVar3 = puVar3 + 1;
    puVar4 = puVar4 + 1;
  }
  puts("Welcome to the lottery!");
  puts("So now we\'re going to pick a ginormous number!");
  puts("If it\'s 1, you win!");
  iVar1 = rand();
  printf("Your number is %d!\n");
  if (iVar1 == 1) {
    puts("Congratulations, you\'re our lucky winner!");
    iVar1 = rand();
    local_618[0] = (char)iVar1;
    local_11fc = 0;
    while (local_11fc < 0x2f6) {
      local_618[(long)(local_11fc * 2 + 1)] =
           (char)(*(int *)((long)local_11f8 + (long)local_11fc * 4) / 100);
      local_618[(long)((local_11fc + 1) * 2)] = '\n';
      local_11fc = local_11fc + 1;
    }
    local_2b = 0;
    iVar1 = rand();
    local_2a = (undefined)iVar1;
    printf(local_618 + 1);
  }
  else {
    puts("Try again next time!");
  }
  if (local_20 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}


{% endhighlight %}

<p>Jadi disini puVar3 mengambil data dari section .rodata pada address 004008e0 yang mana data tersebut nantinya disimpan pada local_11f8. Dan kemudian terdapat while looping yang mengisi local_618 yang mana setiap index ganjil ke-n merupakan value dari local_11f8 index ke -n*4 dengan valuenya dibagi 100, misal ketika local_11fc=0 maka local_618[0*2+1] =local_11f8[0*4] (jika value dari local_11f8=4600 maka yang disimpan di local_618 adalah 46 )dan untuk index genap diisi dengan "\n" sampai memenuhi local_11fc < 758 . Nantinya local_618 ini lah yang diprint jika iVar1 == 1. Disini kita juga akan menggunakan radare2 untuk mengubah value register yang nantinya di bandingkan dengan 1 .</p>


{% highlight raw %}
r2 -d executable-2-ubuntu
[0x7f7eb82c1c30]> aa
[0x7f7eb82c1c30]> pdf @main
{% endhighlight %}

<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_18-40-36.png" alt="" class="wp-image-160"/>



<p>Perbandingan tadi terdapat pada address 0x00400691 , yaitu cmp ebx,0x1 . Jadi disini kita cukup mengubah value dari register ebx menjadi 1 agar hasilnya True.</p>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_18-42-27.png" alt="" class="wp-image-161"/>



<p>Karena ini file binary 64 bit maka yang kita ubah adalah register rbx (ebx untuk 32 bit)</p>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_18-43-16.png" alt="" class="wp-image-163"/>



<p>Dan berhasil , dibawah tulisan Congratulations terdapat string hasil encode dari brainfuck sebagai berikut.</p>


{% highlight raw %}
+[--------->++<]>.+.--.++.---------.++++++++++++.+++++.+[-->+<]>+++.--[----->+<]>-.-------------.+++++++++.++++++.+[->+++<]>.++++++.[--->+<]>+.-[->+++<]>.--.-[--->+<]>-.-.+[->+++<]>++.+.++++++++++.-------.[--->+<]>----.++[->+++<]>.+++++++.-[--->+<]>--.-------.[------>+<]>++.+[-->+++<]>-.[--->+++++<]>.[----->+++<]>.[--->+<]>-.------------.+.+++++.---.------------.[--->+<]>--.----.+[----->++<]>-.[--->+<]>--.+++[->+++<]>++.+++++++.-----.++++.--.+++++++++.--------.-[--->+<]>-.[->+++<]>.[--->+<]>--.---.+++++++.[->+++<]>--.++++++++.++++.++.[->+++<]>.+++++++++++++.+++[->+++<]>++.+++++++++.+[--->+<]>+.[->+++<]>.-----------.-[--->+<]>++.--[->+++<]>.[--->+<]>.[->+++<]>-.++++++++.-.+++++++++.--------.-[--->+<]>-.--.++[->+++<]>.[--->+<]>++.-------.+++++++++++.
{% endhighlight %}


<p>Yang jika didecode hasilnya adalah sebagai berikut.</p>


{% highlight raw %}

rsqsjv{Arent_executables_fun?_I_think_so_sdkfjhqiweuryiquwerajzncxbvaihqiwueyr}

{% endhighlight %}

<p>Karena kita tahu bahwa format flag adalah bcactf{} , maka tentunya string tersebut bukanlah flag,jadi disini kita coba gunakan caesar cipher untuk menemukan flag sebenarnya.</p>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_18-47-32.png" alt="" class="wp-image-162"/>



<p>Dan ketemulah flagnya.</p>



<p>Flag :  bcactf{Aboxd_ohomedklvoc_pex?_I_drsxu_cy_cnuptrasgoebisaegobktjxmhlfksrasgeoib}</p>



<h2>Category : Crypto</h2>



<h4>basic-number  50 Pts </h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-06-30_21-14-37.png" alt="" class="wp-image-73"/>



<p>Disini kita diberikan soal sebagai berikut </p>


{% highlight raw %}
01100010 00110001 01101110 01100001 01110010 01111001 01011111 01110011
00110000 01101100 01110110 00110011 01100100 01011111 01100111 00110000
00110000 01100100 01011111 01110111 00110000 01110010 01101011
{% endhighlight %}


<p>Setiap 8 digit biner tersebut ternyata membentuk suatu character , jadi disini saya membuat sebuah script yang melakukan konversi biner tersebut ke karakter ascii .</p>


{% highlight python %}
a="01100010 00110001 01101110 01100001 01110010 01111001 01011111 01110011 00110000 01101100 01110110 00110011 01100100 01011111 01100111 00110000 00110000 01100100 01011111 01110111 00110000 01110010 01101011".split(" ")
b=""
for i in a:
  l=0
  for j,k in enumerate(i[::-1]):
    m=(2**j)*int(k)
    l+=m
  b+=str(chr(l))
print b

{% endhighlight %}

<p>Flag : bcactf{b1nary_s0lv3d_g00d_w0rk}</p>



<h4 id="mce_4">cracking-the-cipher  50 Pts</h4>



<p>Disini kita diberikan sebuah soal yang mana terdapat clue yaitu <strong>Caesar Salad Dressing</strong>  yang berarti terdapat sebuah string yang di encode menggunakan caesar cipher. Pada bagian akhir soal terdapat sebuah ciphertext yang sepertinya telah di encode menggunakan caesar cipher</p>


{% highlight raw %}
vjg rcuuyqtf ku ngctpkpi_ecguct_ekrjgtu_ku_hwp!
{% endhighlight %}


<p>Kemudian disini saya membuat sebuah script untuk mengembalikan cipher text tersebut menjadi plain text,karena disini kita tidak tahu text tersebut di encode menggunakan shift key berapa maka kita lakukan bf mulai dari 1 - 26 .</p>


{% highlight python %}
import string
cipher="vjg rcuuyqtf ku ngctpkpi_ecguct_ekrjgtu_ku_hwp!"
for i in range(1,27):
  plain=""
  for j in cipher:
    if(j in string.lowercase):  
      a=ord(j)+i
      if(a>122):
        plain+=chr(96+(a-122))
      else:
        plain+=chr(a)
    else:
      plain+=j
  print plain
{% endhighlight %}


<p>Flag : bcactf{learning_caesar_ciphers_is_fun!}</p>



<h4 id="mce_0">three-step-program  125 Pts</h4>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_07-07-58.png" alt="" class="wp-image-75"/>



<p>Disini kita diberika sebuah file txt yang isinya sebagai berikut.</p>


{% highlight  raw%}
MzIgLSAgfDMgVGltZXMgQSBDaGFybXwgLSAzMg==



JJGTEVSLKNBVISSGINCU2VCTGJFVETCWKNGVGTKLKJEEKQ2VJNEUSNC2KZKVCS2OJFNE4RKPKNFUUSKSJNKTITSDKJFEERKUI5GTETKJLJGVMQ2RJNLEWUSLIZAVES2DJRFE2RKDK5JU2SKKJBCTEVKLJBDUSWSUI5KTETSLKZEVKS2TLJKEWUSFIU2FKU2WJRBEIVCFKFJVASKWIFKU2USLIRDUUR2FGJJEWQ2LKJGFMR2TJNCUYSSIIRFU2U2UJFCTEVKJKZJUMSKKJNKU6VK2KRFVES2VGZKEWUSKIJCVIR2XKNBEUNKGIZDVMMSEJRFEERKDKRJVOR2SJJKUGV2TJVFDKR2VGRLVGSKLJUZEKSKWJNHEWWSKKVDVCSSUJFJEERJUK5JVKTCCIZKEKVCDIVFFUQKWKFITEQSJJZEVKV2SGJDEYQSCKVBVMSSTJFFEMRSFKMZEISKFLJCVSTKTIZEUUTCGJ5JVUV2KJJAVKNSVKNMUWTSBKZKU2MSUJJLEYRCFKEZEETCKJNDECVCSKZFU4QSVI5ITEU2LJZCEMU2VJNDEYRSOIVKVCS2OJRFE4RKPKFNFIS2SINCTEUSTKZGEERCVKNJEGRKKGVDEISKXINBEOVSDIVGVES2DJM2UIVKXKNFUKSS2I5LE2VSLLBGEKWSVJFJFGUCLLJHEKQ2QJI2UQVJWKE6T2PJ5



lhlm oad lamaew eyhmgs. lg i sxsro rgu ntee qhj a qesg? dbfcp rgu stne xtve tm lhtl xac, b’dl rh wadr gn jhm ayw zayw at zowr. 
mvscey{bu57_j0n_o4i7_kgbhmffhlqe} bfm, te htjnpw, feim lixx at hhf’t mx ko dbepwx…
{% endhighlight %}


<p>Dari list karakter yang membentuk string tersebut dapat ditentukan bahwa kemungkinan block 1 = base64 , block 2 = base32 .</p>



<p>Kemudian kita lakukan decode sesuai dengan encodingnya</p>

{% highlight raw %}
Block 1 

32 - |3 Times A Charm| - 32 
( clue untuk block 2 , yaitu di encode menggunakan base32 3 kali )

Block 2

Why english so ard to tok. 
No speak more English. 
Ail gi you tu hints to read my encrypted languich.

1. SALT iz key to gret food!
2. 2. Le francais crypte le meilleur 

{% endhighlight %}
<p>Dari block 2 kita dapat mengetahui bahwa block 3 menggunakan vignere cipher ( salah satu cipher yang diciptakan oleh orang prancis ) dengan key yaitu **SALT**. Berikut hasil decode nya </p>


{% highlight raw %}
that was simple enough. so i heard you came for a flag? since you have made it this far, i’ll go easy on you and hand it over. bcactf{ju57_y0u_w4i7_znjhbmnhaxm} but, be warned, next time it won’t be so simple…
{% endhighlight %}


<p>Flag : bcactf{ju57_y0u_w4i7_znjhbmnhaxm}</p>



<h4 id="mce_4">a-major-problem  200 Pts</h4>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_07-21-54.png" alt="" class="wp-image-76"/>



<p>Disini kita diberikan hint yaitu </p>



The words translate to numbers, which then translate to the flag.



<p>Berarti disini kita mengubah kata kata tersebut menjadi angka lalu mengubahnya ke karakter ascii . Cluenya disini adalah Major Mnemonic .</p>



<p>Saya menggunakan website https://major-system.info/en/ untuk melakukan translate ke angka dan didapatkan angka sebagai berikut .</p>


{% highlight python %}
>>> a="98 99 97 99 116 102 123 103 51 116 95 103 47 116 125".split(" ")
>>> print "".join(chr(int(i)) for i in a)
bcactf{g3t_g/t}
{% endhighlight %}


<p>Ternyata karakter ke 13 bukanlah / melainkan 0.</p>



<p>Flag : bcactf{g3t_g0t}</p>



<h2 id="mce_2">Category : Forensics</h2>



<h4 id="mce_6">split-the-red-sea  100 Pts</h4>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_07-44-46.png" alt="" class="wp-image-83"/>



<p>Berikut file redsea.png nya</p>


<img src="/assets/images/post/bcactf/redsea-1024x985.png" alt="" class="wp-image-84"/>



<p>Disini saya menggunakan exiftool untuk melakukan forensic terhadap file tersebut dan didapatkan flag sebagai berikut .</p>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_07-48-07-1.png" alt="" class="wp-image-86"/>



<p>Flag : bcactf{7w0_r3d5_sdf3wqa}</p>



<h4 id="mce_54">bca-craft  125 Pts</h4>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_08-03-55.png" alt="" class="wp-image-87"/>



<p>Disini saya menemukan sebuah file dengan nama flag.mfunction yang didalamnya terdapat flag dengan format sebagai berikut .</p>


{% highlight raw %}
"! The flag is: ", "b", "c", "a", "c", "t", "f", "{", {"text": "m1n3cr4f7_b347s_f0rtn1t3", ... }
{% endhighlight %}


<p>Flag : bcactf{m1n3cr4f7_b347s_f0rtn1t3}</p>



<h4 id="mce_65">file-head  125 Pts</h4>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_10-24-18.png" alt="" class="wp-image-88"/>



<p>Terdapat sebuah file flag.png yang mana ketika dibuka error ( not a png file ). Dilihat dari clue nya maka disini kita diminta untuk merubah file header nya sehingga file tersebut dapat dikenali sebagai file png.</p>



<p>Kita ubah file header nya menjadi file header dari png<br></p>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_10-26-48.png" alt="" class="wp-image-89"/>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_10-27-38.png" alt="" class="wp-image-90"/>



<p>Kemudian buka kembali maka muncullah flagnya.</p>



<p>Flag : bcactf{f1l3_h3ad3rs_r_c001}</p>



<h4>of-course-rachel  150 Pts</h4>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_10-30-34.png" alt="" class="wp-image-91"/>



<p>Isi dari file zip tersebut adalah screenshot dari hex sebuah file yang terbagi menjadi 5. Disini saya menggunakan tesseract untuk melakukan konversi img to txt lalu menjadikan file part1-5 tersebut menjadi satu file</p>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_10-39-08.png" alt="" class="wp-image-92"/>



<p>Disini saya menamakan file nya sebagai **full**</p>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_10-45-12.png" alt="" class="wp-image-94"/>



<p>Lalu setelah itu saya coba untuk mengembalikannya kembali dengan command **xxd -r -p**</p>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_10-46-58.png" alt="" class="wp-image-95"/>



<p>Sepertinya file tersebut adalah script python, namun hanya beberapa baris kode saja yang readable. Kemungkinan besar kesalahan sewaktu konversi dari img ke txt,setelah saya betulkan dan menggabungkan semua part berikut adalah potongan kodenya .</p>


{% highlight python %}
import binascii
import random
class Vector(object):    """        
This class represents a vector of arbitray size.        
You need to give the vector components.         
Overview about the methods:        
constructor(components : list) : init the vector        
set(components : list) : changes the vector components.        
__str__() : toString method        
component(i : int): 
--------------------------snippet---------------------------
{% endhighlight %}

<p>Pada script tersebut terdapat potongan kode yang menarik  yaitu  function int_to_text serta variable flag. Kemudian saya jadikan satu lalu coba run</p>


{% highlight python %}
def int_to_text(inp):
  hexed = hex(inp)    
  return bytearray.fromhex(hexed[2:]).decode()
flag = 820921601166721424573282546345206805820898697321521913920196691573868657577500743744203737234698
print(int_to_text(flag))
{% endhighlight %}


<p>Flag : bcactf{0p71c4lly_r3c0gn1z3d_ch4r4c73rs</p>



<h4>open-docs  150 Pts</h4>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_12-47-07.png" alt="" class="wp-image-96"/>



<p>Disini kita bisa mengexplore isi dari file docx dengan cara mengubah ekstensinya menjadi .zip</p>



<p>Saya menemukan sebuah file xml yang menarik bernama secrets.xml</p>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_12-50-00.png" alt="" class="wp-image-97"/>



<p>Isi dari secrets.xml adalah sebuah string yang diencode menggunakan base64</p>


{% highlight raw %}
PHNlY3JldCBmbGFnPSJiY2FjdGZ7ME94TWxfMXNfNG00ejFOZ30iIC8+
{% endhighlight %}


<p>Setelah di decode ketemulah flagnya</p>



<p>Flag : bcactf{0OxMl_1s_4m4z1Ng}</p>



<h4>study-of-roofs  150Pts</h4>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_12-53-23.png" alt="" class="wp-image-98"/>



<p>Diberikan sebuah file jpg , kemudian saya coba untuk menjalankan binwalk pada file jpg tersebut dan muncullah flagnya.</p>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_12-55-31.png" alt="" class="wp-image-100"/>



<p>Flag : bcactf{r4i53_7h3_r00f_liz4rd}</p>



<h4>wavey  150 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_12-57-17.png" alt="" class="wp-image-101"/>



<p>Diberikan sebuah file straightfire.wav , disini saya coba langsung melihat spectogram dari file straightfire tersebut menggunakan audacity.</p>


<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_13-00-06.png" alt="" class="wp-image-102"/>



<p>Flag : bcactf{f33lin_7h3_vib3z</p>



<h4>corrupt-psd  200 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_13-01-11.png" alt="" class="wp-image-103"/>



<p>Diberikan sebuah file psd yang mengalami error,dapat diketahui dari soal errornya disebabkan oleh file header yang salah , jadi disini saya merubah file header nya sesuai dengan file header psd.</p>

	
<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_13-07-55.png" alt="" class="wp-image-106"/>

<p>Dan berikut ketika file tersebut dibuka</p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_13-08-05-1.png" alt="" class="wp-image-106"/>



<p>Flag : bcactf{corrupt3d_ph0705sh0p?_n0_pr0b5_1af4efb890}</p>



<h4 id="mce_34">the-flag-is 200 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_13-16-31.png" alt="" class="wp-image-107"/>



<p>Disini saya membuka file flag.pdf dengan browser dan muncul lah flagnya</p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_13-17-05.png" alt="" class="wp-image-108"/>



<p>Flag : bcactf{d0n7_4g3t_4b0u7_1nCr3Men74l_uPd473s}</p>



<h4>one-punch-zip  250 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_14-29-27.png" alt="" class="wp-image-153"/>



<p>Diberikan sebuah file png dan juga file zip terpassword. Disini saya coba analisa file png karena passwordnya pasti ada hubungannya dengan file tersebut.</p>



<p>Setelah saya melakukan banyak percobaan forensic dan ternyata tidak membuahkan hasil akhirnya saya coba memanfaatkan string yang terdapat pada opm.png saat saya melakukan binwalk.</p>


{% highlight raw %}
binwalk --dd=".*" opm.png

{% endhighlight %}


<p>Disini saya coba melakukan bruteforce terhadap zip tersebut menggunakan string printable yang ada pada file 84.</p>


{% highlight raw %}
strings 84 > bf.txt
mv bf.txt ../
{% endhighlight %}



<p>Kemudian langsung saya coba crack dengan menggunakan fcrackzip dengan worldist pada bf.txt</p>


{% highlight raw %}
fcrackzip -u -D -p bf.txt superSecure.zip
{% endhighlight %}


<p>Dan hasilnya</p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_17-56-48.png" alt="" class="wp-image-154"/>



<p>Lalu saya extract dan ketemulah flagnya</p>



<p>Flag : bcactf{u5ing_4ll_string5_0f_1mag3_@s_dictionary?}</p>



<h2 id="mce_2">Category : Programming</h2>



<h4 id="mce_6">1+1=window  75 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_13-19-58.png" alt="" class="wp-image-109"/>



<p>Diberikan sebuah file one.txt dan two.txt yang isinya adalah hex,dilihat dari soal dapat disimpulkan bahwa flagnya adalah hasil penjumlahan dari one.txt dan two.txt yang dikonversi ke ascii.</p>


{% highlight python %}
flag=""
one="0x23 0x49 0x16 0x46 0x45 0x16 0x3c 0x3c 0x45 0x64 0x16 0x37 0x3c 0x3c 0x3c 0x16 0x46 0x45 0x37 0x1e 0x49 0x16 0x46 0x49 0x16 0x1e 0x16 0x32 0x32 0x3c 0x32 0x49 0x3c 0x64 0x1e 0x32 0x3c 0x18 0x64 0x32 0x32 0x50 0x14 0x64 0x32 0x5a 0x45 0x32 0x32 0x55 0x50 0x49 0x3c 0x14 0x3c 0x5f".split(" ")
two="0x26 0x2b 0x0a 0x23 0x2e 0x0a 0x29 0x25 0x2e 0x15 0x0a 0x37 0x25 0x25 0x2c 0x0a 0x23 0x2e 0x37 0x09 0x2b 0x0a 0x23 0x2b 0x0a 0x21 0x0a 0x30 0x31 0x25 0x31 0x2b 0x2a 0x17 0x13 0x2d 0x2c 0x18 0x0c 0x01 0x2d 0x29 0x1c 0x11 0x2d 0x1b 0x2e 0x01 0x2d 0x1b 0x29 0x2b 0x2c 0x1c 0x32 0x1e".split(" ")
for i in range(0,len(one)):
     flag+=chr(int(one[i],16)+int(two[i],16)) 
print flag
{% endhighlight %}


<p>Flag : bcactf{1_h0p3_y0u_us3_pyth0n}</p>



<h4>bca-store  75 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_13-50-11.png" alt="" class="wp-image-111"/>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_13-50-33.png" alt="" class="wp-image-112"/>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_13-50-44.png" alt="" class="wp-image-113"/>



<p>Di berikan soal seperti diatas, dengan isi file input.txt sebagai berikut</p>


{% highlight raw %}
 C B B C A 250
 A C D A 230
 A B C D 240
 D D D 225
 A A A A 150
 A A A A B B B B B C C C C C D D D D 1000
 A A A A B B B B B C C C C C D D D D 90
{% endhighlight %}


<p>Disini saya menggunakan python untuk membuat solvernya .</p>


{% highlight python %}
a=45
b=52
c=67
d=75
e="C B B C A 250,A C D A 230,A B C D 240,D D D 225,A A A A 150,A A A A B B B B B C C C C C D D D D 1000,A A A A B B B B B C C C C C D D D D 900".split(",")
for i in e:
  n=i.split(" ")
  a1=n.count('A')
  b1=n.count('B')
  c1=n.count('C')
  d1=n.count('D')
  total=a1*a
  if(b1>1):
    b2=46.8
    total+=((b2*(b1/2))+(b*(b1-(b1/2))))
  elif(b1==0):
    total+=0
  elif(b1==1):
    total+=b
  if(c1>1):
    c2=33.5
    total+=((c2*(c1/2))+(c*(c1-(c1/2))))
  elif(c1==0):
    total+=0
  elif(c1==1):
    total+=c
  if(d1>2):
    total+=(d*(d1-(d1/3)))
  elif(d1==0):
    total+=0
  else:
    total+=(d*d1)
  if(int(n[-1])<total):
    print "-1"
  else:
    print '%.2f' % (int(n[-1])-total)

{% endhighlight %}



Output : 5.70 -1 1.00 75.00 -1 77.40 -1



<h4>instructions  175 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_13-54-56.png" alt="" class="wp-image-114"/>



<p>Berikut isi pesannya</p>



Dear Agent Reffef, 
	
	I have attached the super secret plans for operation 0x576f726b206f6e207468652070757a7a6c652c2073746f702072656164696e672068657821. You will need to decode it first though. 
	
	The rules are simple: 
	
	A line is "viable" if the length of a line is divisible by 3, and the line does not contain the & character. 
	
	For every viable line, you will grab the `n`th character,  where `n` is the corresponding number at the top of the file (Counting from one!)  The first viable line will use the first number, etc. 
	
	Put all the letters together to find the answer!  
	
	- Agent Doposi



<p>Disini saya menggunakan python untuk membuat solver soal diatas</p>

{% highlight python %}
f=open("flag.txt", "r")
f1=f.readlines()
flag=""
no="20 30 8 14 17 24 44 19 17 29 20 34 35 27 42 34 7 25 7 21 8 38 13 25 14 13 42 14 20 23 3 27 38 9 18 41 3 11 35".split(" ")
arr=[]
for i in f1:
  if('&amp;' not in i and (len(i)-1)%3==0):
    arr.append(i[:-1])  
for i in range(0,len(arr)):
  flag+=arr[i][int(no[i])-1]
print flag
{% endhighlight %}

<h4>public-library  200 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_14-08-27.png" alt="" class="wp-image-116"/>



<p>Diberikan file .class lalu saya decompile file tersebut dan didapatlah file .java yang isinya sebagai berikut.</p>


{% highlight kotlin %}
import kotlin.Metadata;

@Metadata(mv={1, 1, 13}, bv={1, 0, 3}, k=1, d1={"\000\024\n\002\030\002\n\002\020\000\n\002\b\002\n\002\020\016\n\002\b\002\030\0002\0020\001B\005¢\006\002\020\002J\006\020\005\032\0020\004R\016\020\003\032\0020\004XD¢\006\002\n\000¨\006\006"}, d2={"LPublicLibrary;", "", "()V", "flag", "", "getFlag", "public-library"})
public final class PublicLibrary { private final String flag = "bcactf{t4k3_4_j4v4_c7a55_789208694209642475}";
  
  @org.jetbrains.annotations.NotNull
  public final String getFlag() { return flag; }
  
  public PublicLibrary() {}
}
{% endhighlight %}

<p>Flag : bcactf{t4k3_4_j4v4_c7a55_789208694209642475</p>



<h4>manner-of-thpeaking 250 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-01_14-11-00.png" alt="" class="wp-image-117"/>



<p>Berikut isi dari file inthtructhins.txt</p>


{% highlight raw %}
cadadddddr, caddadddddr, caadddddr, caddadddddr, cadddddddddddddddddddadddddr, cadddddadddddr, caaddddddr, cadddddddddddadddr, cadadr, cadddddadr, cadddddddadr, caddddaddddr, caddddddddadr, caddddadr, cadddddadr, cadddadr, cadddadddddr, caddddaddddr, cadddddddddddddddadddddr, cadddddddddddddddddadddr, caadr, caddddddadddddr, cadddddddddddddddddadddr, caddddadr, caddddddddddddadddr, caddddddddddddadddddr, cadadr, cadddddddddddddadddddr, caddddddadddr, caddddaddddr, cadadr, cadddddadr, caddddaddddr, caddddadr, caddddddddddddddddddddddadddddr, cadddadr, caddddddddddddddddddadddr, caadr, caddddddddddddadddr, caddddadddddr, cadar, caddaddddddr
{% endhighlight %}


<p>dan berikut isi dari file printableth.txt</p>


{% highlight raw %}
(( !\"#$%&amp;'()*+,-./)(0123456789)(:;<=>?@)(ABCDEFGHIJKLMNOPQRSTUVWXYZ)([\]^_`)(abcdefghijklmnopqrstuvwxyz)({|}~))
{% endhighlight %}


<p>Mekanisme encoding pada string di file inthructhins.txt adalah mengikuti urutan grup string pada printableth . Berikut penjelasannya</p>


{% highlight raw %}
( !\"#$%&amp;'()*+,-./) --> Group 1 = *ar* 
(0123456789) --> Group 2 = *adr*
(:;<=>?@) --> Group 3 = *addr*
(ABCDEFGHIJKLMNOPQRSTUVWXYZ) --> Group 4 = *adddr*
([\]^_`) --> Group 5 = *addddr*
(abcdefghijklmnopqrstuvwxyz) --> Group 6 = *adddddr*
({|}~) --> Group 7 = *addddddr*

Example :
1. caadddddr = 'ca'+ 'adddddr' = group 6 karakter ke 1
2. 2. cadadddddr = 'cad'+ 'adddddr' = group 6 karakter ke 2
3. 3. caddadddddr = 'cadd'+ 'adddddr' = group 6 karakter ke 
4. Dst..
{% endhighlight %}


<p>Berikut solver yang saya buat menggunakan python</p>


{% highlight python %}
a=['cadadddddr', 'caddadddddr', 'caadddddr', 'caddadddddr', 'cadddddddddddddddddddadddddr', 'cadddddadddddr', 'caaddddddr', 'cadddddddddddadddr', 'cadadr', 'cadddddadr', 'cadddddddadr', 'caddddaddddr', 'caddddddddadr', 'caddddadr', 'cadddddadr', 'cadddadr', 'cadddadddddr', 'caddddaddddr', 'cadddddddddddddddadddddr', 'cadddddddddddddddddadddr', 'caadr', 'caddddddadddddr', 'cadddddddddddddddddadddr', 'caddddadr', 'caddddddddddddadddr', 'caddddddddddddadddddr', 'cadadr', 'cadddddddddddddadddddr', 'caddddddadddr', 'caddddaddddr', 'cadadr', 'cadddddadr', 'caddddaddddr', 'caddddadr', 'caddddddddddddddddddddddadddddr', 'cadddadr', 'caddddddddddddddddddadddr', 'caadr', 'caddddddddddddadddr', 'caddddadddddr', 'cadar', 'caddaddddddr']
b=" !\"#$%&amp;'()*+,-./"
c="0123456789"
d=":;<=>?@"
e="ABCDEFGHIJKLMNOPQRSTUVWXYZ"
f="[\]^_`"
g="abcdefghijklmnopqrstuvwxyz"
h="{|}~"
flag=""
for i in a:
  if 'addddddr' in i:
    flag+=h[len(i)-10]
  elif 'adddddr' in i:
    flag+=g[len(i)-9] 
  elif 'addddr' in i:
    flag+=f[len(i)-8] 
  elif 'adddr' in i:
    flag+=e[len(i)-7]
  elif 'addr' in i:
    flag+=d[len(i)-6]
  elif 'adr' in i:
    flag+=c[len(i)-5]
  elif 'ar' in i:
    flag+=b[len(i)-4]
print flag
{% endhighlight %}


<p>Flag : bcactf{L157_8453d_pR0gR4Mm1nG_15_4w3S0Me!}</p>



<h2 id="mce_2">Category : Reversing</h2>



<h4 id="mce_6">large-pass  100 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_07-58-40.png" alt="" class="wp-image-122"/>



<p>Diberikan sebuah file binary large-linux , disini saya coba untuk melakukan decompile dengan ghidra</p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_08-00-02.png" alt="" class="wp-image-123"/>



<p>Terlihat jelas bahwa input kita tersimpan pada variable LStack32 yang mana dibandingkan dengan LStack24 dan jika sama maka akan melakukan print Correct password.</p>



<p>beriku adalah value dari variable LStack24 jika diubah ke decimal</p>


{% highlight raw %}
5546068866699313608
{% endhighlight %}


<p>Lalu jalankan ./{nama-file} lalu masukkan password tersebut maka outputnya adalah Correct password.</p>



<p>Flag : 5546068866699313608</p>



<h4>basic-pass-1</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_08-12-10.png" alt="" class="wp-image-124"/>



<p>Diberikan sebuah file binary basic-pass-1-linux , disini saya coba untuk melakukan decompile dengan ghidra.</p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_08-13-15.png" alt="" class="wp-image-125"/>



<p>Terlihat bahwa dibutuhkan setidaknya satu argument yang merupakan passwordnya sendiri yang dicocokkan dengan variable iVar1. Variable iVar1 sendiri jika diubah ke decimal adalah 1234. Jadi disini kita bisa tau langsung flagnya ataupun kita bisa juga menjalankan ./basic-pass-1-linux 1234 untuk mengetahui flagnya</p>



<p>Flag : bcactf{hey_its_a_password}</p>



<h4>scratch-that  150 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-30-29.png" alt="" class="wp-image-133"/>



<p>Link : <a href="https://scratch.mit.edu/projects/276674047/">https://scratch.mit.edu/projects/276674047/</a></p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-31-38.png" alt="" class="wp-image-134"/>



<p>Ketika kita membuka link tersebut maka akan tampil seperti gambar diatas,lalu disini saya coba menginputkan bebas dan berikut hasilnya</p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-31-48.png" alt="" class="wp-image-135"/>



<p>Dilihat dari judulnya adalah guess the flag, namun akan sangat sangat lama jika kita menebak flagnya karena tampilannya ketika salah flag hanya merah dan tulisan wrong , tidak ada info tambahan mengenai flagnya.</p>



<p>Jadi disini saya coba untuk melihat struktur dari program tersebut dan menemukan sesuatu yang menarik yaitu sebuah block generate flag dan variable flag. </p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-33-23.png" alt="" class="wp-image-136"/>



<p>Jadi saya coba menyalinnya ke function utama dengan struktur awal seperti berikut</p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-33-45.png" alt="" class="wp-image-137"/>



<p>Setelah saya ubah menjadi sebagai berikut</p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-34-22.png" alt="" class="wp-image-138"/>



<p>Dan berikut hasilnya setelah dijalankan</p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-40-54.png" alt="" class="wp-image-139"/>



<p>Flag : bcactf{scr4tch3d_Pourquoi_empty_23412342463682</p>



<h4>another-pass  200 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_08-21-59.png" alt="" class="wp-image-126"/>



<p>Diberikan sebuah file binary another-linux , disini saya coba untuk melakukan decompile dengan ghidra.</p>



<p>Hasil decompile fungsi main()</p>


{% highlight c %}
undefined8 main(void)

{
  undefined8 uVar1;
  long in_FS_OFFSET;
  undefined auStack72 [56];
  long lStack16;
  
  lStack16 = *(long *)(in_FS_OFFSET + 0x28);
  printf("Pass: ");
  __isoc99_scanf(&amp;DAT_001009d2,auStack72);
  checkInput(auStack72);
  putchar(10);
  uVar1 = 0;
  if (lStack16 != *(long *)(in_FS_OFFSET + 0x28)) {
    uVar1 = __stack_chk_fail();
  }
  return uVar1;
}
{% endhighlight %}


<p>Terlihat disini inputkan kita disimpan sebagai string pada variable auStack72 dan kemudian variable tersebut digunakan sebagai parameter function checkinput()</p>



<p>Dan berikut hasil decompile fungsi checkinput()</p>


{% highlight c %}
void checkInput(long lParm1)

{
  ulong uVar1;
  ulong uVar2;
  long in_FS_OFFSET;
  undefined local_2d;
  int local_2c;
  int local_28;
  int local_24;
  long local_20;
  
  local_20 = *(long *)(in_FS_OFFSET + 0x28);
  local_28 = 0;
  local_24 = 0;
  do {
    uVar2 = SEXT48(local_24);
    uVar1 = strlen(lParm1);
    if (uVar1 <= uVar2) {
      printf("Incorrect.");
LAB_001008a0:
      if (local_20 != *(long *)(in_FS_OFFSET + 0x28)) {
        __stack_chk_fail();
      }
      return;
    }
    local_2d = *(undefined *)(lParm1 + (long)local_24);
    __isoc99_sscanf(&amp;local_2d,&amp;DAT_001009b4,&amp;local_2c);
    local_28 = local_28 + local_2c;
    if (local_28 == 0x4b) {
      printf("Correct.");
      goto LAB_001008a0;
    }
    local_24 = local_24 + 1;
  } while( true );
}
{% endhighlight %}


<p>inputan kita tadi pada function checkinput dinamakan lParm1. Kemudian lParm1 dicek panjangnya dengan fungsi strlen() lalu dimasukkan ke variable uVar1 , jika panjang uVar1 lebih besar dari uVar2 yang mana uVar2 sendiri di increment 1 setiap akhir looping ( local_24+=1 ) maka program akan mengeluarkan output <strong>Incorrect</strong> dan berhenti. Namun didalam proses looping terdapat pengecekan lain , yaitu pengecekan local_28 == 0x4b (75 dalam decimal) , local_28 sendiri adalah hasil penjumlahan dari local_2c (local_2c adalah setiap karakter pada lParm1 , *(undefined *)(lParm1 + (long)local_24) seperti array lParm1[0] dikarenakan looping pertama local_24 = 0 ) , jika pengecekan tersebut bernilai True maka akan memunculkan output <strong>Correct</strong> dan program akan berhenti .</p>



<p>Jadi disini kita hanya perlu memberikan input angka yang jika setiap digitnya dijumlah bernilai 75 , contoh : 999999993</p>



<p>Flag : 999999993</p>



<h4>basic-pass-2  200 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_10-44-29.png" alt="" class="wp-image-127"/>



<p>Diberikan sebuah file binary basic-pass-2-linux , disini saya coba untuk melakukan decompile dengan ghidra.</p>



<p>Hasil decompile fungsi main()</p>


{% highlight c %}
undefined8 main(int iParm1,undefined8 *puParm2)

{
  int iVar1;
  undefined8 uVar2;
  long in_FS_OFFSET;
  undefined8 uStack72;
  undefined8 uStack64;
  undefined8 uStack56;
  undefined8 uStack48;
  undefined8 uStack40;
  undefined4 uStack32;
  undefined uStack28;
  long lStack16;
  
  lStack16 = *(long *)(in_FS_OFFSET + 0x28);
  if (iParm1 == 2) {
    uStack72 = 0x2073692073696874;
    uStack64 = 0x6d206863756d2061;
    uStack56 = 0x756365732065726f;
    uStack48 = 0x7773736170206572;
    uStack40 = 0x742069202c64726f;
    uStack32 = 0x6b6e6968;
    uStack28 = 0;
    iVar1 = strcmp(puParm2[1],&amp;uStack72,&amp;uStack72);
    if (iVar1 == 0) {
      puts("Congrats! The key is bcactf{its_another_password}");
      uVar2 = 0;
    }
    else {
      puts("Incorrect passcode.");
      uVar2 = 1;
    }
  }
  else {
    fprintf(stderr,"Usage: %s <password>",*puParm2);
    uVar2 = 1;
  }
  if (lStack16 != *(long *)(in_FS_OFFSET + 0x28)) {
    uVar2 = __stack_chk_fail();
  }
  return uVar2;
}
{% endhighlight %}


<p>Password kita disimpan dalam variable puParm2[1] yang mana dicocokkan dengan string <strong>this is a much more secure password, i think</strong> , yang merupakan value dari variable uStack32 - Ustack72 dengan fungsi <strong>strcmp</strong>. Disini kita dapat mengetahui flag secara langsung melalui hasil decompile atau dengan menjalankan program sebagai berikut.</p>



./basic-pass-2-linux 'this is a much more secure password, i thin



<p>Flag : bcactf{its_another_password</p>



<h4>basic-pass-3  200 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_11-22-44.png" alt="" class="wp-image-128"/>



<p>Pertama kita coba jalankan nc challenges.ctfd.io 30133 , berikut hasilnya</p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-04-57.png" alt="" class="wp-image-129"/>



<p>Karena kita tahu format flagnya adalah bcactf{} maka saya coba menginputkan b saja , hasilnya seperti diatas.</p>



<p>Jadi program akan mengeluarkan angka 1 jika input kita cocok dengan karakter yang ada index tersebut. contohnya jika saya menginputkan b sebanyak 38 kali (sesuai panjang flag) maka akan keluar angka 1 di index yang karakternya b.</p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-07-54.png" alt="" class="wp-image-130"/>



<p>Jadi disini saya langsung membuat script automation untuk melakukan percobaan semua string yang printable lalu melakukan print terhadap flagnya.</p>


{% highlight python %}
import string
from pwn import *

length=38
flag=["" for i in range(length)]
r=remote('challenges.ctfd.io',30133)
for i in string.printable[:-5]:
  pl=i*length
  r.recvuntil('Enter the password.\n')
  r.sendline(pl)
  a= r.recvline()
  for j,k in enumerate(a[:-2]):
    if('1'==k):
      flag[j]=i
  print "".join(flag)
{% endhighlight %}


<p>Flag : bcactf{y0u_4r3_4_m4573rm1nD!_Ym9vbGlu}</p>



<h4>compression  200 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-10-35.png" alt="" class="wp-image-131"/>



<p>Disini kita diberikan sebuah file yang bernama 999 , tanpa ekstensi . Namun kita dapat tahu file tersebut jenis apa dari command file di linux , jadi disini intinya kita tinggal memberikan ekstensi lalu extract ( atau langsung extract jika bisa ) , dan terdapat juga file hasil dari hexdump, kita dapat mengembalikannya dengan command berikut .</p>


{% highlight raw %}
awk '{print $2,$3,$4,$5,$6,$7,$8,$9}' filehexdump > filebaru
{% endhighlight %}


<p>Kemudian jika sudah kita kembalikan lagi menjadi file aslinya dengan command berikut.</p>


{% highlight raw %}
xxd -r -p filebaru  > filebaru.extension
{% endhighlight %}


<p>Jika sudah kita lakukan hal yang sama , yaitu extract dan extract sampai menemukan sebuah file dengan  nama 000 yang berisi flag.</p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-29-09.png" alt="" class="wp-image-132"/>



<p>Flag : bcactf{A_l0t_0f_c0mPr3s510n}</p>



<h2 id="mce_2">Category : Web</h2>



<h4 id="mce_6">the-inspector  50 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-43-30.png" alt="" class="wp-image-140"/>



<p>Disini kita cukup melakukan inspect element sesuai dengan judul soal dan muncullah flagnya</p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-45-01.png" alt="" class="wp-image-144"/>



<p>Flag : bcactf{1nsp3ct_3l3m3nt} </p>



<h4 id="mce_72">wite-out  50 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-43-41.png" alt="" class="wp-image-141"/>



<p>Terdapat sebuah string di bawah challenge description dengan warna yang sama dengan background dan string tersebut adalah flagnya</p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-45-26.png" alt="" class="wp-image-145"/>



<p>Flag : bcactf{17s_r1gh7_h3r3_1n_wh1t3_1397856}</p>



<h4 id="mce_72">dig-dug  100 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-43-51.png" alt="" class="wp-image-142"/>



<p>Disini kita cukup jalankan command berikut untuk mencari dns record berupa TXT pada domain tersebut.</p>


{% highlight raw %}
dig -t txt hole.sketchy.de
{% endhighlight %}


<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-46-04.png" alt="" class="wp-image-146"/>



<p>Flag : bcactf{d1g-f0r-h073s-w/-dns-8044323}</p>



<h4 id="mce_72">cookie-clicker 150 Pts</h4>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-44-18.png" alt="" class="wp-image-143"/>



<p>Berikut adalah tampilan ketika kita membuka link tersebut</p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-47-45.png" alt="" class="wp-image-147"/>



<p>Ketika kita melakukan klik pada cookies maka angka cookie tersebut akan bertambah 1,kemudian saya coba klik shop.</p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-47-54-1024x358.png" alt="" class="wp-image-148"/>



<p>Dan ternyata kita membutuhkan sekian banyak cookies untuk dapat membeli flag,namun berdasarkan judulnya dapat kita ketahui bahwa soal ini berhubungan dengan cookie. Selanjutnya saya coba untuk membeli flag dan berikut hasilnya</p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-48-01.png" alt="" class="wp-image-149"/>



<p>Saya coba untuk mengamati http request yang dilakukan saat mengklik buy flag tadi.</p>



<p> <br></p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-48-35.png" alt="" class="wp-image-150"/>



<p>Dan ternyata terdapat paramter cookies pada bagian cookie,dan saya ubah menjadi sejumlah yang dibutuhkan lalu coba untuk melakukan send kembali.</p>



<p>Berikut hasilnya</p>



<img src="/assets/images/post/bcactf/Screenshot_2019-07-03_13-48-47.png" alt="" class="wp-image-151"/>



<p>Flag : bcactf{c00k13s_c71ck3d_34a2344d}</p>



<p></p>



<p>Terima kasih telah membaca :) </p>