一些用纯C语法计算远比C++快
如 `scanf`，`printf`。原理不多赘述。
----
# 一、`scanf`
#### 1.功能简化的`cin`，速度当然比`cin`快。使用大概如下：
```c
scanf("格式控制字符串",变量地址列表);
```
记住这里是变量地址列表，所以传入变量得用`&`比如想要`cin>>a`，就是 `scanf("...",&a)` 。
 >注：你个艾斯比C++不好好学。啊 **如过我前面有的是`int* a`,`int &a`,怎么用呢？**
- 首先要知道这里`scanf`要求的是传入一个地址，如果你直接`int *a`,然后`scanf(...,a)`必定报错。因为这个指针还没存储地址。正确做法：
```CPP
int b;
int *a = &b;
scanf("%d",a); #你看这里就没有&
```
- 那`int &a`呢？这玩意只有C++有，其本身没啥意义。这玩意就是个别名，完全等价于&b。如下：
```CPP
int b;
int &a = b; //给b起了个别名，名字是a
//然后a就是b，地址，存储的东西都相同，修改a会改变b，同理修改b也一样，所以在scanf处还是要传入 &a 或 &b (等价)
```
#### 2.`"%d"`,`"格式控制字符串"`
- 指定能传入的格式

|格式符|对应数据类型|说明|
|---|---|---|
|`%d`|int|读取整数|
|`%lld`|long long|读取长整型（避免溢出）|
|`%f`|float|读取单精度浮点数|
|`%lf`|double|读取双精度浮点数（重点）|
|`%c`|char|读取单个字符|
|`%s`|char[]|读取字符串（到空格 / 换行截止）|
>`scanf("%c",...)`的时候注意前面是否有输入，`scanf`会读取==回车键==的。
```CPP
// 读取双精度浮点数（易错点：double必须用%lf） 
double b; 
scanf("%lf", &b);
printf("读取的浮点数：%.2lf\n", b); 
// 读取字符（注意：%c会读取空格/换行等空白符） 
char c; 
// 先吸收前面的换行符（否则会读取到上一次输入的回车）
getchar(); 
scanf("%c", &c); printf("读取的字符：%c\n", c);
```
- **如何传入多个变量？**
请严格按照输入的格式来写。
```CPP
#include <cstdio> 
int main() { 
int x, y; 
// 输入格式：10,20（逗号分隔），格式串要和输入的分隔符匹配 
scanf("%d,%d", &x, &y);
printf("x=%d, y=%d\n", x, y);
 // 输入格式：30 40（空格分隔），格式串用空格或直接写%d%d都可以 
scanf("%d %d", &x, &y);
printf("x=%d, y=%d\n", x, y); 
return 0; 
}
```

留空。。。。

----
# 二、`printf`
## 1. 长这样
```CPP
printf("格式控制字符串", 输出项列表);
```
这玩意不用`&`,直接传入变量。

|格式符|对应数据类型|说明|scanf 对应格式符|
|---|---|---|---|
|`%d`|int|输出整数|`%d`|
|`%lld`|long long|输出长整型|`%lld`|
|`%f`|float/double|输出浮点数（重点：double 也用 % f）|`%lf`（double）|
|`%c`|char|输出单个字符|`%c`|
|`%s`|char[]|输出字符串|`%s`|
|`%u`|unsigned int|输出无符号整数|`%u`|

## 2.输出多个变量
格式符是什么输出什么样子,结果就是什么样子。
```cpp
printf("%d%d",a,b);
```
结果
```
ab
```
---

```cpp
printf("%d,%d",a,b);
```
结果
```
a,b
```
---
```cpp
printf("%d ",a);//注意有空格
```
结果
```CPP
 a //a前面有空格，在循环中方便使用
```
……类推

