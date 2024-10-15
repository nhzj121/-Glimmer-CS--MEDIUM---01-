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
## Part 3 从表达式中提取操作数和操作符
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
   int i;
   int n,m;
   int countsub = 0,countadd = 0;
   bign a,b,c;
   char s0[85],s1[40],s2[40],*op;
   char sign[]="+-";
   char buf[40];
   gets(s0);
   printf("skjfh\n");
   char *p = s0;
   printf("%c\n",*p);//ok
   while(*p != '\0')
   {
       if(*p == '-') countsub++;
       if(*p == '+') countadd++;
       printf("%c\n",*p);
       p++;
   }
   *p='\0';
   printf("%d%d\n",countadd,countsub);//ok

   if(countsub == 0 && countadd == 1) i=0;
   if(countsub == 1 && countadd == 0) i=1;
   if(countsub == 2 && countadd == 1) i=2;
   if(countsub == 3 ) i=3;
   if(countsub == 1 && countadd == 1) i=4;
   if(countsub == 2 && countadd == 0) i=5;

   printf("%d\n",i);//ok

   switch(i)
   {
       case 0: op = &sign[0];
               n=0;
               while('0'<= s0[n] <='9' && s0[n] != ' ')
               {
                   s1[n] = s0[n];
                   printf("%c",s1[n]);
                   n++;

               }
               printf("\n");
               n += 3;
               m=0;
               while(s0[n] != '\0')
               {
                   s2[m] =s0[n];
                   printf("%c",s2[m]);
                   m++;
                   n++;

               }

               break;
       case 1: op = &sign[1];
               n=0;
               while('0'<= s0[n] <='9' && s0[n] != ' ')
               {
                   s1[n] = s0[n];
                   n++;
               }
               n += 3;
               m=0;
               while(s0[n] != '\0')
               {
                   s2[m] =s0[n];
                   m++;
                   n++;
               }
               break;
       case 2: op = &sign[0];
               n=0;
               while(('0'<= s0[n] <='9' || s0[n] == '-') && s0[n] != ' ')
               {
                   s1[n] = s0[n];
                   n++;
               }
               n += 4;
               m=0;
               while(('0'<= s0[n] <='9' || s0[n] == '-') && s0[n] != '\0' && s0[n] != ')')
               {
                   s2[m] = s0[n];
                   m++;
                   n++;
               }
               break;
       case 3: op = &sign[1];
               n=1;
               s1[0] = s0[0];
               while('0'<= s0[n] <='9' && s0[n] != ' ')
               {
                   s1[n] = s0[n];
                   n++;
               }
               n += 4;
               s2[0] = s0[n];
               m=1;
               n++;
               while(('0'<= s0[n] <='9' || s0[n] == '-') && s0[n] != '\0' && s0[n] != ')')
               {
                   s2[m] = s0[n];
                   m++;
                   n++;
               }
               break;
       case 4: op = &sign[0];
               if(s0[0] == '-')
               {
                   n=1;
                   s1[0] = s0[0];
                   while(('0'<= s0[n] <='9' || s0[n] == '-') && s0[n] != ' ')
                   {
                       s1[n] = s0[n];
                       n++;
                   }
                   n += 3;
                   m=0;
                   while('0'<= s0[n] <='9' && s0[n] != '\0')
                   {
                       s2[m] = s0[n];
                       m++;
                       n++;
                   }
                   break;

               }
               else
               {
                   n=0;
                   while('0'<= s0[n] <='9' && s0[n] != ' ')
                   {
                       s1[n] = s0[n];
                       n++;
                   }
                   n += 4;
                   m=0;
                   while(('0'<= s0[n] <='9' || s0[n] == '-') && s0[n] != '\0' && s0[n] != ')')
                   {
                       s2[m] = s0[n];
                       m++;
                       n++;
                   }
                   break;
               }
       case 5: op = &sign[1];
               if(s0[0] == '-')
               {
                   n=0;
                   while(('0'<= s0[n] <='9' || s0[n] == '-') && s0[n] != ' ')
                   {
                       s1[n] = s0[n];
                       n++;
                   }
                   n += 3;
                   m=0;
                   while('0'<= s0[n] <='9' && s0[n] != '\0')
                   {
                       s2[m] = s0[n];
                       m++;
                       n++;
                   }
                   break;
               }
               else
               {
                   n=0;
                   while('0'<= s0[n] <='9' && s0[n] != ' ')
                   {
                       s1[n] = s0[n];
                       n++;
                   }
                   n += 4;
                   m=0;
                   while(('0'<= s0[n] <='9' || s0[n] == '-') && s0[n] != '\0' && s0[n] != ')')
                   {
                       s2[m] = s0[n];
                       m++;
                       n++;
                   }
                   break;
               }
   }
//ok
   s2[m+1] = '\0';
   if(s1[0] != '-' && s2[0] != '-')
   {
        a=from_str(s1);
        b=from_str(s2);
        to_str(buf, &a);
        printf("操作数1：%s\n",buf);
        to_str(buf, &b);
        printf("操作数2：%s\n",buf);
        printf("操作符：%c",*op);
        return 0;
   }

   if(s1[0] =='-' && s2[0] == '-')
   {
        s1[0] = '0';
        s2[0] = '0';
        a=from_str(s1);
        b=from_str(s2);
        to_str(buf, &a);
        printf("操作数1：-%s\n",buf);
        to_str(buf, &b);
        printf("操作数2：-%s\n",buf);
        printf("操作符：%c",*op);
        return 0;
   }

   if(s1[0] == '-' && s2[0] != '-')
   {
        s1[0] = '0';
        a=from_str(s1);
        b=from_str(s2);
        to_str(buf, &a);
        printf("操作数1：-%s\n",buf);
        to_str(buf, &b);
        printf("操作数2：%s\n",buf);
        printf("操作符：%c",*op);
        return 0;
   }

   if(s1[0] != '-' && s2[0] == '-')
   {
        s2[0] = '0';
        a=from_str(s1);
        b=from_str(s2);
        to_str(buf, &a);
        printf("操作数1：%s\n",buf);
        to_str(buf, &b);
        printf("操作数2：-%s\n",buf);
        printf("操作符：%c",*op);
        return 0;
   }

   return 0;
}
```
