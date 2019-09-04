---
title: 逆向之Crack Me第一期
date: 2019-08-18 19:14:51
tags: 逆向工程
---

#### <center> 破解Crack Me-Acid burn</center>

---

找了一份Crack Me的大合集，来巩固一下所学知识，查漏补缺。话不多说，直接上菜。

----

###### 总览：

该软件由两部分组成，即分别需要破解serial以及name-serial。

----

##### 1.serial的破解

用OD打开，F9运行，随便输入，弹出窗口提示错误：Try Again!!

此处有两种方式：1.用字符串搜索：Try Again!! 2.在MessageBoxA函数处下断点。

第二种方式较为常规，第一种比较简单，此处采用第二种。

断点之后，在最近的return处返回用户代码

```
0019F710   0042A1AE  /CALL to MessageBoxA from Acid_bur.0042A1A9
0019F714   00040A52  |hOwner = 00040A52 ('Acid burn',class='TApplication')
0019F718   0042F58C  |Text = "Try Again!!"
0019F71C   0042F584  |Title = "Failed!"
0019F720   00000000  \Style = MB_OK|MB_APPLMODAL
0019F724   0019F754  Pointer to next SEH record
0019F728   0042A1D0  SE handler
0019F72C   0019F748
0019F730   022A9778
0019F734   022A9778
0019F738   022A674C
0019F73C   022AA4F8
0019F740   000608DE
0019F744   022A9778
0019F748  /0019F774
0019F74C  |0042F509  RETURN to Acid_bur.0042F509 from Acid_bur.0042A170
```

返回至用户代码：

```
0042F4D5     /75 1A         jnz     short 0042F4F1					 ;  此处判断，若不相等，则跳转至0042F4F1
0042F4D7     |6A 00         push    0
0042F4D9  |. |B9 64F54200   mov     ecx, 0042F564                    ;  Congratz!
0042F4DE  |. |BA 70F54200   mov     edx, 0042F570                    ;  God Job dude !! =)
0042F4E3  |. |A1 480A4300   mov     eax, dword ptr [430A48]
0042F4E8  |. |8B00          mov     eax, dword ptr [eax]
0042F4EA  |. |E8 81ACFFFF   call    0042A170
0042F4EF  |. |EB 18         jmp     short 0042F509
0042F4F1  |> \6A 00         push    0                                ;  跳转至此处
0042F4F3  |.  B9 84F54200   mov     ecx, 0042F584                    ;  Failed!
0042F4F8  |.  BA 8CF54200   mov     edx, 0042F58C                    ;  Try Again!!
0042F4FD  |.  A1 480A4300   mov     eax, dword ptr [430A48]
0042F502  |.  8B00          mov     eax, dword ptr [eax]
0042F504  |.  E8 67ACFFFF   call    0042A170
0042F509  |>  33C0          xor     eax, eax						 ; return返回至此处,向上查找关键词
```

在0042F4D5处可使用爆破法，用`nop`替换` jnz short 0042F4F1	`,这个比较简单，不如去查找serial的值

再往上看：

```
0042F4CA  |.  8B45 F0       mov     eax, dword ptr [ebp-10]
0042F4CD  |.  8B55 F4       mov     edx, dword ptr [ebp-C]
0042F4D0  |.  E8 2745FDFF   call    004039FC
0042F4D5      75 1A         jnz     short 0042F4F1
```

在`  jnz  short 0042F4F1`判断指令前，有`call 004039FC`来判断输入数值与原有数值进行比较，即以上两条mov指令。

在0042F4D0处下断点。观察eax与edx的值：

```
EAX 022A7840 ASCII "454564"             ; 此处值为我输入的值
...
EDX 022AA4B4 ASCII "Hello Dude"
...
```

由此猜测Hello Dude为密钥，验证正确

---

##### 2.name-serial的破解

同理，在MessageBoxA处下断点。

````
0019F704   0042A1AE  /CALL to MessageBoxA from Acid_bur.0042A1A9
0019F708   00250A82  |hOwner = 00250A82 ('Acid burn',class='TApplication')
0019F70C   0042FB80  |Text = "Sorry , The serial is incorect !"
0019F710   0042FB74  |Title = "Try Again!"
0019F714   00000000  \Style = MB_OK|MB_APPLMODAL
0019F718   0019F748  Pointer to next SEH record
0019F71C   0042A1D0  SE handler
0019F720   0019F73C
0019F724   02281304
0019F728   00000509
0019F72C   022863FC  ASCII "x鸟"
0019F730   0228A494
0019F734   000C0A8C
0019F738   02281304
0019F73C  /0019F774
0019F740  |0042FB37  RETURN to Acid_bur.0042FB37 from Acid_bur.0042A170        ; 在此处return
````

返回至用户代码：

