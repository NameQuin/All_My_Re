## 题目名称：EASYMAZE

出题人:**Gyan**

IDA不好用了？因为这是C#

扔进dnspy

进入check函数发现这是一个迷宫

从C经过*走到X,0为下，1为上，2为右，3为左

路径：2220000000221220221221133333311222211220022112220030020002

提交dino{2220000000221220221221133333311222211220022112220030020002}发现不对?

继续审计代码，找到两个关键的地方

![](./.asset/maze_1.png)

第一个地方是将数据转换成十六进制，第二个地方是将这个十六进制的数据拆成前两位和后两位，即  'a' =>0xa =>1010 => array2 = {10,10}，所以我们应该一次"走两步“

即22 =>1010 =>a，以此类推

![](./.asset/maze_2.png)

```c++
//最后部分我是手算的，附上DorinXL师傅的脚本
#include<iostream>
#include<cstring>
using namespace std;
int main(){
	char s[] = "2220000000221220221221133333311222211220022112220030020002"; 
	int a,b;
	char flag[50];
	for(int i = 0;i < 29;i++){
		for(int j = 0;j < 128;j++){
			a = j >> 2;
			b = j & 3;
			if((a == (s[i * 2] - '0')) && (b == (s[i * 2 + 1] - '0'))){
				if(j >= 0 && j <= 9){
					flag[i] = '0' + j;
				}
				else if(j >= 10 && j <= 16){
					flag[i] = 'a' + j -10;
				}
				break;
			}
		}
	}
	for(int i = 0;i < 29;i++){
		cout<<flag[i];
	}
}
```





## 题目名称:嘘!

出题人:**Gyan**

双击后打不开？？？？直接扔进IDA

发现一大堆看不懂的东西!!!

往旁边函数里一看，发现一个创建窗口的函数WinMain

点击即得flag

![](./.asset/xu.png)

## 题目名称:捉迷藏

出题人:**DorinXL**

查壳！出这个题的师傅本来没加壳的，我（**Nameless**）手贱加了:) 

直接upx -d 脱壳 （记得用高版本的upx，不然可能失败）手脱可无视

脱完壳后扔进ida,shift+F12一把梭

在几个有字符串的地方找到数据，然后直接按R

如：

![](./.asset/zhuomicang.png)

找到其他几段后，拼接出来就行

## 题目名称:base64

出题人:**DorinXL**

ida64打开，发现是base64编码加密

看左边函数，会发现ohMyGod函数，进去看后，发现更换了base64的表（这里如果不懂，要去看base64的原理）

写出新表的脚本

```python
key = "Y2R1KgtDM195M3VIN25vK19FYXaCX2OGMWCsJT99"
table = list('ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/')
for i in range(25):
    tmp = table[i]
    table[i] = table[39 -i]
    table[39-i] = tmp

m = ''.join(table)
print(m)
```

得到新表后，用base64的脚本or工具直接解出

![](./.asset/base64.png)

## 题目名称:BabyAndroid

出题人:**Nameless**

直接扔进JEB

![](./.asset/android_1 .png)

进去后发现看不懂，直接decompile

![](./.asset/android_2.png)

稍微代码审计一下发现就是一个简单的异或

![](./.asset/android_3.png)

上脚本

```python
li = [18, 36, 8, 67, 36, 3, 64, 61, 28, 2, 1, 13, 4, 36, 68, 48, 5, 26, 60, 100, 84, 15]
st = list("tHi$_Is_CKrRect_aNsTer")
flag = list("flag{J3b_Is_aG0odTO01}")
s = ''
for i in range(22):
    s += chr(ord(st[i]) ^ li[i])
print(s)
```



## 题目名称:方程组?

出题人:**Nameless**

解线性方程组

直接上脚本

