---
title: TUCTF 2019 Reverse Engineering
layout: post
date: '2019-12-16 17:00:00'
image: "/assets/images/markdown.jpg"
tag:
- reverse engineering
- ctf
star: true
category: blog
author: kosong
description: TUCTF 2019 Reverse Engineering Category
---

# Faker

The first thing i do is try to run the binary and give some input
{% highlight raw %}
./faker
{% endhighlight %}

![Markdown Image][1]

From the result we know that there is no one output is the real flag , so the next thing i do is try to decompile it with ghidra.

{% highlight c %}

void main(void)

{
  int iVar1;
  long in_FS_OFFSET;
  char local_38 [40];
  undefined8 local_10;
  
  local_10 = *(undefined8 *)(in_FS_OFFSET + 0x28);
  setvbuf(stdout,(char *)0x0,2,0x14);
  setvbuf(stdin,(char *)0x0,2,0x14);
  do {
    printMenu();
    read(0,local_38,0x20);
    iVar1 = atoi(local_38);
    if (iVar1 == 4) {
                    /* WARNING: Subroutine does not return */
      exit(0);
    }
    if (iVar1 < 5) {
      if (iVar1 == 3) {
        C();
      }
      else {
        if (3 < iVar1) goto LAB_0010151c;
        if (iVar1 == 1) {
          A();
        }
        else {
          if (iVar1 != 2) goto LAB_0010151c;
          B();
        }
      }
    }
    else {
LAB_0010151c:
      puts("Unknown Input");
    }
    puts("");
  } while( true );
}


{% endhighlight %}

From pseudocode we know that when we input >=5 the binary will print "Unknown Input" and 1,2,3 is fake flag also 4 is exit , so where is the flag?

So i try to list all function and there is a function called thisone and i try to open it

![Markdown Image][2]

{% highlight c %}

void thisone(void)

{
  printFlag("\\PJ\\fC|)L0LTw@Yt@;Twmq0Lw|qw@w2$a@0;w|)@awmLL|Tw|)LwZL2lhhL0k");
  return;
}


{% endhighlight %}

and here is the printFlag function

{% highlight c %}

void printFlag(char *pcParm1)

{
  char *__dest;
  size_t sVar1;
  int local_30;
  
  __dest = (char *)malloc(0x40);
  memset(__dest,0,0x40);
  strcpy(__dest,pcParm1);
  sVar1 = strlen(__dest);
  local_30 = 0;
  while (local_30 < (int)sVar1) {
    __dest[(long)local_30] =
         (char)((int)((((int)__dest[(long)local_30] ^ 0xfU) - 0x1d) * 8) % 0x5f) + ' ';
    local_30 = local_30 + 1;
  }
  puts(__dest);
  return;
}


{% endhighlight %}

Now we have two solution to solve this challenge, first call the thisone function or decode the cipher text ( flag ) with the printFlag algorithm

## 1. Call thisone function
 Here we will use gdb to call thisone function , so first open the binary with gdb 
 {% highlight raw %}
gdb -q faker
{% endhighlight %}
After run this gdb command to call thisone function
{% highlight raw %}
b main
r
jump thisone
{% endhighlight %}

That commands mean first breakpoint at the beginning main function after that run the binary with that breakpoint and then jump to thisone function ( call thisone function ). 
Here is the result :

![Markdown Image][4]

## 2. Decode with printFlag algorithm
In this section i wil convert that pseudocode to python script and here is the script

{% highlight python %}

pcParm1='\\PJ\\fC|)L0LTw@Yt@;Twmq0Lw|qw@w2$a@0;w|)@awmLL|Tw|)LwZL2lhhL0k'
__dest=[i for i in pcParm1]
sVar1=len(__dest)
local_30=0
while (local_30 < sVar1):
	__dest[local_30] = chr(((((ord(__dest[local_30]) ^ 0xf) - 0x1d) * 8) % 0x5f) + ord(' '))
	local_30 = local_30 + 1
