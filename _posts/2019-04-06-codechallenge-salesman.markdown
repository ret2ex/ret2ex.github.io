---
title: Code Challenge - Salesman
layout: post
date: '2019-04-06 17:00:00'
image: "/assets/images/markdown.jpg"
tag:
- competitive programming
- python
star: true
category: blog
author: kosong
description: Code Challenge - Salesman Category
---

Diberikan soal sebagai berikut.

![Markdown Image][1]
![Markdown Image][2]
![Markdown Image][3]
![Markdown Image][4]
![Markdown Image][5]

Disini saya membuat solver menggunakan bahasa python dan saya akan menjelaskannya per bagian dari kode program yang saya buat.

Pertama kita memasukkan data berupa integer yaitu bilangan **1-50** jadi kita menggunakan **input()** . Ketika input yang diberikan adalah 1 maka program akan mengeluarkan output 1,karena ketika kita menginputkan 1 kita hanya bisa menginputkan **N** atau **E** dan yang pasti paling banyak hanya 1 , namun ketika kita menginputkan bilangan **2-50** maka akan dilakukan pengecekan apakah panjang inputan **setiap baris** sama dengan **panjang kolom**,jika sama maka akan ditambahkan ke **array baris** yang telah dideklarasikan sebelumnya . Berikut potongan kodenya :


{% highlight python %}
m=input()
if(m>1 and m<=50):
	baris=[]
	for i in range(0,m):
		a=raw_input()
		if(len(a)==m):
			for j in a:
				baris.append(j)
elif(m==1):
	print 1
{% endhighlight %}

Variable baris berguna untuk melakukan perubahan struktur dari yang awalnya kita menginput berupa baris menjadi bentuk kolom dan berguna juga untuk melakukan pengecekan apakah terdapat inputan selain N dan E. Berikut ilustrasinya :

{% highlight raw %}
3  
ENN
NEN
EEN

3 <-- m
 _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
|    3   | kolom1 | kolom2 | kolom3 |
| baris1 |   E    |   N    |   N    | \
| baris2 |   N    |   E    |   N    |  | <-- Inputan kita(baris)
| baris3 |   E    |   E    |   N    | /
 _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _

baris = ['E', 'N', 'N', 'N', 'E', 'N', 'E', 'E', 'N']

Selanjutnya kita melakukan pengecekan terhadap value dari array baris
apakah terdapat huruf selain N dan E.

Jika tidak ada maka lanjut ke tahap selanjutnya yaitu membuat kolom.
Disini kita memanfaatkan modulus untuk mendapatkan value dari masing-masing kolom.

Disini kita memanfaatkan looping
imodm
0mod3=0 , setiap value 0 akan menjadi kolom1
1mod3=1 , setiap value 1 akan menjadi kolom2
2mod3=2 , setiap value 2 akan menjadi kolom3
3mod3=0
4mod3=1
dan seterusnya...

Sehingga menghasilkan variable kolom yang isinya sebagai berikut
kolom = ['ENE', 'NEE', 'NNN']
{% endhighlight %}


Berikut kodenya :


{% highlight python %}
m=input()
if(m>1 and m<=50):
	baris=[]
	for i in range(0,m):
		a=raw_input()
		if(len(a)==m):
			for j in a:
				baris.append(j)
	if((baris.count("N")+baris.count("E"))==len(baris)):
		kolom=[]
		for k in range(0,m):		
			n=""
			for i,j in enumerate(baris):
				if(i%m==k): 
					n+=j
			kolom.append(n)
elif(m==1):
	print 1
{% endhighlight %}


Selanjutnya kita tinggal melakukan **perhitungan terhadap banyak huruf yang sama dengan syarat huruf tersebut berurutan.**

Disini saya menggunakan cara yaitu ketika **value dari index ke-(n) pada kolomX sama dengan value dari index ke-(n-1) pada kolomX maka variable count akan ditambah 1** , default value dari variable count sendiri adalah 1,jadi semisal tidak sama maka akan dihitung 1.

Kita analogikan pada array kolom diatas :

{% highlight raw %}
kolom = ['ENE', 'NEE', 'NNN']
rumus
kolom[n]==kolom[n-1],count + 1
kolom[n]!=kolom[n-1],count = 1

contoh penggunaan range pada python
range(0,4) = 0,1,2,3
range(2,7) = 2,3,4,5,6
disini kita memulai dari n=1 dan batasan loopingnya adalah m=3
range(1,3)

kolom[0]='ENE'
count=1
1. kolom[1]==kolom[0] --> N==E (hasilnya false) --> count=1
2. kolom[2]==kolom[1] --> E==N (hasilnya false) --> count=1
hasil count = 1,1

kolom[1]='NEE'
count=1
1. kolom[1]==kolom[0] --> E==N (hasilnya false) --> count=1
2. kolom[2]==kolom[1] --> E==E (hasilnya true) --> count+1 --> count=2
hasil count = 1,2

kolom[2]='NNN'
count=1
1. kolom[1]==kolom[0] --> N==N (hasilnya true) --> count+1 --> count=2
2. kolom[2]==kolom[1] --> N==N (hasilnya true) --> count+1 --> count=3
hasil count = 2,3

Selanjutnya kita jadikan hasil count tersebut menjadi satu variable
misal nama variablenya adalah banyak
banyak = [1,1,1,2,2,3]
{% endhighlight %}

Berikut kodenya :

{% highlight python %}
m=input()
if(m>1 and m<=50):
	baris=[]
	for i in range(0,m):
		a=raw_input()
		if(len(a)==m):
			for j in a:
				baris.append(j)
	if((baris.count("N")+baris.count("E"))==len(baris)):
		kolom=[]
		for k in range(0,m):		
			n=""
			for i,j in enumerate(baris):
				if(i%m==k): 
					n+=j
			kolom.append(n)
		banyak = []
		for l in kolom:
			count=1
			for i in range(1,len(l)):
				if l[i-1]==l[i]:
					count+=1
				else:
					count=1
				banyak.append(count)		
elif(m==1):
	print 1
{% endhighlight %}


Terakhir kita tinggal menggunakan fungsi **max()** pada python untuk mencari nilai tertinggi pada variable **banyak**.

{% highlight python %}
m=input()
if(m>1 and m<=50):
	baris=[]
	for i in range(0,m):
		a=raw_input()
		if(len(a)==m):
			for j in a:
				baris.append(j)
	if((baris.count("N")+baris.count("E"))==len(baris)):
		kolom=[]
		for k in range(0,m):		
			n=""
			for i,j in enumerate(baris):
				if(i%m==k): 
					n+=j
			kolom.append(n)
		banyak = []
		for l in kolom:
			count=1
			for i in range(1,len(l)):
				if l[i-1]==l[i]:
					count+=1
				else:
					count=1
				banyak.append(count)		
		print max(banyak)
elif(m==1):
	print 1
{% endhighlight %}


Dengan demikian selesailah penjelasan untuk soal salesman. 

[1]: /assets/images/post/backupwp/6f36bd0f-f6b7-418a-9ba7-ffe6853864a3.jpeg
[2]: /assets/images/post/backupwp/727fd5ee-035d-43cb-9bb1-12793aefd322.jpeg
[3]: /assets/images/post/backupwp/a6530d64-8c1a-4b35-882f-95404bff81c6.jpeg
[4]: /assets/images/post/backupwp/00da1df3-81b8-478f-b04e-d4de71b12dfb.jpeg
[5]: /assets/images/post/backupwp/f818f873-7569-4fbf-9a1f-f58828678ca4.jpeg