## 3.特殊格式输出
### (1)有效数字位数控制
格式：`%m.ng`（n 为有效数字位数，m为宽度，m可省略，m是输出的长度，和位数完全无关）
#### 示例1: 正数（整数 / 小数 / 极小值）
```cpp
#include <cstdio>
int main() {
    double num1 = 123.456;   // 正数，小数
    double num2 = 12300;     // 正数，整数（末尾有0）
    double num3 = 0.0012345; // 正数，极小值
    int n = 3;               // 要输出的有效数字位数

    // 输出3位有效数字（%.3g）
    printf("num1（123.456）的3位有效数字：%.3g\n", num1); // 123（自动省略小数点）
    printf("num2（12300）的3位有效数字：%.3g\n", num2);   // 1.23e+04（科学计数法，保证3位）
    printf("num3（0.0012345）的3位有效数字：%.3g\n", num3); // 0.00123（自动保留有效位）

    return 0;
}
```
####  示例 2：负数（验证负号不占有效数字）
```CPP
#include <cstdio>
int main() {
    double num1 = -3.14159;  // 负小数
    double num2 = -1234.56;  // 负整数+小数
    double num3 = -0.009876; // 负极小值

    // 输出4位有效数字
    printf("num1的4位有效数字：%.4g\n", num1); // -3.142（负号保留，4位有效数字）
    printf("num2的4位有效数字：%.4g\n", num2); // -1235（四舍五入，无小数点）
    printf("num3的4位有效数字：%.4g\n", num3); // -0.009876（负号保留，4位有效）

    return 0;
}
```

### (2)宽度与小数控制
格式：`%m.nf`（m为总宽度，n 为要保留的小数位数），会自动**四舍五入**。其中`.`会占一位宽度。

- `%f`：默认保留 6 位小数；
- `%.2f`：保留 2 位小数；
- `%.0f`：保留 0 位小数（取整）。

#### 示例：


```CPP
#include <cstdio>
int main() {
    double pi = 3.141592653589793;
    
    printf("默认保留6位：%f\n", pi);       // 输出：3.141593（自动四舍五入）
    printf("保留2位小数：%.2f\n", pi);    // 输出：3.14
    printf("保留4位小数：%.4f\n", pi);    // 输出：3.1416
    printf("保留0位小数：%.0f\n", pi);    // 输出：3
    printf("保留8位小数：%.8f\n", pi);    // 输出：3.14159265

    // 结合宽度：总宽度10，保留2位小数（不足补空格）
    printf("总宽度10，保留2位：%10.2f\n", pi);  // 输出："      3.14"（前面6个空格）
    return 0;
}
```
#### (3)宽度与对齐控制

格式：`%[修饰符]n[类型符]`（n 为总宽度），用于控制输出的字符长度，常用修饰符：

- 无修饰符：右对齐（默认）；
- `-`：左对齐；
- `0`：不足宽度时补 0（而非空格）。

#### 示例：宽度与对齐
```CPP
#include <cstdio>
int main() {
    int num = 123;
    double f = 45.6;

    // 整数宽度控制
    printf("右对齐，宽度5：%5d\n", num);    // 输出："  123"（右对齐，前面2个空格）
    printf("左对齐，宽度5：%-5d\n", num);   // 输出："123  "（左对齐，后面2个空格）
    printf("补0，宽度5：%05d\n", num);     // 输出："00123"（前面补0）

    // 浮点数宽度+小数位数组合
    printf("宽度8，保留1位小数：%8.1f\n", f);  // 输出："   45.6"（右对齐）
    printf("左对齐，宽度8，保留1位：%-8.1f\n", f); // 输出："45.6   "
    return 0;
}
```
#### (4)：进制转换

表格

|格式符|功能|示例（num=255）|
|---|---|---|
|`%d`|十进制整数|255|
|`%o`|八进制整数（无前缀）|377|
|`%x`|十六进制小写（0x）|ff|
|`%X`|十六进制大写（0X）|FF|
|`%#x`|带前缀的十六进制小写|0xff|
|`%#o`|带前缀的八进制|0377|

