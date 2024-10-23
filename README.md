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

bign add(const bign* a,const bign* b)
{
    bign t;
    int i;
    t = *a;
    if(e%2 == 1) return sub(a,b);
    if(a->n < b->n) return add(b,a);
    for(i=0; i< t.n;i++)
    {
        t.d[i] += b->d[i];
        if(t.d[i] > 9)
        {
            t.d[i] -=10;
            t.d[i+1]++;
        }
        if(t.d[t.n]) t.n++;
    }
    return t;
}

bign sub(const bign* a,const bign* b)
{
    bign t;
    int i;
    t = *a;
    if(e%2 == 0) return add(a,b);
    if(a->n < b->n || (a->n == b->n && strcmp(a->d, b->d) < 0))
    {
        SIGN++;
        return sub(b,a);

    }
    for(i=0; i< t.n;i++)
    {
        t.d[i] -= b->d[i];
        if(t.d[i] <0)
        {
            t.d[i] +=10;
            t.d[i+1]--;
        }
    }
    Remove(&t);
    return t;
}

bign mul(const bign* a,const bign* b)
{
    bign t;
    int i,j;

    memset(&t ,0 ,sizeof(bign));
    t.n = a->n + b->n;
    for(i=0; i< t.n; i++)
    {
        for(j=0;j< b->n; j++)
        {
            t.d[i+j] += a->d[i] * b->d[j];
            if(t.d[i+j] > 9)
            {
                t.d[i+j+1] += t.d[i+j] / 10;
                t.d[i+j] %= 10;
            }
        }
    }
    Remove(&t);
    return t;
}

int _small(const char a[],int n , const bign* b)
{
    const char *pa,*pb;

    if(n < b->n) return 1;
    if(n == b->n)
    {
        pa = a + n - 1;
        pb = b->d + b->n -1;
        while(pa >= a && *pa == *pb)
        {
            pa--;
            pb--;
        }
        if(pa >= a && *pa < *pb) return 1;
    }
    return 0;
}
//对除法的每一步进行减法操作
int _sub(char a[], int n, const bign* b)
{
    char* pa;
    const char* pb;
    if(_small(a,n,b)) return -1;
    pa = a;
    pb = b->d;
    if(pd)
    {
        *pa--;
        pd = 0;
    }
    while(pa < a+n)
    {
        if(*pa < *pb)
        {
            pa[0] += 10;
            pa[1]--;
        }
        *pa -= *pb;
        pa++;
        pb++;
    }
    if(*pa < 0)
    {
        *pa += 10;
        pd = 1;
    }
    while(n>0 && a[ n - 1] == 0) n--;
    return n;
}

int div_mod(bign* shang,bign* yu,const bign* a,const bign* b)
{
    int m;//表示当前商的位置
    int t,t0;//余数的位数
    if(b->n == 0) return 0;
    memset(shang, 0, sizeof(bign));
    *yu = *a;
    shang->n = a->n - b->n + 1;//含0
    m = shang->n -1;//最高位
    t0 = b->n;
    while(m >= 0)
    {
        while(t = _sub(yu->d + m, t0 ,b), t>=0)
        {
            t0 = t;
            shang->d[m]++;
        }
        m--;
        t0++;

    }
    yu->n = t0 - 1;
    Remove(yu);
    Remove(shang);
    return 1;
}

bign div(const bign* a,const bign* b)
{
    bign shang, yu;
    div_mod(&shang, &yu,a,b);
    return shang;
}

bign mod(const bign* a,const bign* b)
{
    bign shang, yu;
    div_mod(&shang, &yu,a,b);
    return yu;
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
   int i=0,k=0,j=1,l,duandian=0;
   int n,m;
   int countsub = 0,countadd = 0;
   bign a,b,c;
   char s0[85],s1[40],s2[40],sign[4]={[2]='%'};
   char buf[40];
   gets(s0);
   char *p = s0;
   //存储大数
   if(s0[0] == '-')
   {
       sign[0] = '-';
       e++;
       p++;
   }
   else
   {
       sign[0] = '+';
   }

   while(*p != '\0')
   {

        if(*p == '*')
        {
            sign[j] = '*';
            j++;
            duandian=1;
        }
        if(*p == '/')
        {
            sign[j] = '/';
            j++;
            duandian=1;
        }
        if(*p == '-')
        {
            countsub++;
            sign[j] = '-';
            e++;
            j++;
            duandian = 1;
        }
        if(*p == '+')
        {
            countadd++;//用于乘除时判断正负
            sign[j] = '+';
            j++;
            duandian = 1;
        }
        if(isdigit(*p) && duandian == 0)//isdigit如果其中的变量是数字，返回非零值，否则返回0
        {
            s1[i] = *p;
            i++;
        }
        if(isdigit(*p) && duandian == 1)
        {
            s2[k] = *p;
            k++;
        }
        p++;
   }
   s1[i] = '\0';
   s2[k] = '\0';

    //转化大数，倒序，方便后面运算
   a=from_str(s1);
   b=from_str(s2);
   //如果负号是奇数个，一定是减法；负号是偶数个，一定是加法
   switch(sign[1])
   {
       case '+': c = add(&a,&b);
                break;
       case '-': c = sub(&a,&b);
                break;
       case '*': c = mul(&a,&b);
                break;
       case '/': c = div(&a,&b);
                break;
   }
   to_str(buf, &c);
   printf("%s\n",buf);
   if(sign[1] == '*' || sign[1] == '/')
   {
       if((sign[0] == '+' && sign[2] == '-') || (sign[0] == '-' && sign[2] == '%'))
       {
            if(sign[0] == '-')
            {
                sign[0] = '+';
                printf("%c%s\n",sign[0],buf);
                return 0;
            }
            if(sign[0] == '+')
            {
                sign[0] = '-';
                printf("%c%s\n",sign[0],buf);
                return 0;
            }
       }
       else
       {
            sign[0] = '+';
            printf("%c%s\n",sign[0],buf);
            return 0;
       }
   }
   if(SIGN%2 == 1)
   {
       if(sign[0] == '-')
       {
           sign[0] = '+';
           printf("%c%s\n",sign[0],buf);
           return 0;
       }
       if(sign[0] == '+')
       {
           sign[0] = '-';
           printf("%c%s\n",sign[0],buf);
           return 0;
       }
   }
   if(SIGN%2 == 0)
   {
        printf("%c%s\n",sign[0],buf);
        return 0;
   }
}
```
