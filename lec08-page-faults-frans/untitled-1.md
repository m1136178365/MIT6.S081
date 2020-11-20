# Untitled

接下来我将看一下RISC-V提供的基本的机制和基本的信息，并且我想过一个功能清单，来帮助我们理解如何能够使用page fault handler来修复page table并做一些有趣的事情。

我们首先来看一下allocation，或者更具体的说sbreak。sbreak是XV6提供的系统调动，它使得用户应用程序能扩大自己的heap。当一个应用程序启动的时候，sbrk指向的是heap的最底端，stack的最顶端。同时这也是p-&gt;sz指向的位置。

![](../.gitbook/assets/image%20%28306%29.png)

当调用sbrk时，比如以参数12345调用，它的参数代表了你想要分配的page数量，sbrk会扩展stack的上边界。

![](../.gitbook/assets/image%20%28304%29.png)

这意味着，当sbrk实际发生或者被调用的时候，内核会分配一些物理内存，并将这些内存映射到用户应用程序的地址空间，然后将内存内容初始化为0，再返回sbrk系统调用。这样，应用程序可以通过多次调用sbrk系统调用来增加它所需要的内存。类似的，应用程序还通过给sbrk传入负数作为参数，来减少或者压缩它的地址空间。但是现在我们只关注增加地址空间。

在XV6中，sbrk实现的是eager allocation。这表示了，一旦调用了sbrk，内核会立即分配应用程序需要的物理内存。但是实际上，对于应用程序来说很难预测自己需要多少内存，所以通常来说，应用程序倾向于申请多于自己所需要的内存。这意味着，地址空间会增加许多，但是有部分内存永远也不会被应用程序所使用。你或许会认为这里很蠢，怎么能这样呢？你可以设想你写了一个应用程序，读取了一些输入，通过一个矩阵进行一些运算，你需要为最坏的情况做准备，比如说为最大的矩阵分配内存，但是应用程序可能永远也用不上这些内存。