```python
import z3
v1 = z3.Int('v1')
v2 = z3.Int('v2')
v3 = z3.Int('v3')
v4 = z3.Int('v4')
v5 = z3.Int('v5')
v6 = z3.Int('v6')
v7 = z3.Int('v7')
v8 = z3.Int('v8')
v9 = z3.Int('v9')
v10 = z3.Int('v10')
v11 = z3.Int('v11')
v12 = z3.Int('v12')
v13 = z3.Int('v13')
v14 = z3.Int('v14')
v15 = z3.Int('v15')
v16 = z3.Int('v16')
v17 = z3.Int('v17')
v18 = z3.Int('v18')
v19 = z3.Int('v19')
v20 = z3.Int('v20')
v21 = z3.Int('v21')
v22 = z3.Int('v22')
v23 = z3.Int('v23')
v24 = z3.Int('v24')

a = [ 0x295b,0x1050,0x1c7a,0x11f,0xe4c,0x1827,0x31a4,0x11bb,0xb2b,0x116a,0x10a6,0xb81,0x1012,0x1514,0x10c4,0x1089,0x1d2,0x119b,0x7af,0x874,0x13e7,0x177b,0xbe9,0x2068]
s = z3.Solver()
s.add(a[0]== 32 * v1 - 6 * v2 + 44 * v3 - 56 * v4 + 77 * v5)
s.add(a[1]== 22 * v1 - 5 * v2 + 7 * v3 - 22 * v4 + 33 * v5)
s.add(a[2]== 41 * v1 - 3 * v2 + 21 * v3 - 39 * v4 + 44 * v5)
s.add(a[3]== 13 * v1 - 20 * v2 + 31 * v3 - 41 * v4 + 19 * v5)
s.add(a[4]== 22 * v1 - 19 * v2 + 29 * v3 - 2 * v4 + 7 * v5)
s.add(a[5]== 24 * v6 + 12 * v7 + 37 * v8 - 11 * v9 + 12 * v10)
s.add(a[6]== 79 * v6 + 76 * v7 + 23 * v8 - 15 * v9 + 20 * v10)
s.add(a[7]== 35*v6 + 30*v7 + 16 * v8 - 28*v9+ 21*v10)
s.add(a[8]== 19 * v6 + 29 * v7 + 11 * v8 - 23 * v9 + 17 * v10)
s.add(a[9]== 24 * v6 + 33 * v7 + 18 * v8 - 19 * v9 + 13 * v10)
s.add(a[10] ==32 * v11 - 6 * v12 + 44 * v13 - 56 * v14 + 77 * v15)
s.add(a[11] == 46 * v11 - 78 * v12 + 99 * v13 - 67 * v14 + 87 * v15)
s.add(a[12] == 75 * v11 - 7 * v12 + 18 * v13 - 66 * v14 + 54 * v15)
s.add(a[13] == 25 * v11 - 64 * v12 + 85 * v13 - 31 * v14 + 97 * v15)
s.add(a[14] == 31 * v11 - 56 * v12 + 35 * v13 - 13 * v14 + 99 * v15)
s.add(a[15] == 57 * v16 - 25 * v17 + 32 * v18 - 33 * v19 + 71 * v20 - 69 * v21 + 75 * v22)
s.add(a[16] == 67 * v16 - 7 * v17 + 33 * v18 - 35 * v19 + 31 * v20 - 97 * v21 + 99 * v22)
s.add(a[17] == 77 * v16 - 11 * v17 + 54 * v18 - 47 * v19 + 47 * v20 - 71 * v21 + 83 * v22)
s.add(a[18] == 27 * v16 - 12 * v17 + 34 * v18 - 39 * v19 + 97 * v20 - 99 * v21 + 93 * v22)
s.add(a[19] == 15 * v16 - 32 * v17 + 19 * v18 - 19 * v19 + 95 * v20 - 69 * v21 + 57 * v22)
s.add(a[20] == 33 * v16 - 61 * v17 + 72 * v18 - 13 * v19 + 93 * v20 - 79 * v21 + 87 * v22)
s.add(a[21] == 55 * v16 - 26 * v17 + 57 * v18 - 21 * v19 + 22 * v20 - 33 * v21 + 79 * v22)
s.add(a[22] == 24 * v23 + 5 * v24 )
s.add(a[23] == 71 * v23 + 9 * v24)
if s.check():
    print(s.model())
# [v13 = 95,
#  v5 = 123,
#  v16 = 95,
#  v24 = 125,
#  v12 = 101,
#  v14 = 116,
#  v21 = 114,
#  v2 = 108,
#  v10 = 48,
#  v18 = 51,
#  v20 = 101,
#  v3 = 97,
#  v15 = 48,
#  v6 = 87,
#  v23 = 101,
#  v19 = 118,
#  v7 = 51,
#  v4 = 103,
#  v8 = 108,
#  v22 = 51,
#  v17 = 82,
#  v11 = 109,
#  v1 = 102,
#  v9 = 99]
a = [102,108,97,103,123,87,51,108,99,48,109,101,95,116,48,95,82,51,118,101,114,51,101,125]
flag = ""
for i in a :
    flag += chr(i)
    print(flag)
```