```
0042FAF8  |.  8B55 F0       mov     edx, dword ptr [ebp-10]			 
0042FAFB  |.  8B45 F4       mov     eax, dword ptr [ebp-C]
0042FAFE  |.  E8 F93EFDFF   call    004039FC
0042FB03      75 1A         jnz     short 0042FB1F					 ; 在此处判断，若不等于则跳转至0042FB1F
0042FB05  |.  6A 00         push    0
0042FB07  |.  B9 CCFB4200   mov     ecx, 0042FBCC                    ;  Congratz !!
0042FB0C  |.  BA D8FB4200   mov     edx, 0042FBD8                    ;  Good job dude =)
0042FB11  |.  A1 480A4300   mov     eax, dword ptr [430A48]
0042FB16  |.  8B00          mov     eax, dword ptr [eax]
0042FB18  |.  E8 53A6FFFF   call    0042A170
0042FB1D  |.  EB 18         jmp     short 0042FB37
0042FB1F  |>  6A 00         push    0								 ; 跳转至此处
0042FB21  |.  B9 74FB4200   mov     ecx, 0042FB74                    ;  Try Again!
0042FB26  |.  BA 80FB4200   mov     edx, 0042FB80                    ;  Sorry , The serial is incorect !
0042FB2B  |.  A1 480A4300   mov     eax, dword ptr [430A48]
0042FB30  |.  8B00          mov     eax, dword ptr [eax]
0042FB32  |.  E8 39A6FFFF   call    0042A170
0042FB37  |>  33C0          xor     eax, eax						 ; 返回至此处，往上看到关键词
```

同理可用爆破法将`jnz  short 0042FB1F`替换，则爆破完成。

往上走，找到最近的几个call

```
0042FAEA  |.  8D55 F0       lea     edx, dword ptr [ebp-10]
0042FAED  |.  8B83 E0010000 mov     eax, dword ptr [ebx+1E0]
0042FAF3  |.  E8 60AFFEFF   call    0041AA58						 ; call1
0042FAF8  |.  8B55 F0       mov     edx, dword ptr [ebp-10]
0042FAFB  |.  8B45 F4       mov     eax, dword ptr [ebp-C]
0042FAFE  |.  E8 F93EFDFF   call    004039FC						 ; call2
0042FB03      75 1A         jnz     short 0042FB1F
```

在第一个call1处下断点，观察eax，ebx，ecx，edx等寄存器的值；

发现为空值；

则继续在第二个call2处下断点：

```
EAX 0222A460 ASCII"CW-4018-CRACKED"
...
EDX 0222A47C ASCII"12313"				; 此处为输入的密码
```

说明 EAX在call1处产生

然后跟进`call  0041AA58`

```
0041AA58  /$  53            push    ebx
0041AA59  |.  56            push    esi
0041AA5A  |.  57            push    edi
0041AA5B  |.  8BFA          mov     edi, edx
0041AA5D  |.  8BF0          mov     esi, eax
0041AA5F  |.  8BC6          mov     eax, esi
0041AA61  |.  E8 A2FFFFFF   call    0041AA08
0041AA66  |.  8BD8          mov     ebx, eax
0041AA68  |.  8BC7          mov     eax, edi
0041AA6A  |.  8BCB          mov     ecx, ebx
0041AA6C  |.  33D2          xor     edx, edx
0041AA6E  |.  E8 E18CFEFF   call    00403754
0041AA73  |.  85DB          test    ebx, ebx
0041AA75  |.  74 0C         je      short 0041AA83
0041AA77  |.  8D4B 01       lea     ecx, dword ptr [ebx+1]
0041AA7A  |.  8B17          mov     edx, dword ptr [edi]
0041AA7C  |.  8BC6          mov     eax, esi
0041AA7E  |.  E8 95FFFFFF   call    0041AA18
0041AA83  |>  5F            pop     edi
0041AA84  |.  5E            pop     esi
0041AA85  |.  5B            pop     ebx
0041AA86  \.  C3            retn
```

在此处跟进，并未发现EAX的ASCII值，即没有发现注册码，则初步判定注册码不是在这里生成的。但是这么想就与之前的猜测完全推翻了，说明我们的想法有问题，即思路有问题。既然他的码不是及时算出来的，那肯定就是事先算好的，我们再次回到产生注册码的CALL那里，向上查找，F8单步进行查看。

