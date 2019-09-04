---
title: windows编程
date: 2019-07-28 09:31:37
tags: windows
---

### <center>windows编程随笔</center>

---

#### 1.从MessageBox开始

```c++
int MessageBox( 
    HWND hWnd,
    LPCTSTR lpText, 
    LPCTSTR lpCaption,
    UINT wType 
    );

```





观察具体几个参数：

**hWnd**：

为父窗口<u>句柄</u>，指定该对话框的所有者窗口。如果该参数为空(0/NULL)，则该对话框不属于任何窗口。

**lpText**:

显示在对话框中的消息。

**lpCaption**：

在对话框[标题栏](https://baike.baidu.com/item/标题栏)中显示的字符串[表达式](https://baike.baidu.com/item/表达式)。如果该参数为空（vbNullString），则使用默认的“错误”作为对话框的标题。

**wType**：

指定显示按钮的数目及形式

---

- #### 那么问题出现了，句柄代表的到底是什么


*简单的来说，句柄就是类似指针的标识*

1.windows 之所以要设立句柄，根本上源于[内存](http://baike.baidu.com/view/1082.htm)管理机制的问题—[虚拟地址](http://baike.baidu.com/view/1499823.htm)，简而言之数据的地址需要变动，变动以后就需要有人来记录管理变动，（就好像户籍管理一样），因此系统用句柄来记载数据地址的变更。

2.更透彻一点地认识句柄，句柄是一种指向[指针](http://baike.baidu.com/view/159417.htm)的[指针](http://baike.baidu.com/view/159417.htm)但是它的属性是只读的。

---

- #### 实例


那么一个完整的MessageBox便展现出来了

```c++
#include<windows.h>
int WINAPI WinMain(HINSTANCE hinstance, HINSTANCE hprevinstance, PSTR szCmdLine, int iCmdShow) 
{
	MessageBox(NULL, 
     		   TEXT("this is content !"),
               TEXT("this is title !"),
               MB_OK);
	return 0;
}
/*WinMain()函数的原型声明
int WINAPI WinMain(
  HINSTANCE hInstance,//当前运行实例句柄
  HINSTANCE hPrevInstance,//前一个实例句柄
  LPSTR lpCmdLine,//指定命令参数行字符串
  int nCmdShow;//指定窗口的显示状态
)

MessageBox函数声明
int MessageBox(
   HWND hWnd,//所属窗口的句柄
   LPCTSTR lpText,//消息字符串
   LPCTSTR lpCaption,//消息框标题字符串
   UNIT uType//消息框的类型
);*/

```

---

- #### 思考

在`MB_OK`处转到定义：

```c++
#define MB_OK                       0x00000000L
#define MB_OKCANCEL                 0x00000001L
#define MB_ABORTRETRYIGNORE         0x00000002L
#define MB_YESNOCANCEL              0x00000003L
#define MB_YESNO                    0x00000004L
#define MB_RETRYCANCEL              0x00000005L
```

对应关系 ：MB_OK 对应十六进制的0？

用 1 来替换 MB_OK 一样可以得到结果吗？

bingo！

```c++
MessageBox(NULL, 
     		   TEXT("this is content !"),
               TEXT("this is title !"),
               0);
```

编译通过！

---

#### 2.匈牙利命名法

对于上面MessageBox函数中奇奇怪怪的参数，它们使用了匈牙利命名法。

  这种标记法非常简单，即变量名以一个或者多个小写字母开始，这些字母表示变量的数据型态。例如：szCmdLine 中的 sz 代表“以0结尾的字符串（StringZero）”；在 hInstance 和 hPrevInstance 中的 h 前缀表示“句柄（Handle）”；在 iCmdShow 中的 i 前缀表示“整型（Integer）”。

当命名结构变量时，可以用结构名（或者结构名的一种缩写）的小写形式作为变量名称的前缀，或者用作整个变量名。例如：msg 变量是 MSG 型态的结构；wndclass 是 WNDCLASSEX 型态的一个结构；ps 是一个 PAINTSTRUCT 结构，rect 是一个 RECT 结构。

匈牙利表示法能够帮助程序写作者及早发现并避免程序中的错误。由于变量名既描述了变量的作用，又描述了其数据型态，就比较容易避免产生数据型态不合的错误。
  

| **前缀** | **数据类型**                                   |
| -------- | ---------------------------------------------- |
| i        | int（整型）                                    |
| c        | char 或 WCHAR 或 TCHAR                         |
| by       | BYTE （无符号字符）                            |
| x, y     | int，表示 x 坐标和 y 坐标                      |
| l        | LONG（长整型）                                 |
| B 或 f   | BOOL（int）；f 表示“flag”                      |
| w        | WORD（无符号短整型）                           |
| dw       | DWORD（无符号长整型）                          |
| fn       | 函数                                           |
| s        | 字符串                                         |
| sz       | 以零结束的字符串                               |
| h        | 句柄                                           |
| p        | 指针                                           |
| cx, cy   | int，表示 x 或 y 的长度，c 表示“count”（计数） |