print "".join(__dest)

{% endhighlight %}

#### Flag : TUCTF{7h3r35_4lw4y5_m0r3_70_4_b1n4ry_7h4n_m3375_7h3_d3bu663r}

# core
Given file run.c and core where core cannot be run , so the first thing i do is try to open run.c to know the program flow
{% highlight c %}
#include <stdio.h>  // prints
#include <stdlib.h> // malloc
#include <string.h> // strcmp
#include <unistd.h> // read
#include <fcntl.h>  // open
#include <unistd.h> // close
#include <time.h>   // time

#define FLAG_LEN 64
char flag[FLAG_LEN];

void xor(char *str, int len) {
	for (int i = 0; i < len; i++) {
		str[i] = str[i] ^ 1;
	}
}

int main() {
    setvbuf(stdout, NULL, _IONBF, 20);
    setvbuf(stdin, NULL, _IONBF, 20);

	// Read the flag
	memset(flag, 0, FLAG_LEN);
	printf("> ");
	int len = read(0, flag, FLAG_LEN);

	xor(flag, len);

	char buf[32];
	read(0, buf, 128);

    return 0;
}

{% endhighlight %}
From the source code we know that there is operation where the flag is xoring by 1 , because we know format flag is TUCTF{} so we can find xored flag .

{% highlight raw %}
Python 2.7.12 (default, Oct  8 2019, 14:14:10) 
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> a='TUCTF'
>>> b=''
>>> for i in a:
...     b+=chr(ord(i)^1)
... 
>>> b
'UTBUG'
{% endhighlight %}

After we know the xored flag then we strings and grep core file with xored flag
{% highlight raw %}
strings core | grep UTBUG
{% endhighlight %}

Here the result

![Markdown Image][5]

After that just xor it with 1 to get the real flag
{% highlight raw %}

Python 2.7.12 (default, Oct  8 2019, 14:14:10) 
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> a='UTBUGzb1s2^etlq>^O2w2s^i25se^1g^x1t|'
>>> flag=''
>>> for i in a:
...     flag+=chr(ord(i)^1)
... 
>>> flag
'TUCTF{c0r3_dump?_N3v3r_h34rd_0f_y0u}'

{% endhighlight %}

#### Flag : TUCTF{c0r3_dump?_N3v3r_h34rd_0f_y0u}

# Object
Given file run.o and cannot be run , so i try to open it with ghidra

{% highlight c %}

void main(void)

{
  char cVar1;
  byte bVar2;
  char *pcVar3;
  char cVar4;
  undefined2 uVar5;
  long lVar6;
  byte extraout_DL;
  char unaff_BH;
  long lVar7;
  undefined8 local_10;
  
  lVar6 = 0x14;
  lVar7 = stdout;
  pcVar3 = (char *)func_0x00201115(stdout,0,2);
  *pcVar3 = *pcVar3 + (char)pcVar3;
  *(char *)(lVar7 + lVar6) = *(char *)(lVar7 + lVar6) + unaff_BH;
  *pcVar3 = *pcVar3 + (char)pcVar3;
  uVar5 = 0x14;
  pcVar3 = (char *)func_0x00201133(pcVar3,0,2);
  cVar4 = (char)uVar5;
  cVar1 = (char)pcVar3;
  *pcVar3 = *pcVar3 + cVar1;
  *pcVar3 = *pcVar3 + cVar1;
  bVar2 = cVar1 + (char)((ushort)uVar5 >> 8);
  _bVar2 = (byte *)((ulong)pcVar3 & 0xffffffffffffff00 | (ulong)bVar2);
  *_bVar2 = *_bVar2 + bVar2 + (*_bVar2 < extraout_DL);
  *_bVar2 = *_bVar2 + bVar2;
  *_bVar2 = *_bVar2 + bVar2;
  pcVar3 = (char *)func_0x0020116f(local_10,0,0x40);
  *pcVar3 = *pcVar3 + (char)pcVar3;
  *pcVar3 = *pcVar3 + (char)pcVar3;
  *pcVar3 = *pcVar3 + unaff_BH + cVar4;
  pcVar3 = (char *)func_0x00201158();
  *pcVar3 = *pcVar3 + (char)pcVar3;
  *pcVar3 = *pcVar3 + (char)pcVar3;
  pcVar3 = (char *)func_0x002011a0(&DAT_00100218,pcVar3);
  *pcVar3 = *pcVar3 + (char)pcVar3;
  *pcVar3 = *pcVar3 + (char)pcVar3;
  pcVar3 = (char *)func_0x00201188("pass: %s\n",pcVar3);
  *pcVar3 = *pcVar3 + (char)pcVar3;
  *pcVar3 = *pcVar3 + (char)pcVar3;
  return;
}


