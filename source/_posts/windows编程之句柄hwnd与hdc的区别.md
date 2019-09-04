---
title: windows编程之句柄hwnd与hdc的区别
date: 2019-07-28 11:09:45
tags: windows
---

### <center>谈谈hdc与hwnd的区别</center>

---

#### hWnd 与hDC的概念

**hWnd**(Handle of Window，也可以这么说：h是类型描述，表示句柄；wnd是变量对象描述，表示窗口)是窗口句柄，其中包含窗口的属性。例如，窗口的大小、显示位置、父窗口。

**hDC**(Handle to Device Context)是图像的设备描述表，窗口显示上下文句柄，其中可以进行图形显示。

利用hDC=GetDC(hWnd)，可以获得一个窗口的图形设备描述表。可以通过ReleaseDC()函数释放。

hWnd句柄是描述一个窗口的形状、位置、大小、是否显示、它的父窗口、兄弟窗口、等等的一组数据结构；  
hDC句柄是一个实实在在的用于具体表现这个窗口时，需要对这个窗口有个场合来实现的地方。

 

hWnd是窗体句柄；hDC是设备场景句柄。
hWnd与窗口管理有关；hDC与绘图API（GDI函数）有关。
hWnd是windows给窗口发送消息（事件）用的；hDC是把窗口绘制在屏幕上用的。

有了hWnd，可以使用API的GetDC()函数得到与其相关的hDC：hDC=GetDC(hWnd)。

hWnd与hDC都是句柄，但是**hWnd是窗口句柄而hDC是设备描述表的句柄**。

在Windows标编程设计中，使用了大量的句柄来标识对象。一个句柄是指使用的一个唯一的整数值，即一个4字节（64位程序中为8字节）长的数值，来标识应用程序中的不同对象和同类中的不同的实例，例如：一个窗口、按钮、图标、滚动条、输出设备、孔健、文件等。应用程序能通过句柄来访问相应的对象的信息。但是**句柄不是指针，程序不能利用句柄来直接阅读文件中的信息。如果句柄不在I/O文件中，它是毫无用处的。**我们来看看另一个好理解的说法：在进程的地址空间中设一张表，表里头专门保存一些编号和由这个编号对应一个地址，而由那个地址去引用实际的对象，这个编号跟那个地址在数值上没有任何规律性的联系，纯粹是个映射而已。在Windows系统中，这个编号就叫做"句柄"。

句柄实际上是一种指向某种资源的指针，但与指针又有所不同：HWND是跨进程可见的，而指针从来都是属于某个特定进程的。指针对应着一个数据在内存中的地址，得到了指针就可以自由地修改该数据。Windows并不希望一般程序修改其内部数据结构，因为这样太不安全。所以Windows给每个使用GlobalAlloc等函数声明的内存区域指定一个句柄(本质上仍是一个指针，但不要直接操作它)，平时我们只是在调用API函数时利用这个句柄来说明要操作哪段内存。

---

#### 实例：

​    HWND hwnd;//窗口句柄
​    char szAppName[] = "window1";

//创建窗口
    hwnd = CreateWindow(szAppName, //窗口类型名
            TEXT("The First Experiment"), //窗口实例的标题
            WS_OVERLAPPEDWINDOW, //窗口风格
            CW_USEDEFAULT, //窗口左上角位置坐标值x
            CW_USEDEFAULT, //窗口左上角位置坐标值y
            800, //窗口的宽度
            600, //窗口的高度
            NULL, //父窗口的句柄
            NULL, //主菜单的句柄
            hInstance, //应用程序实例句柄
            NULL );
　　　　//显示窗口
    ShowWindow(hwnd, iCmdShow);
    UpdateWindow(hwnd);
    
    static int nWidth, nHeight;
    HDC hdc;//定义设备环境句柄  
    HBRUSH hB;//定义画笔句刷

case WM_LBUTTONDOWN://按下鼠标左键则用户区被刷成灰色
            nWidth = GetSystemMetrics(SM_CXFULLSCREEN);  //屏幕宽度    
            nHeight = GetSystemMetrics(SM_CYFULLSCREEN); //屏幕高度
            hdc=GetDC(hwnd);
            hB = (HBRUSH)GetStockObject(GRAY_BRUSH);//灰色画刷
            SelectObject(hdc, hB);
            Rectangle(hdc, 0, 0, nWidth, nHeight);//将用户区重新刷成灰色
            DeleteObject(hB);//删除画刷

​            return 0;

