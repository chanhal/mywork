

# Chinese fenci 

## tools
- jieba
- wordnet in nltk

The information is included in 06_CBIR/README.md


# 调试segment failt错误
ulimit -a 
ulimit -c unlimited
gcc/g++ -g 生成调试信息

libslog: 作为日志系统
valgrind: 检测内存泄漏，自己编译很方便

## 造成segment fault，产生core dump的可能原因

1. 内存访问越界
a) 由于使用错误的下标，导致数组访问越界
b) 搜索字符串时，依靠字符串结束符来判断字符串是否结束，但是字符串没有正常的使用结束符
c) 使用strcpy, strcat, sprintf, strcmp, strcasecmp等字符串操作函数，将目标字符串读/写爆。应该使用strncpy, strlcpy, strncat, strlcat, snprintf, strncmp, strncasecmp等函数防止读写越界。

2. 多线程程序使用了线程不安全的函数。
3. 多线程读写的数据未加锁保护。对于会被多个线程同时访问的全局数据，应该注意加锁保护，否则很容易造成core dump
4. 非法指针
a) 使用空指针
b) 随意使用指针转换。一个指向一段内存的指针，除非确定这段内存原先就分配为某种结构或类型，或者这种结构或类型的数组，否则不要将它转换为这种结构或类型的指针，而应该将这段内存拷贝到一个这种结构或类型中，再访问这个结构或类型。这是因为如果这段内存的开始地址不是按照这种结构或类型对齐的，那么访问它时就很容易因为bus error而core dump.

5. 堆栈溢出.不要使用大的局部变量（因为局部变量都分配在栈上），这样容易造成堆栈溢出，破坏系统的栈和堆结构，导致出现莫名其妙的错误。


## segment fault 调试
$ gdb a.out xx.corefile
(gdb) bt # 查看堆栈信息
(gdb) f 4 # 不知道，估计是dumpCrash()
(gdb) disassemble # 查看assembler dump

[ 这里参考](https://www.cnblogs.com/wsw-seu/p/10589187.html)

# Deep convolutional learning for Content Based Image Retrieval
1. retraining method
2. max-pooling is superior over sum and stochastic pooling  
3. three retraining methods: unsupervised, with labels, with users's feedback.
fully supervised

1. The rationale behind this approach is rooted to the cluster hypothesis which states that documents in the same cluster are likely to satisfy the same information need.


原来VGG模型权重从何而来？


查看邮件中旷视的语义分割论文：
1. GCN (CVPR2017)
2. DFN (CVPR2018)、
3. BiSeNet (ECCV2018)

https://mp.weixin.qq.com/s/CzODFHWMFl56uZ0ef_6L8g

计算机视觉有三大核心任务——分类、检测、分割

multiview algorithm
SIRF, HOG
SIFT Official Website: http://robwhess.github.io/opensift/

http://www.robots.ox.ac.uk:5000/~vgg/research/affine/index.html
Affine Covariant Regions

### Mac configure opencv
$ brew install opencv@2 
export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig # assure a opencv4.pc file exits in the path
pkg-config --cflags --libs opencv4 # includes both include and library

export PKG_CONFIG_PATH="/usr/local/opt/opencv@2/lib/pkgconfig"

../include/utils.h:12:10: fatal error: 'cxcore.h' file not found

find . -name "*" -type f -size 0c
查找大小为0的文件

find -iname "*.jpg" -print0 | xargs -0 jpeginfo -c | grep -e WARNING -e ERROR
Check the integrity of all JPG files in a directory.

### 查看一个文件是否是JPG
```
bool isAImageFile(const char* fileName) 
{
    FILE* fp;
    int16_t head, tail;
    fp = fopen(fileName, "rb");
    fseek(fp, 0L, SEEK_SET);
    fread(&head, 2, 1, fp);
    fseek(fp,-2L,SEEK_END);
    fread(&tail, 2, 1, fp);
	fclose(fp);
	return (((head & 0x0000ffff) ^ 0x0000ffd8) | (tail & 0x0000ffff) ^ 0x0000ffd9) == 0;
}
```

jpginfo
查看图片信息

### 通用平台数据类型
int8_t
int16_t
int32_t
int64_t

uint8_t
uint16_t
uint32_t
uint64_t

included in #include <stdint.h>

编程中要尽量使用sizeof来计算数据类型的大小
size_t: unsigned signed size of computer word size。
ssize_t: sign size_t
它们也是表示计算机的字长，在32位机器上是int型，在64位机器上long型，从某种意义上来说它们等同于intptr_t和 uintptr_t。它们在stddef.h里面定义。

### GDB调试带参数
$gdb --args ./A V1 V2 V3
$gdb ./A，进入gdb后 r V1 V2 V3
$gdb ./A，进入gdb后 设置参数set args V1 V2 V3 再直接 r。


### vim 16进制显示
vim -b filename
:%!xxd

### vim dos to unix
:set fileformat=unix

### 字符串命名
char sLine[1024] = {0};
char * szLine = sLine;
char * psz = malloc(1024);

https://zhuanlan.zhihu.com/p/54122042


### 大小端

为何要有大小端：https://www.ruanyifeng.com/blog/2016/11/byte-order.html


``` 测试大小端
#include<stdio.h>
int main()
{
short int x;
char x0, x1;
x = 0x1122;
x0 = *((char *)&x);      //把x的低位地址的值赋给x0;
x1 = *((char *)&x + 1);      //把x的高位地址的值赋给x1;
if( x0 == 0x11 && x1 == 0x22)
printf(" This is big-endian \n");
else if( x0 == 0x22 && x1 == 0x11)
printf("This is little-endian \n");
else
printf("呵呵,你这个方法有误啊\n");
return 0;
}
```


ps -aux | grep -E "a.out|PID"


- 统计包含指定字符的行数
命令：cat test.log | grep -c test
如果一行中一个字符出现多次，这个会只统计一次

- 统计包含指定字符的行数

方法1

命令：cat test.log | grep -o test | grep -c test
-o指示 grep显示所有匹配的地方，并且每一个匹配单独一行输出。
命令执行结果：

方法2

命令：cat test.log | grep -o test | wc -l
-o指示 grep显示所有匹配的地方，并且每一个匹配单独一行输出。
wc -l可以代替 grep -c test统计行数

不包含jpg, png 一整行
cat source.txt | grep -E -v "*.jpg|*.png|*.jgeg" > source_without_jpg_png.txt

包含转义符号的查找
cat source.txt | grep -E "*\.png" > source_with_png.txt

gdb --args ./darknet ./mydata/saved_im/400006218.jpg

###  vim全词匹配
/\< int\>















