---
title: 逆向之Crack Me第二期
date: 2019-08-19 20:53:52
tags: 逆向工程
---

#### <center> 破解Crack Me-AfKayAs</center>

不多哔哔，直接上菜

---

1. 爆破法：

   ```
   0040258B     /74 58           je      short 004025E5
   0040258D   . |68 801B4000     push    00401B80                                  ;  You Get It
   00402592   . |68 9C1B4000     push    00401B9C                                  ;  \r\n
   00402597   . |FFD7            call    edi
   00402599   . |8BD0            mov     edx, eax
   0040259B   . |8D4D E8         lea     ecx, dword ptr [ebp-18]
   0040259E   . |FFD3            call    ebx
   004025A0   . |50              push    eax
   004025A1   . |68 A81B4000     push    00401BA8                                  ;  KeyGen It Now
   004025A6   . |FFD7            call    edi
   004025A8   . |8D4D 94         lea     ecx, dword ptr [ebp-6C]
   004025AB   . |8945 CC         mov     dword ptr [ebp-34], eax
   004025AE   . |8D55 A4         lea     edx, dword ptr [ebp-5C]
   004025B1   . |51              push    ecx
   004025B2   . |8D45 B4         lea     eax, dword ptr [ebp-4C]
   004025B5   . |52              push    edx
   004025B6   . |50              push    eax
   004025B7   . |8D4D C4         lea     ecx, dword ptr [ebp-3C]
   004025BA   . |6A 00           push    0
   004025BC   . |51              push    ecx
   004025BD   . |C745 C4 0800000>mov     dword ptr [ebp-3C], 8
   004025C4   . |FF15 10414000   call    dword ptr [<&MSVBVM50.#595>]              ;  MSVBVM50.rtcMsgBox
   004025CA   . |8D4D E8         lea     ecx, dword ptr [ebp-18]
   004025CD   . |FF15 80414000   call    dword ptr [<&MSVBVM50.__vbaFreeStr>]      ;  MSVBVM50.__vbaFreeStr
   004025D3   . |8D55 94         lea     edx, dword ptr [ebp-6C]
   004025D6   . |8D45 A4         lea     eax, dword ptr [ebp-5C]
   004025D9   . |52              push    edx
   004025DA   . |8D4D B4         lea     ecx, dword ptr [ebp-4C]
   004025DD   . |50              push    eax
   004025DE   . |8D55 C4         lea     edx, dword ptr [ebp-3C]
   004025E1   . |51              push    ecx
   004025E2   . |52              push    edx
   004025E3   . |EB 56           jmp     short 0040263B
   004025E5   > \68 C81B4000     push    00401BC8                                  ;  You Get Wrong
   004025EA   .  68 9C1B4000     push    00401B9C                                  ;  \r\n
   004025EF   .  FFD7            call    edi
   ```

   在0040258B处用nop填充，破解完毕！