## 题目名称:Crypto

出题人:**Nameless**

两种解法

打开IDA,会发现key = "CDUTCTF"  ，从函数名可以分析出采用的是rc4加密

解法1.已知密钥为CDUTCTF，直接用rc4进行计算即可

```c
#include<stdio.h>
#include<string.h>
typedef unsigned longULONG; 
void rc4_init(unsigned char*s, unsigned char*key, unsigned long Len)
{
    int i = 0, j = 0;
    char k[256] = { 0 };
    unsigned char tmp = 0;
    for (i = 0; i<256; i++)
    {
        s[i] = i;
        k[i] = key[i%Len];
    }
    for (i = 0; i<256; i++)
    {
        j = (j + s[i] + k[i]) % 256;
        tmp = s[i];
        s[i] = s[j];
        s[j] = tmp;
    }
}

void rc4_crypt(unsigned char*s, unsigned char*Data, unsigned long Len)
{
    int i = 0, j = 0, t = 0;
    unsigned long k = 0;
    unsigned char tmp;
    for (k = 0; k<Len; k++)
    {
        i = (i + 1) % 256;
        j = (j + s[i]) % 256;
        tmp = s[i];
        s[i] = s[j];
        s[j] = tmp;
        t = (s[i] + s[j]) % 256;
        Data[k] ^= s[t];
    }
}
 
int main()
{
    unsigned char s[256] = { 0 }, s2[256] = { 0 };//S-box
    char key[256] = { "CDUTCTF" };
    char pData[512] = {0x24,0x18,0x74,0x78,0x26,0x8e,0xed,0xd6,0xea,0x38,0x73,0xed,0xed,0xe4,0x85,0xb4,0x18,0x4e,0x0e,0x01};
    unsigned long len = strlen(pData);
    int i;
    rc4_init(s, (unsigned char*)key, strlen(key));
    for (i = 0; i<256; i++)
    {
        s2[i] = s[i];
    }
    rc4_crypt(s2, (unsigned char*)pData, len);
    printf("pData=%s", pData);
    return 0;
}
```



解法2.gdb调试

- 在rc4_crypt处下断点

<img src="./.asset/rc4_1.png"/>

- 向下单步调试（注意堆栈和寄存器的信息）
- 得到flag

![](./.asset/rc4_2.png)



## 题目名称：DinoTree

出题人:**DorinXL**

丢进f5，代码审计发现两个关键地方，查看qwq和qaq数组

![](./.asset/tree1.png)

![](./.asset/tree2.png)

可以看到两个字符串，那么进行反解，要注意看for循环的长度

```python
qwq = "noiFusL\\Wf;zmu"
qaq = "ihs@}oRY5}ernD   "
s =""
# print(len(qwq))
# print(len(qaq))
for i in range(5,len(qwq)):
    s += chr(ord(qwq[i])^8)
print(s)

f =""
for i in range(len(qwq)-5):
    f += chr(ord(qaq[i])^6)
print(f)

#qwq = {DT_n3re}
#qaq = onuF{iT_3
```