#### 示例：进制转换
```CPP
#include <cstdio>
int main() {
    int num = 255;
    printf("十进制：%d\n", num);        // 255
    printf("八进制：%o\n", num);         // 377
    printf("带前缀八进制：%#o\n", num);  // 0377
    printf("十六进制小写：%x\n", num);   // ff
    printf("十六进制大写：%X\n", num);   // FF
    printf("带前缀十六进制：%#x\n", num);// 0xff
    return 0;
}
```
### （4）特殊格式输出  ~~不重要~~

#### 科学计数法输出（% e/% E）

```CPP
#include <cstdio>
int main() {
    double num = 12345.6789;
    printf("科学计数法小写：%e\n", num);  // 1.234568e+04（默认6位小数）
    printf("科学计数法大写：%E\n", num);  // 1.234568E+04
    printf("保留2位小数：%.2e\n", num);  // 1.23e+04
    return 0;
}
```
####  输出百分号（%%）

`%`是格式符的开头，要输出`%`本身，需写`%%`：

```CPP
#include <cstdio>
int main() {
    printf("通过率：%.2f%%\n", 98.5);  // 输出：通过率：98.50%
    return 0;
}
```

####  输出内存地址（% p）
```C
#include <cstdio>
int main() {
    int a = 10;
    printf("变量a的地址：%p\n", &a);  // 输出：0x7ffee1b5c8ac（不同环境地址不同）
    return 0;
}
```
#### 输出布尔值（C++）

C++ 的`bool`类型用`%d`输出（true=1，false=0），或用`std::boolalpha`（需结合 cout，仅作补充）：


```CPP
#include <cstdio>
#include <iostream>
using namespace std;

int main() {
    bool flag = true;
    printf("布尔值（数字）：%d\n", flag);  // 输出：1
    cout << boolalpha << "布尔值（文字）：" << flag << endl; // 输出：true
    return 0;
}
```

### 读取 / 输出带空格的字符串（补充 scanf 的短板）

`scanf("%s")`无法读取带空格的字符串，需用`fgets`（读入）+`printf`（输出）：

```CPP
#include <cstdio>
int main() {
    char buf[100];
    printf("请输入带空格的字符串：");
    fgets(buf, sizeof(buf), stdin);  // 读入整行（包括空格，截止到换行）
    printf("你输入的是：%s", buf);   // 注意：fgets会读取换行符，输出时会多一行
    return 0;
}
```

# 三、如何处理string类型
### 1. printf 输出 string 类型（需用 c_str () 转换）
```C
#include <cstdio>
#include <string>  // 必须包含string头文件
using namespace std;

int main() {
    string s = "Hello C++ string";
    // 错误写法：printf("%s\n", s);  // 编译报错，printf不认识string对象
    // 正确写法：用c_str()转成const char*
    printf("输出string：%s\n", s.c_str());
    return 0;
}
```

**输出**：`输出string：Hello C++ string`

### 2. scanf 读取 string 类型（无直接方法，需间接处理）

`scanf`无法直接写入`string`对象（因为`string`是动态内存，`scanf`需要固定的内存地址），有两种常用解决方案：

#### 方案 1：先读入字符数组，再赋值给 string（推荐）

```C
#include <cstdio>
#include <string>
using namespace std;

int main() {
    char buf[100];  // 定义足够大的字符数组
    printf("请输入字符串：");
    scanf("%s", buf);  // 读入到字符数组（注意：%s遇空格截止）
    
    string s = buf;  // 转换为string类型
    printf("读取的string：%s\n", s.c_str());
    return 0;
}
```

#### 方案 2：用 string 的 resize 预留空间（不推荐，易出错）
```C
#include <cstdio>
#include <string>
using namespace std;

int main() {
    string s;
    s.resize(100);  // 预留100个字符的空间
    scanf("%s", &s[0]);  // 取第一个字符的地址写入
    s.shrink_to_fit();   // 收缩到实际长度
    printf("读取的string：%s\n", s.c_str());
    return 0;
}
```

⚠️ 注意：`scanf("%s")`只能读取到空格 / 换行前的内容，若要读取带空格的字符串，需用`fgets`替代（后续补充）。

----
先写到这吧，也太长了太多了，不过希望以后查起来方便