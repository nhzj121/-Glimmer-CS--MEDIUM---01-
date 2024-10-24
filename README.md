# 2024090908012-但家利-CS-04
## Part 1 理解大数运算
1. 因为标准的整数数据类型都有大小限制，即只能处理一定范围内的整数数据。而字符数组可以存储任意长度的数字字符串，因此可以用来表示任意大小的整数。
2. 数位对齐-->逐位相加/相减-->处理进位（%10）/借位（%10）-->前一位+1/-1-->（如果需要）添加位数/处理高位0
3. 加法和减法，正常处理。乘法和除法，采用同号为正，异号为负。
## Part 2 初步实现大数运算
### Step 1 尝试存储大数
```c
#include <stdio.h>
#include <string.h>
#define MAXN 40

//无效位必须是0
//数0 表示：n=0
typedef struct _bign{
    int n;//数字的有效长度
    char d[MAXN];
}bign;

//将多余的"0"去除。如“012345”变为“12345”
void Remove(bign* p)
{ //项数不为0         最高项为0      指向前一项
    while(p->n > 0 && p->d[p->n - 1] == 0) p->n--;
}
//字符串转换为bign结构体
bign from_str(const char* s)
{
    bign t;
    int i;
    memset(&t, 0,MAXN); //将t的内存初始化为0
    t.n = strlen(s);  //计算字符串s的长度，存储在bign类型的成员n中
    i = t.n - 1;    //将i设置为字符串长度-1，以便从最后一个字符开始处理
    //只要s指向的字符不是（null，'\0'）就继续循环
    //                将字符转换为对应整数值
    while(*s) t.d[i--]=*s++ - '0';
    Remove(&t);
    return t;
}

void to_str(char* buf,const bign* p)
{
    int i;
    if(p->n == 0)
    {
        strcpy(buf,"0");
        return;
    }
    i=p->n - 1;
    while(i >= 0) *buf++ = p->d[i--] + '0';
    *buf='\0';
}


int main()
{
   bign a;
   char s1[40];
   char buf[40];
   scanf("%[0-9]",s1);
   a=from_str(s1);
   printf("%s\n",s1);
   return 0;
}
```
![image](https://github.com/user-attachments/assets/8d773e85-f1f2-4213-84ac-7b8a61b7166d)
## Step 2 实现大数加法
```c
#include <stdio.h>
#include <string.h>
#define MAXN 40

//无效位必须是0
//数0 表示：n=0
typedef struct _bign{
    int n;//数字的有效长度
    char d[MAXN];
}bign;

//将多余的"0"去除。如“012345”变为“12345”
void Remove(bign* p)
{ //项数不为0         最高项为0      指向前一项
    while(p->n > 0 && p->d[p->n - 1] == 0) p->n--;
}
//字符串转换为bign结构体
bign from_str(const char* s)
{
    bign t;
    int i;
    memset(&t, 0,MAXN); //将t的内存初始化为0
    t.n = strlen(s);  //计算字符串s的长度，存储在bign类型的成员n中
    i = t.n - 1;    //将i设置为字符串长度-1，以便从最后一个字符开始处理
    //只要s指向的字符不是（null，'\0'）就继续循环
    //                将字符转换为对应整数值
    while(*s) t.d[i--]=*s++ - '0';
    Remove(&t);
    return t;
}

void to_str(char* buf,const bign* p)
{
    int i;
    if(p->n == 0)
    {
        strcpy(buf,"0");
        return;
    }
    i=p->n - 1;
    while(i >= 0) *buf++ = p->d[i--] + '0';
    *buf='\0';
}

bign add(const bign* a, const bign* b)
{
    bign t;
    int i;
    if(a->n < b->n) return add(b,a);

    t = *a;
    for(i=0;i<t.n;i++)
    {
        t.d[i] += b->d[i];
        if(t.d[i] > 9)
        {
            t.d[i] -= 10;
            t.d[i+1]++;

        }
    }
    if(t.d[t.n]) t.n++;
    return t;
}

int main()
{
   bign a,b,c;
   char s1[40],s2[40];
   char buf[40];
   scanf("%[0-9]\n",s1);
   scanf("%[0-9]",s2);
   a=from_str(s1);
   b=from_str(s2);
   c=add(&a,&b);
   to_str(buf, &c);
   printf("%s\n",buf);
   return 0;
}
```
![image](https://github.com/user-attachments/assets/07dcde14-642b-425c-9b5e-3ea64eba45e9)
## Step 3 实现大数加法（处理负数）
```c
#include <stdio.h>
#include <string.h>
#define MAXN 40

//无效位必须是0
//数0 表示：n=0
typedef struct _bign{
    int n;//数字的有效长度
    char d[MAXN];
}bign;

//将多余的"0"去除。如“012345”变为“12345”
void Remove(bign* p)
{ //项数不为0         最高项为0      指向前一项
    while(p->n > 0 && p->d[p->n - 1] == 0) p->n--;
}
//字符串转换为bign结构体
bign from_str(const char* s)
{
    bign t;
    int i;
    memset(&t, 0,MAXN); //将t的内存初始化为0
    t.n = strlen(s);  //计算字符串s的长度，存储在bign类型的成员n中
    i = t.n - 1;    //将i设置为字符串长度-1，以便从最后一个字符开始处理
    //只要s指向的字符不是（null，'\0'）就继续循环
    //                将字符转换为对应整数值
    while(*s) t.d[i--]=*s++ - '0';
    Remove(&t);
    return t;
}

void to_str(char* buf,const bign* p)
{
    int i;
    if(p->n == 0)
    {
        strcpy(buf,"0");
        return;
    }
    i=p->n - 1;
    while(i >= 0) *buf++ = p->d[i--] + '0';
    *buf='\0';
}

bign add(const bign* a, const bign* b)
{
    bign t;
    int i;
    if(a->n < b->n) return add(b,a);

    t = *a;
    for(i=0;i<t.n;i++)
    {
        t.d[i] += b->d[i];
        if(t.d[i] > 9)
        {
            t.d[i] -= 10;
            t.d[i+1]++;

        }
    }
    if(t.d[t.n]) t.n++;
    return t;
}

int main()
{
   bign a,b,c;
   int sign = 0;
   char s1[40],s2[40];
   char buf[40];
   scanf("%s\n",s1);
   scanf("%s",s2);
   if(s1[0] == '-' && s2[0] == '-')
   {
       sign++;
       s1[0] = '0';
       s2[0] = '0';
   }
   a=from_str(s1);
   b=from_str(s2);
   c=add(&a,&b);
   to_str(buf, &c);
   if(sign == 0)
   {
       printf("%s\n",buf);
   }
   else
   {
       printf("-%s\n",buf);
   }
   return 0;
}
```
![image](https://github.com/user-attachments/assets/1464d4d9-d560-43c3-ada2-441692551168)
## Part 3 从表达式中提取操作数和操作符
```c
#include <stdio.h>
#include <string.h>
#define MAXN 40

//无效位必须是0
//数0 表示：n=0
//采用优先判断数的正负，然后利用对应正数计算
typedef struct _bign{
    int n;//数字的有效长度
    char d[MAXN];
}bign;

//将多余的"0"去除。如“012345”变为“12345”
void Remove(bign* p)
{ //项数不为0         最高项为0      指向前一项
    while(p->n > 0 && p->d[p->n - 1] == 0) p->n--;
}
//字符串转换为bign结构体,即大数的内部表示
bign from_str(const char* s)
{
    bign t;
    int i;
    memset(&t, 0,MAXN); //将t的内存初始化为0
    t.n = strlen(s);  //计算字符串s的长度，存储在bign类型的成员n中
    i = t.n - 1;    //将i设置为字符串长度-1，以便从最后一个字符开始处理
    //只要s指向的字符不是（null，'\0'）就继续循环
    //                将字符转换为对应整数值
    while(*s) t.d[i--]=*s++ - '0';
    Remove(&t);
    return t;
}

void to_str(char* buf,const bign* p)
{
    int i;
    if(p->n == 0)
    {
        strcpy(buf,"0");
        return;
    }
    i=p->n - 1;
    while(i >= 0) *buf++ = p->d[i--] + '0';
    *buf='\0';
}

int main()
{
   int l;
   int n,m;
   int countsub = 0,countadd = 0;
   bign a,b,c;
   char s0[85],s1[40],s2[40],*op;
   char *token,*save_ptr;
   char *ps1;
   char buf[40];
   gets(s0);
   char *p = s0;
   while(*p != '\0')
   {
       if(*p != ' ')
       {
            if(*p == '-') countsub++;
            if(*p == '+') countadd++;
            p++;
       }
       else
       {
           op = p;
           op++;
           p +=3;
       }
   }
   *p='\0';
   //存储大数
   token = strtok_r(s0," ",&save_ptr);
   while(*token != '\0')
   {

       s1[l] = *token;
       token++;
       l++;
   }
   s1[l] = '\0';

   token = strtok_r(NULL," ",&save_ptr);
   op = token;

   l=0;
   token = strtok_r(NULL," ",&save_ptr);
   while(*token != '\0')
   {

       s2[l] = *token;
       token++;
       l++;
   }
   if(s2[0] != '(')
   {
        s2[l] = '\0';
   }
    //去除括号
   else
   {
       l=0;
       while(s2[l+1] != ')')
       {
           s2[l] = s2[l+1];
           l++;
       }
       s2[l] = '\0';
   }
    //转化大数，倒序，方便后面运算
   a=from_str(s1);
   b=from_str(s2);
   to_str(buf, &a);
   printf("操作数1：%s\n",buf);
   to_str(buf, &b);
   printf("操作数2：%s\n",buf);
   printf("操作符：%c",*op);

   return 0;
}
```
![image](https://github.com/user-attachments/assets/7f1e9f13-fb25-4207-a1d1-9ddc49f38cf2)
## Part 4 封装大数四则运算
```c
#include <stdio.h>
#include <string.h>
#include <ctype.h>
#define MAXN 40

//无效位必须是0
//数0 表示：n=0
//采用优先判断数的正负，然后利用对应正数计算
typedef struct _bign{
    int n;//数字的有效长度
    char d[MAXN];
}bign;

bign add(const bign* a,const bign* b);
bign sub(const bign* a,const bign* b);

int SIGN=0,e=0,pd=0;
//将多余的"0"去除。如“012345”变为“12345”
void Remove(bign* p)
{ //项数不为0         最高项为0      指向前一项
    while(p->n > 0 && p->d[p->n - 1] == 0) p->n--;
}
//字符串转换为bign结构体,即大数的内部表示
bign from_str(const char* s)
{
    bign t;
    int i;
    memset(&t, 0,MAXN); //将t的内存初始化为0
    t.n = strlen(s);  //计算字符串s的长度，存储在bign类型的成员n中
    i = t.n - 1;    //将i设置为字符串长度-1，以便从最后一个字符开始处理
    //只要s指向的字符不是（null，'\0'）就继续循环
    //                将字符转换为对应整数值
    while(*s) t.d[i--]=*s++ - '0';
    Remove(&t);
    return t;
}

// 大整数加法函数
bign add(const bign* a, const bign* b) {
    bign t; // 用于存储结果的临时变量
    int i;
    t = *a; // 将a的值复制到t
    // 如果bign的位数e是奇数，使用减法代替加法
    if (e % 2 == 1) return sub(a, b);
    // 如果a的位数小于b的位数，交换a和b以简化处理
    if (a->n < b->n) return add(b, a);
    // 逐位相加，并处理进位
    for (i = 0; i < t.n; i++) {
        t.d[i] += b->d[i];
        if (t.d[i] > 9) { // 如果某位的和大于9，进位
            t.d[i] -= 10;
            t.d[i + 1]++;
        }
    }
    // 如果最高位有进位，增加位数
    if (t.d[t.n]) t.n++;
    return t; // 返回结果
}

// 大整数减法函数
bign sub(const bign* a, const bign* b) {
    bign t; // 用于存储结果的临时变量
    int i;
    t = *a; // 将a的值复制到t
    // 如果bign的位数e是偶数，使用加法代替减法
    if (e % 2 == 0) return add(a, b);
    // 如果a的位数小于b的位数或者a小于b，交换a和b并改变符号
    if (a->n < b->n || (a->n == b->n && strcmp(a->d, b->d) < 0)) {
        SIGN++; // 改变符号
        return sub(b, a);
    }
    // 逐位相减，并处理借位
    for (i = 0; i < t.n; i++) {
        t.d[i] -= b->d[i];
        if (t.d[i] < 0) { // 如果某位不够减，借位
            t.d[i] += 10;
            t.d[i + 1]--;
        }
    }
    Remove(&t); // 移除前导零
    return t; // 返回结果
}

// 大整数乘法函数
bign mul(const bign* a, const bign* b) {
    bign t; // 用于存储结果的临时变量
    int i, j;
    memset(&t, 0, sizeof(bign)); // 初始化t为0
    t.n = a->n + b->n; // 结果的位数是a和b的位数之和
    // 逐位相乘，并处理进位
    for (i = 0; i < t.n; i++) {
        for (j = 0; j < b->n; j++) {
            t.d[i + j] += a->d[i] * b->d[j];
            if (t.d[i + j] > 9) { // 如果某位的乘积大于9，进位
                t.d[i + j + 1] += t.d[i + j] / 10;
                t.d[i + j] %= 10;
            }
        }
    }
    Remove(&t); // 移除前导零
    return t; // 返回结果
}

// 比较字符串a和大整数b的大小
int _small(const char a[], int n, const bign* b) {
    const char *pa, *pb;
    // 如果a的位数小于b的位数，返回1
    if (n < b->n) return 1;
    if (n == b->n) { // 如果a和b的位数相同，比较每一位
        pa = a + n - 1;
        pb = b->d + b->n - 1;
        while (pa >= a && *pa == *pb) {
            pa--;
            pb--;
        }
        if (pa >= a && *pa < *pb) return 1; // 如果a小于b，返回1
    }
    return 0; // 否则返回0
}

// 对除法的每一步进行减法操作
int _sub(char a[], int n, const bign* b) {
    char* pa;
    const char* pb;
    if (_small(a, n, b)) return -1; // 如果a小于b，返回-1
    pa = a;
    pb = b->d;
    if (pd) { // 如果有前一次的余数，处理它
        *pa--;
        pd = 0;
    }
    // 逐位相减，并处理借位
    while (pa < a + n) {
        if (*pa < *pb) {
            pa[0] += 10;
            pa[1]--;
        }
        *pa -= *pb;
        pa++;
        pb++;
    }
    if (*pa < 0) { // 如果最后一位不够减，借位
        *pa += 10;
        pd = 1;
    }
    // 移除尾随零
    while (n > 0 && a[n - 1] == 0) n--;
    return n; // 返回新的位数
}

// 大整数除法和取模函数
int div_mod(bign* shang, bign* yu, const bign* a, const bign* b) {
    int m; // 表示当前商的位置
    int t, t0; // 余数的位数
    if (b->n == 0) return 0; // 如果除数为0，返回0
    memset(shang, 0, sizeof(bign)); // 初始化商为0
    *yu = *a; // 将被除数复制到余数
    shang->n = a->n - b->n + 1; // 商的位数
    m = shang->n - 1; // 最高位
    t0 = b->n;
    // 进行除法运算
    // 从最高位开始，逐位计算商
    while (m >= 0) {
    // 尝试从余数中减去除数，如果能够减去，则计算下一次减法
    while ((t = _sub(yu->d + m, t0, b)) >= 0) {
        // _sub函数尝试从余数的当前部分减去除数b
        // 如果能够减去（即余数的当前部分大于等于除数），则返回新的余数长度
        // 并将商的当前位加1
        t0 = t; // 更新余数的长度为新的余数长度
        shang->d[m]++; // 将商的当前位加1
    }
    // 完成当前位的计算后，移动到下一位
    m--; // 移动到下一位
    t0++; // 增加余数的长度，因为下一次减法可能会多减一位
}
    yu->n = t0 - 1; // 更新余数的位数
    Remove(yu); // 移除余数的前导零
    Remove(shang); // 移除商的前导零
    return 1; // 返回成功
}

// 大整数除法函数
bign div(const bign* a, const bign* b) {
    bign shang, yu;
    div_mod(&shang, &yu, a, b); // 执行除法和取模
    return shang; // 返回商
}

// 大整数取模函数
bign mod(const bign* a, const bign* b) {
    bign shang, yu;
    div_mod(&shang, &yu, a, b); // 执行除法和取模
    return yu; // 返回余数
}

// 将大整数转换为字符串
void to_str(char* buf, const bign* p) {
    int i;
    if (p->n == 0) { // 如果大整数为0，直接返回"0"
        strcpy(buf, "0");
        return;
    }
    i = p->n - 1; // 从最高位开始
    // 将每一位数字转换为字符并存储到buf中
    while (i >= 0) *buf++ = p->d[i--] + '0';
    *buf = '\0'; // 字符串结束符
}

int main() {
    int i = 0, k = 0, j = 1, l, duandian = 0; // 用于索引和标记的变量
    int n, m; // 未使用的变量
    int countsub = 0, countadd = 0; // 减法和加法的计数器
    bign a, b, c; // 定义三个大数变量用于存储操作数和结果
    char s0[85], s1[40], s2[40], sign[4] = { [2] = '%' }; // 存储输入的字符串和符号
    char buf[40]; // 用于存储最终结果的字符串

    gets(s0); // 读取输入的表达式
    char *p = s0; // 指针用于遍历输入的字符串

    // 如果输入的第一个字符是负号，设置符号并增加负号计数
    if (s0[0] == '-') {
        sign[0] = '-';
        e++; // e是全局变量，表示负号的个数
        p++;
    } else {
        sign[0] = '+'; // 默认符号为正
    }

    // 遍历输入的字符串，解析数字和符号
    while (*p != '\0') {
        if (*p == '*') {
            sign[j] = '*'; // 设置乘法符号
            j++;
            duandian = 1; // 标记下一个字符为数字
        }
        if (*p == '/') {
            sign[j] = '/'; // 设置除法符号
            j++;
            duandian = 1; // 标记下一个字符为数字
        }
        if (*p == '-') {
            countsub++; // 增加减法计数
            sign[j] = '-';
            e++; // 增加负号计数
            j++;
            duandian = 1; // 标记下一个字符为数字
        }
        if (*p == '+') {
            countadd++; // 增加加法计数
            sign[j] = '+'; // 设置加法符号
            j++;
            duandian = 1; // 标记下一个字符为数字
        }
        if (isdigit(*p) && duandian == 0) { // 如果是数字且不是乘除法后的数字
            s1[i] = *p; // 存储第一个操作数
            i++;
        }
        if (isdigit(*p) && duandian == 1) { // 如果是数字且是乘除法后的数字
            s2[k] = *p; // 存储第二个操作数
            k++;
        }
        p++;
    }
    s1[i] = '\0'; // 字符串结束符
    s2[k] = '\0'; // 字符串结束符

    // 将字符串转换为大数
    a = from_str(s1);
    b = from_str(s2);

    // 根据操作符执行相应的运算
    switch (sign[1]) {
        case '+': c = add(&a, &b); break; // 加法
        case '-': c = sub(&a, &b); break; // 减法
        case '*': c = mul(&a, &b); break; // 乘法
        case '/': c = div(&a, &b); break; // 除法
    }

    // 将结果转换为字符串并输出
    to_str(buf, &c);
    printf("%s\n", buf);

    // 如果是乘法或除法，根据符号输出结果
    if (sign[1] == '*' || sign[1] == '/') {
        if (((sign[0] == '+' && sign[2] == '-') || (sign[0] == '-' && sign[2] == '%'))) {
            if (sign[0] == '-') {
                sign[0] = '+'; // 改变符号
                printf("%c%s\n", sign[0], buf);
                return 0;
            }
            if (sign[0] == '+') {
                sign[0] = '-'; // 改变符号
                printf("%c%s\n", sign[0], buf);
                return 0;
            }
        } else {
            sign[0] = '+'; // 默认符号为正
            printf("%c%s\n", sign[0], buf);
            return 0;
        }
    }

    // 根据SIGN的奇偶性输出结果
    if (SIGN % 2 == 1) {
        if (sign[0] == '-') {
            sign[0] = '+'; // 改变符号
            printf("%c%s\n", sign[0], buf);
            return 0;
        }
        if (sign[0] == '+') {
            sign[0] = '-'; // 改变符号
            printf("%c%s\n", sign[0], buf);
            return 0;
        }
    }
    if (SIGN % 2 == 0) {
        printf("%c%s\n", sign[0], buf); // 输出结果
        return 0;
    }
}
```
![屏幕截图 2024-10-23 233727](https://github.com/user-attachments/assets/3442992b-5a57-47dd-abcd-a70be727a293)
![屏幕截图 2024-10-23 233744](https://github.com/user-attachments/assets/423858f3-3055-4ca2-8dc3-d2d21486e883)
![屏幕截图 2024-10-23 233804](https://github.com/user-attachments/assets/935725f7-0a80-41e9-9bfe-6fc3a9ff4fc8)
![屏幕截图 2024-10-23 233823](https://github.com/user-attachments/assets/d060a81e-59fa-41bf-af8b-dc6c671769cb)