解出后，根据字符串的提示，以及题目的提示，知道这是树的中序和后序遍历

那么需要求出前序遍历

> 中序：noiFu{DT_n3re}
>
> 后序：onuF{iT_3}ernD
>
> 求解前序的过程是手写的，这里不再提出
>
> 前序：Dino{Fun_Tr3e}
>
> 即为flag

## 题目名称：PaperTiger!!!

出题人:**Nameless**

扔进IDA,发现找不到main函数，shift+f12查看字符串,找到关键字符串，双击跟进
![](./.asset/babyc_1.png)

![](./.asset/babyc_2.png)

鼠标放在这里，按ctrl+X查看交叉引用，即查看哪里用到了这个字符串

![](./.asset/babyc_3.png)

发现F5不好使了，提示sp指针有问题。根据参考资料，调整最后面的堆栈指针

![](./.asset/babyc_4.png)

![](./.asset/babyc_5.png)

此时便可F5成功，审计代码知，就是简单的xor而已

![](./.asset/babyc_6.png)

写脚本计算出来即可

```pyhton
key = [0x0, 0x1, 0x2, 0x3, 0x5, 0x5, 0x6, 0x7, 0x8, 0x9, 0xa, 0xb, 0xc, 0xd, 0xe, 0xf, 0x10, 0x11, 0x12, 0x13, 0x14, 0x15, 0x16, 0x17, 0x18, 0x19, 0x1a, 0x1b, 0x1c]
s2 = "fmcd~qn6=VKT|l~jbE#tqgIE)^roa"
flag =""
for i in range(len(key)):
    flag += chr(ord(s2[i])^key[i])

print(flag)
```

所以如题，真的是个纸老虎:)

## 题目名称：你不会真的要用CE吧

出题人：**Gyan**