{% endhighlight %}
There is two function , main and checkPassword . I think main function only get the input and after that pass it to checkPassword function .
{% highlight c %}

/* WARNING: Instruction at (ram,0x001000a6) overlaps instruction at (ram,0x001000a5)
    */

void checkPassword(long lParm1,undefined8 uParm2,undefined8 uParm3,undefined8 uParm4)

{
  byte *pbVar1;
  char cVar2;
  byte bVar3;
  char *pcVar4;
  int *piVar5;
  char cVar6;
  char unaff_BL;
  undefined7 unaff_00000019;
  uint local_10;
  int local_c;
  
  cVar6 = (char)((ulong)uParm4 >> 8);
  pcVar4 = (char *)func_0x00201024(lParm1);
  *pcVar4 = *pcVar4 + (char)pcVar4;
  *pcVar4 = *pcVar4 + (char)pcVar4;
  if (local_c == (int)pcVar4 + 0x15b) {
    local_10 = 0;
  }
  else {
    piVar5 = (int *)func_0x00201046("Close, but no flag");
    cVar2 = (char)piVar5;
    *(char *)piVar5 = *(char *)piVar5 + cVar2;
    *(char *)piVar5 = *(char *)piVar5 + cVar2;
    *piVar5 = *piVar5 + 0x45c70000;
    *(char *)piVar5 = *(char *)piVar5 + cVar2;
    *(char *)piVar5 = *(char *)piVar5 + cVar2;
  }
  while ((int)local_10 < passlen) {
    if ((byte)(~(*(char *)(lParm1 + (long)(int)local_10) << 1) ^ 0xaaU) ==
        password[(long)(int)local_10]) {
      local_10 = local_10 + 1;
    }
    else {
      pcVar4 = (char *)func_0x002010b2("Incorrect password\nError at character: %d\n",
                                       (ulong)local_10);
      bVar3 = (byte)pcVar4;
      *pcVar4 = *pcVar4 + bVar3;
      *pcVar4 = *pcVar4 + bVar3;
      unaff_BL = unaff_BL + cVar6;
      pbVar1 = (byte *)(CONCAT71(unaff_00000019,unaff_BL) + -0x74fe07bb);
      *pbVar1 = *pbVar1 & bVar3;
    }
  }
  pcVar4 = (char *)func_0x002010cc("Correct Password!");
  *pcVar4 = *pcVar4 + (char)pcVar4;
  *pcVar4 = *pcVar4 + (char)pcVar4;
  return;
}


{% endhighlight %}

from pseudocode we know that there is checking on this line