2. 找算法：（就为了这玩意儿）

   翻到最近的一个ret后面，在push ebp处下断点：

   ```
   00402412   .  50              push    eax                                       ; /name
   00402413   .  8B1A            mov     ebx, dword ptr [edx]                      ; |
   00402415   .  FF15 E4404000   call    dword ptr [<&MSVBVM50.__vbaLenBstr>]      ; \__vbaLenBstr
   0040241B   .  8BF8            mov     edi, eax                                  ;  name的字符串长度，edi=2
   0040241D   .  8B4D E8         mov     ecx, dword ptr [ebp-18]                   ;  ecx=12
   00402420   .  69FF FB7C0100   imul    edi, edi, 17CFB                           ;  edi=edi*0x17CFB=2*0x17CFB=0x2 F9F6
   00402426   .  51              push    ecx                                       ; /把2压栈
   00402427   .  0F80 91020000   jo      004026BE                                  ; |溢出跳转
   0040242D   .  FF15 F8404000   call    dword ptr [<&MSVBVM50.#516>]              ; \rtcAnsiValueBstr
   00402433   .  0FBFD0          movsx   edx, ax                                   ;  edx=ax=0x31
   00402436   .  03FA            add     edi, edx                                  ;  edi=edi+edx=0x2 F9F6+0x31=0x2 FA27
   00402438   .  0F80 80020000   jo      004026BE
   0040243E   .  57              push    edi
   0040243F   .  FF15 E0404000   call    dword ptr [<&MSVBVM50.__vbaStrI4>]        ;  MSVBVM50.__vbaStrI4
   00402445   .  8BD0            mov     edx, eax                                  ;  edx=eax=195111；十进制表示
   00402447   .  8D4D E0         lea     ecx, dword ptr [ebp-20]
   0040244A   .  FF15 70414000   call    dword ptr [<&MSVBVM50.__vbaStrMove>]      ;  MSVBVM50.__vbaStrMove
   00402450   .  8BBD 50FFFFFF   mov     edi, dword ptr [ebp-B0]                   ;  edi=021DA33C
   00402456   .  50              push    eax
   00402457   .  57              push    edi
   00402458   .  FF93 A4000000   call    dword ptr [ebx+A4]
   0040245E   .  85C0            test    eax, eax                                  ;  =0
   00402460   .  7D 12           jge     short 00402474
   00402462   .  68 A4000000     push    0A4
   00402467   .  68 5C1B4000     push    00401B5C
   0040246C   .  57              push    edi
   0040246D   .  50              push    eax
   0040246E   .  FF15 04414000   call    dword ptr [<&MSVBVM50.__vbaHresultCheckOb>;  MSVBVM50.__vbaHresultCheckObj
   00402474   >  8D45 E0         lea     eax, dword ptr [ebp-20]
   00402477   .  8D4D E4         lea     ecx, dword ptr [ebp-1C]
   0040247A   .  50              push    eax
   0040247B   .  8D55 E8         lea     edx, dword ptr [ebp-18]
   0040247E   .  51              push    ecx
   0040247F   .  52              push    edx
   00402480   .  6A 03           push    3
   00402482   .  FF15 5C414000   call    dword ptr [<&MSVBVM50.__vbaFreeStrList>]  ;  MSVBVM50.__vbaFreeStrList
   00402488   .  83C4 10         add     esp, 10
   0040248B   .  8D45 D4         lea     eax, dword ptr [ebp-2C]
   0040248E   .  8D4D D8         lea     ecx, dword ptr [ebp-28]
   00402491   .  8D55 DC         lea     edx, dword ptr [ebp-24]
   00402494   .  50              push    eax
   00402495   .  51              push    ecx
   00402496   .  52              push    edx
   00402497   .  6A 03           push    3
   00402499   .  FF15 F4404000   call    dword ptr [<&MSVBVM50.__vbaFreeObjList>]  ;  MSVBVM50.__vbaFreeObjList
   0040249F   .  8B06            mov     eax, dword ptr [esi]
   004024A1   .  83C4 10         add     esp, 10
   004024A4   .  56              push    esi
   004024A5   .  FF90 04030000   call    dword ptr [eax+304]
   004024AB   .  8B1D 0C414000   mov     ebx, dword ptr [<&MSVBVM50.__vbaObjSet>]  ;  MSVBVM50.__vbaObjSet
   004024B1   .  50              push    eax
   004024B2   .  8D45 DC         lea     eax, dword ptr [ebp-24]
   004024B5   .  50              push    eax
   004024B6   .  FFD3            call    ebx                                       ;  <&MSVBVM50.__vbaObjSet>
   004024B8   .  8BF8            mov     edi, eax
   004024BA   .  8D55 E8         lea     edx, dword ptr [ebp-18]
   004024BD   .  52              push    edx
   004024BE   .  57              push    edi
   004024BF   .  8B0F            mov     ecx, dword ptr [edi]
   004024C1   .  FF91 A0000000   call    dword ptr [ecx+A0]
   004024C7   .  85C0            test    eax, eax
   004024C9   .  7D 12           jge     short 004024DD
   004024CB   .  68 A0000000     push    0A0
   004024D0   .  68 5C1B4000     push    00401B5C
   004024D5   .  57              push    edi
   004024D6   .  50              push    eax
   004024D7   .  FF15 04414000   call    dword ptr [<&MSVBVM50.__vbaHresultCheckOb>;  MSVBVM50.__vbaHresultCheckObj
   004024DD   >  56              push    esi
   004024DE   .  FF95 40FFFFFF   call    dword ptr [ebp-C0]
   004024E4   .  50              push    eax
   004024E5   .  8D45 D8         lea     eax, dword ptr [ebp-28]
   004024E8   .  50              push    eax
   004024E9   .  FFD3            call    ebx
   004024EB   .  8BF0            mov     esi, eax
   004024ED   .  8D55 E4         lea     edx, dword ptr [ebp-1C]
   004024F0   .  52              push    edx
   004024F1   .  56              push    esi
   004024F2   .  8B0E            mov     ecx, dword ptr [esi]
   004024F4   .  FF91 A0000000   call    dword ptr [ecx+A0]
   004024FA   .  85C0            test    eax, eax
   004024FC   .  7D 12           jge     short 00402510
   004024FE   .  68 A0000000     push    0A0
   00402503   .  68 5C1B4000     push    00401B5C
   00402508   .  56              push    esi
   00402509   .  50              push    eax
   0040250A   .  FF15 04414000   call    dword ptr [<&MSVBVM50.__vbaHresultCheckOb>;  MSVBVM50.__vbaHresultCheckObj
   00402510   >  8B45 E8         mov     eax, dword ptr [ebp-18]                   ;  密码=123
   00402513   .  8B4D E4         mov     ecx, dword ptr [ebp-1C]                   ;  上次计算出的十进制数
   00402516   .  8B3D 00414000   mov     edi, dword ptr [<&MSVBVM50.__vbaStrCat>]  ;  MSVBVM50.__vbaStrCat
   0040251C   .  50              push    eax
   0040251D   .  68 701B4000     push    00401B70                                  ;  AKA-
   00402522   .  51              push    ecx                                       ; /String
   00402523   .  FFD7            call    edi                                       ; \__vbaStrCat
   00402525   .  8B1D 70414000   mov     ebx, dword ptr [<&MSVBVM50.__vbaStrMove>] ;  MSVBVM50.__vbaStrMove
   0040252B   .  8BD0            mov     edx, eax                                  ;  edx=eax=AKA-195111
   0040252D   .  8D4D E0         lea     ecx, dword ptr [ebp-20]
   00402530   .  FFD3            call    ebx                                       ;  <&MSVBVM50.__vbaStrMove>
   00402532   .  50              push    eax
   00402533   .  FF15 28414000   call    dword ptr [<&MSVBVM50.__vbaStrCmp>]       ;  MSVBVM50.__vbaStrCmp
   00402539   .  8BF0            mov     esi, eax
   0040253B   .  8D55 E0         lea     edx, dword ptr [ebp-20]
   0040253E   .  F7DE            neg     esi
   00402540   .  8D45 E8         lea     eax, dword ptr [ebp-18]
   00402543   .  52              push    edx
   00402544   .  1BF6            sbb     esi, esi
   00402546   .  8D4D E4         lea     ecx, dword ptr [ebp-1C]
   00402549   .  50              push    eax
   0040254A   .  46              inc     esi
   0040254B   .  51              push    ecx
   0040254C   .  6A 03           push    3
   0040254E   .  F7DE            neg     esi
   00402550   .  FF15 5C414000   call    dword ptr [<&MSVBVM50.__vbaFreeStrList>]  ;  MSVBVM50.__vbaFreeStrList
   00402556   .  83C4 10         add     esp, 10
   00402559   .  8D55 D8         lea     edx, dword ptr [ebp-28]
   0040255C   .  8D45 DC         lea     eax, dword ptr [ebp-24]
   0040255F   .  52              push    edx
   00402560   .  50              push    eax
   00402561   .  6A 02           push    2
   00402563   .  FF15 F4404000   call    dword ptr [<&MSVBVM50.__vbaFreeObjList>]  ;  MSVBVM50.__vbaFreeObjList
   00402569   .  83C4 0C         add     esp, 0C
   0040256C   .  B9 04000280     mov     ecx, 80020004
   00402571   .  B8 0A000000     mov     eax, 0A
   00402576   .  894D 9C         mov     dword ptr [ebp-64], ecx
   00402579   .  66:85F6         test    si, si
   0040257C   .  8945 94         mov     dword ptr [ebp-6C], eax
   0040257F   .  894D AC         mov     dword ptr [ebp-54], ecx
   00402582   .  8945 A4         mov     dword ptr [ebp-5C], eax
   00402585   .  894D BC         mov     dword ptr [ebp-44], ecx
   00402588   .  8945 B4         mov     dword ptr [ebp-4C], eax
   0040258B      74 58           je      short 004025E5
   ```

   算法很简单，C语言表示：

   ```c
   #include <stdafx.h>
   #include <stdio.h>
   #include <iostream>
     
   char buff[100] = {0};
   int _tmain(int argc, _TCHAR* argv[])
   {
       printf("160CrackMe-002 Name/Serial\r\n\r\n");
       printf("Name:");
       gets_s(buff,100);
       int nLen = strlen(buff);
       if ( nLen > 0 )
       {
           int nRet = nLen * 0x17CFB;
           nRet += buff[0];
           printf("AKA-%d\r\n",nRet);
       }else{
           printf("Input error!\r\n");
       }
       system("pause");
       return 0;
   }
   ```

   完工！！