IDA打开,根据提示，关闭ASLR(如图）

![](./.asset/ce_0.png)

然后打开od，搜索字符串

![](./.asset/ce_1.png)

去IDA，按G，随便选上面的一个地址跳转，即可来到主函数

找到关键代码部分

![](./.asset/ce_2.png)

查看这句话的地址

![](./.asset/ce_3.png)

在od中，按ctrl+g跳转到该地址，并按f2下断点,然后f9运行程序

![](./.asset/ce_4.png)

可以发现程序停在了断点处，修改旁边的标志位，使得跳转不成立

![](./.asset/ce_5.png)

然后再按一下f9，运行程序，点击确定，发现flag已经出来了

![](./.asset/ce_6.png)

## 题目名称：fork bomb bomb bomb！！！

### 着手静态

1. 载入IDA 来到主函数进行分析
2. F5查看伪代码
3. 找到回车前的输出字符串
4. 1. 熟悉C语言就能看出，这里向/tmp目录里面输出了一个文件（.virus）
   2. 补充linux里的/tmp目录特点：任何人都可以进行读写，有些linux发行版会在关机后清空/tmp目录。关于linux的使用请参考相关书籍如：鸟哥的linux私房菜
   3. 利用system()函数执行了/tmp里的文件，由于linux的mask,/tmp/.virus这个shell脚本是没有执行权限的，所以使用bash调用
   4. /tmp/.virus脚本的内容是一个很经典的fork炸弹，百度就能查到
5. 这个程序的恶意代码和关键代码的耦合性不高，所以咱们能有好几种方法绕过恶意代码，接下来我们尝试使用静态patch和动态调试绕过恶意代码拿到flag，静态逆向写逆向脚本这里不做讨论

```C
dwhile ( 2 )                                   // 这个无线循环的结束条件是下面的goto
  {
    system("nohup bash /tmp/.virus &");         // 执行注入的sfork炸弹
    for ( j = 0; j <= (signed int)rand(); ++j )
    {
      for ( k = 0; k <= (signed int)rand(); ++k )
      {
        v13 = (__int64 *)malloc(8LL);
        *v13 = (signed int)rand() % 93 + 33;
        v4 = *v13;
        printf((unsigned __int64)"%c");
      }
    }                                           // 这个for是分配大量的内存和输出随机字符
    if ( v13 )                                  // 这个if判断最后一次的分配空间是否成功
                                                // 成功则开始解密
    {
      v5 = (unsigned int)*(char *)(v12 + v11);  // 这里是正式解密代码
      switch ( *(char *)(v12 + v11) )           // switch的值在0-4
      {
        case 0:
          patch1(&v23, v4, v5);                 // 每次patch调用的形参位置都不一样
          goto LABEL_22;                        // 猜测每个case只执行一次。这样的话，就可以精简下代码
        case 1:
          patch5(&v21, v4, v5);					
          goto LABEL_22;                        // 这里的patch函数可以尝试进入函数体分析
                                                // 如果直接patch恶意代码就不做演示
        case 2:
          patch9(&v19, v4, v5);
          goto LABEL_22;
        case 3:
          patch13(&v17, v4, v5);
          goto LABEL_22;
        case 4:
          patch17(&v15, v4, v5);
LABEL_22:
          ++v11;                                // v11从0开始自加
          continue;
        default:                                // v11超过4之后就goto跳出了循环
          goto LABEL_23;
      }
    }
    break;
  }
LABEL_23:
  v6 = j_strlen_ifunc(&v23);
  v14 = malloc(5 * (v6 - 1));
  if ( !v14 )
  {
    printf((unsigned __int64)&unk_488424);
    puts("what a pity!\n");
    exit(0xFFFFFFFFLL);
  }
  j_strcat_ifunc(v14, &v23);                    // strcat函数，可以看出拼接的字符串正好是case里面的内容
  j_strcat_ifunc(v14, &v21);
  j_strcat_ifunc(v14, &v19);
  j_strcat_ifunc(v14, &v17);
  j_strcat_ifunc(v14, &v15);
  putchar('\n');
  putchar('\r');                                // 吃掉无意间输入的字符
  puts(v14);                                    // v14用strcat了5次
  result = getchar();
  if ( __readfsqword(0x28u) == v25 )
    result = 0;
  return result;	
```

### 静态patch

把73行到84行全部patch掉

选中87行头，tab键回到流程图，空格看汇编

记下地址：0x402681

再f5回到伪代码

选中73行的system位置，按tab回到流程图，再按空格回到汇编代码

为了保证平衡，我们在call system上面一条汇编指令lea处

点ida菜单栏的edit-->patchprogram-->assamble

键入jmp 0x402681,只改这一条，下面的不用管，canel取消

点ida菜单栏的edit-->patchprogram-->aplly

把patch好的程序拖进linux

加上执行权限`chmod +x ez_virus`

执行`./ez_virus`

### gdb动态调试

还是刚才看到的那个地址，

`gdb ez_virus`

因为跳了5次，所以我们可能需要更改5次eip（64位叫rip）

在gdb里面`b *0x4025c1`设断点

r运行

运行到这里的时候，我么需要控制程序流程跳过恶意代码

在ida里面找到

由于这里是jz，所以可能不一定会进入正常代码（这里对应的是if(v13)这个变量在恶意代码中是否分配成功）

所以我们尝试进入下一句汇编（就当他没有跳转，按照程序逻辑，cmp的结果为0（即v13分配成功）则跳转，我们需要cmp的结果不为0,所以，这句需要不执行），则按照汇编流程,下一句的地址是0x402681

设置eip 

`set $eip=0x402681`

这样就跳过了第一次恶意代码

按c继续程序，程序会再次断在刚才设置的断点

重复设置eip

`set $eip=0x402681`

再按c继续程序

重复5次，得到flag