```
0042FA4D  |.  A1 6C174300   mov eax,dword ptr ds:[0x43176C]          ;  // name/Tag
0042FA52  |.  E8 D96EFDFF   call 00406930                            ;  // 关键CALL
0042FA57  |.  83F8 04       cmp eax,0x4                              ;  // 判断tag/serial 是否合格
0042FA5A  |.  7D 1D         jge short 0042FA79
0042FA5C  |.  6A 00         push 0x0
0042FA5E  |.  B9 74FB4200   mov ecx,0042FB74                         ;  ASCII 54,"Try Again!"
0042FA63  |.  BA 80FB4200   mov edx,0042FB80                         ;  ASCII 53,"orry , The serial is incorect !"
0042FA68  |.  A1 480A4300   mov eax,dword ptr ds:[0x430A48]
0042FA6D  |.  8B00          mov eax,dword ptr ds:[eax]
0042FA6F  |.  E8 FCA6FFFF   call 0042A170
0042FA74  |.  E9 BE000000   jmp 0042FB37
0042FA79  |>  8D55 F0       lea edx,[local.4]
0042FA7C  |.  8B83 DC010000 mov eax,dword ptr ds:[ebx+0x1DC]
0042FA82  |.  E8 D1AFFEFF   call 0041AA58
0042FA87  |.  8B45 F0       mov eax,[local.4]                        ;  EAX=112233的地址
0042FA8A  |.  0FB600        movzx eax,byte ptr ds:[eax]              ;  第一个字节1=0x31
0042FA8D  |.  F72D 50174300 imul dword ptr ds:[0x431750]             ;  *0x29
0042FA93  |.  A3 50174300   mov dword ptr ds:[0x431750],eax          ;  eax=0x7d9=0x29*0x31
0042FA98  |.  A1 50174300   mov eax,dword ptr ds:[0x431750]
0042FA9D  |.  0105 50174300 add dword ptr ds:[0x431750],eax          ;  加一倍
0042FAA3  |.  8D45 FC       lea eax,[local.1]
0042FAA6  |.  BA ACFB4200   mov edx,0042FBAC
0042FAAB  |.  E8 583CFDFF   call 00403708
0042FAB0  |.  8D45 F8       lea eax,[local.2]
0042FAB3  |.  BA B8FB4200   mov edx,0042FBB8
0042FAB8  |.  E8 4B3CFDFF   call 00403708
0042FABD  |.  FF75 FC       push [local.1]
0042FAC0  |.  68 C8FB4200   push 0042FBC8                            ;  UNICODE "-"
0042FAC5  |.  8D55 E8       lea edx,[local.6]                        ;  12F990
0042FAC8  |.  A1 50174300   mov eax,dword ptr ds:[0x431750]          ;  4018
0042FACD  |.  E8 466CFDFF   call 00406718
0042FAD2  |.  FF75 E8       push [local.6]                           ;  // 注册码中间的值
0042FAD5  |.  68 C8FB4200   push 0042FBC8                            ;  UNICODE "-"
0042FADA  |.  FF75 F8       push [local.2]
0042FADD  |.  8D45 F4       lea eax,[local.3]
0042FAE0  |.  BA 05000000   mov edx,0x5
0042FAE5  |.  E8 C23EFDFF   call 004039AC
0042FAEA  |.  8D55 F0       lea edx,[local.4]                        ;  edx=0012F998
0042FAED  |.  8B83 E0010000 mov eax,dword ptr ds:[ebx+0x1E0]         ;  eax=00A85E4C
0042FAF3  |.  E8 60AFFEFF   call 0041AA58                            ;  // 注册码CALL
0042FAF8  |.  8B55 F0       mov edx,[local.4]                        ;  // EDX=44556677
0042FAFB  |.  8B45 F4       mov eax,[local.3]                        ;  // EAX=CW-4018-CRACKED
0042FAFE  |.  E8 F93EFDFF   call 004039FC
0042FB03      75 1A         jnz short 0042FB1F                       ;  // 这个JNZ条件判断很关键？
0042FB05  |.  6A 00         push 0x0
0042FB07  |.  B9 CCFB4200   mov ecx,0042FBCC
0042FB0C  |.  BA D8FB4200   mov edx,0042FBD8
0042FB11  |.  A1 480A4300   mov eax,dword ptr ds:[0x430A48]
0042FB16  |.  8B00          mov eax,dword ptr ds:[eax]
0042FB18  |.  E8 53A6FFFF   call 0042A170
0042FB1D  |.  EB 18         jmp short 0042FB37                       ;  // 这个跳转是不是很可疑？
0042FB1F  |>  6A 00         push 0x0
0042FB21  |.  B9 74FB4200   mov ecx,0042FB74                         ;  ASCII 54,"ry Again!"
0042FB26  |.  BA 80FB4200   mov edx,0042FB80                         ;  ASCII 53,"orry , The serial is incorect !"

```

**总结：取第一个字母的ASNI的数字，如112233中第一个字符1对应数字0x31，然后用它乘以0x29，结果再自增一倍(即x2)，将得到的数字转为10进制的字符串，在前加上”CW-”,后加上”-CRACKED”，就组成了用户名对应的注册码。**

C++实现：

```c
#include "stdafx.h"
#include "iostream"
  
int _tmain(int argc, _TCHAR* argv[])
{
    printf("Input Name:\r\n");
    // 取第一个字符值
    int cName = getchar();
    if ( cName > 0x21) // 只处理可见字符
    {
        cName *= 0x29; // 乘法
        cName *= 2; // 自增一倍
        printf("Serial: CW-%4d-CRACKED\r\n",cName);
    }else{
        printf("input error!\r\n");
    }
    system("pause");
    return 0;
```