{% highlight c %}
  while ((int)local_10 < passlen) {
    if ((byte)(~(*(char *)(lParm1 + (long)(int)local_10) << 1) ^ 0xaaU) ==
        password[(long)(int)local_10]) {
      local_10 = local_10 + 1;
    }
{% endhighlight %}

we know that lParm1 is our input and passlen is defined as 0000002Ch or 44 in decimal

{% highlight nasm %}
                             //
                             // .data 
                             // SHT_PROGBITS  [0x180 - 0x183]
                             // ram: 00100180-00100183
                             //
                             passlen                                         XREF[3]:     Entry Point(*), 
                                                                                          checkPassword:001000a5(R), 
                                                                                          _elfSectionHeaders::000000d0(*)  
        00100180 2c 00 00 00     undefined4 0000002Ch
				
{% endhighlight %}

and here is the password value

{% highlight nasm %}
                             //
                             // .rodata 
                             // SHT_PROGBITS  [0x188 - 0x226]
                             // ram: 00100188-00100226
                             //
                             DAT_00100188                                    XREF[3]:     checkPassword:0010006b(R), 
                                                                                          00100228(*), 
                                                                                          _elfSectionHeaders::00000150(*)  
        00100188 fd              ??         FDh
        00100189 ff              ??         FFh
        0010018a d3              ??         D3h
        0010018b fd              ??         FDh
        0010018c d9              ??         D9h
        0010018d a3              ??         A3h
        0010018e 93              ??         93h
        0010018f 35              ??         35h    5
        00100190 89              ??         89h
        00100191 39              ??         39h    9
        00100192 b1              ??         B1h
        00100193 3d              ??         3Dh    =
        00100194 3b              ??         3Bh    ;
        00100195 bf              ??         BFh
        00100196 8d              ??         8Dh
        00100197 3d              ??         3Dh    =
        00100198 3b              ??         3Bh    ;
        00100199 37              ??         37h    7
        0010019a 35              ??         35h    5
        0010019b 89              ??         89h
        0010019c 3f              ??         3Fh    ?
        0010019d eb              ??         EBh
        0010019e 35              ??         35h    5
        0010019f 89              ??         89h
        001001a0 eb              ??         EBh
        001001a1 91              ??         91h
        001001a2 b1              ??         B1h
        001001a3 33              ??         33h    3
        001001a4 3d              ??         3Dh    =
        001001a5 83              ??         83h
        001001a6 37              ??         37h    7
        001001a7 89              ??         89h
        001001a8 39              ??         39h    9
        001001a9 eb              ??         EBh
        001001aa 3b              ??         3Bh    ;
        001001ab 85              ??         85h
        001001ac 37              ??         37h    7
        001001ad 3f              ??         3Fh    ?
        001001ae eb              ??         EBh
        001001af 99              ??         99h
        001001b0 8d              ??         8Dh
        001001b1 3d              ??         3Dh    =
        001001b2 39              ??         39h    9
        001001b3 af              ??         AFh
{% endhighlight %}

we can find the password in two ways , first bruteforce it ( because there is a check per index , so bruteforcing is ez for this challenge ) or reverse the check function , my solution in this challenge is reverse the function and here the solver 

{% highlight python %}
a=[0xFD,0xFF,0xD3,0xFD,0xD9,0xA3,0x93,0x35,0x89,0x39,0xB1,0x3D,0x3B,0xBF,0x8D,0x3D,0x3B,0x37,0x35,0x89,0x3F,0xEB,0x35,0x89,0xEB,0x91,0xB1,0x33,0x3D,0x83,0x37,0x89,0x39,0xEB,0x3B,0x85,0x37,0x3F,0xEB,0x99,0x8D,0x3D,0x39,0xAF]
flag=""
for i in a:
	flag+=chr(((0xff-i)^0xAA)/2)
print flag
{% endhighlight %}

#### Flag : TUCTF{c0n6r47ul4710n5_0n_br34k1n6_7h15_fl46}

##### Author : kosong

[1]: /assets/images/post/tuctf/1.png
[2]: /assets/images/post/tuctf/2.png
[3]: /assets/images/post/tuctf/3.png
[4]: /assets/images/post/tuctf/4.png
[5]: /assets/images/post/tuctf/5.png