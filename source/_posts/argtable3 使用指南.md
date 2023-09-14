title: argtable3 使用指南
date: 2021-08-06 11:19:36
categories: zczb
tags: []
---
### argtable3 使用指南

   今天做项目需要在c语言里面解析命令行，然后我看了一下getopt()发现在linux下才能很方便的使用(在Windows下需要进行相关的配置)，然后我记得之前看过有个[项目(QDUOJ的核心)](https://github.com/QingdaoU/Judger)的源码中使用了argtable3，觉得还挺方便的。



## 一、引入头文件和如何编译

#### 1.引入头文件

```c++
#include "argtable3.h"
```

头文件可以在官方的github上下载[Releases · argtable/argtable3 (github.com)](https://github.com/argtable/argtable3/releases/)

下载带有amalgamation的文件

![image-20210806184310801](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/202108061851911.png)

然后解压会有

![image-20210806184427171](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/image-20210806184427171.png)

将源文件和头文件拷贝到项目目录

![image-20210806184503508](https://wangxblog.oss-cn-hangzhou.aliyuncs.com/202108061848813.png)

### 2.编译

在Windows下面使用VC可以

```bash
cl.exe kernel.c argtable3.c -o kernel
```

在Linux下使用GCC

```bash
gcc kernel.c argtable3.c -o kernel
```

## 二、开始使用

#### 1. 设置需要的参数

argtable3设置了多种参数的类型，我在下面一一介绍一下。

##### (1). arg_hdr

```c++
typedef struct arg_hdr {
    char flag;             /* Modifier flags: ARG_TERMINATOR, ARG_HASVALUE. */
    const char* shortopts; /* String defining the short options */
    const char* longopts;  /* String defiing the long options */
    const char* datatype;  /* Description of the argument data type */
    const char* glossary;  /* Description of the option as shown by arg_print_glossary function */
    int mincount;          /* Minimum number of occurences of this option accepted */
    int maxcount;          /* Maximum number of occurences if this option accepted */
    void* parent;          /* Pointer to parent arg_xxx struct */
    arg_resetfn* resetfn;  /* Pointer to parent arg_xxx reset function */
    arg_scanfn* scanfn;    /* Pointer to parent arg_xxx scan function */
    arg_checkfn* checkfn;  /* Pointer to parent arg_xxx check function */
    arg_errorfn* errorfn;  /* Pointer to parent arg_xxx error function */
    void* priv;            /* Pointer to private header data for use by arg_xxx functions */
} arg_hdr_t;
```

最基本的一个类型，所有的其他类型都包含这个类型。我一一解释下里面的参数的含义

- shortopts: 参数的短名称，比如ls -a 中的a就是短名称。
- longopts: 参数中的长名称，比如ls --all 中的all就是长名称。
- datatype: 参数的数据类型有<int>, <double>, NULL,<file> ,(时间类型传入format字符串),(rex类型传入匹配串),<string>等
- glossary: 对于参数的描述
- mincount: 参数最少的个数(可以规定是否必须有参数比如设置为0为非必须，1...n为必须)
- maxcount: 参数最多的个数，采用Unix风格的，如kernel.exe -l 1 -l 2 -l 3
- 其他参数非必须

##### (2). arg_lit (文字)

```c++
typedef struct arg_lit {
    struct arg_hdr hdr; /* The mandatory argtable header struct */
    int count;          /* Number of matching command line args */
} arg_lit_t;
```

##### (3). arg_int(整数)

```c++
typedef struct arg_int {
    struct arg_hdr hdr; /* The mandatory argtable header struct */
    int count;          /* Number of matching command line args */
    int* ival;          /* Array of parsed argument values */
} arg_int_t;
```

其中ival指获得的参数的值得一个数组，count表示获得了多少个值。

##### (4). arg_dbl(双精度浮点数)

```c++
typedef struct arg_dbl {
    struct arg_hdr hdr; /* The mandatory argtable header struct */
    int count;          /* Number of matching command line args */
    double* dval;       /* Array of parsed argument values */
} arg_dbl_t;
```

```c++
// 模式匹配
typedef struct arg_rex {
    struct arg_hdr hdr; /* The mandatory argtable header struct */
    int count;          /* Number of matching command line args */
    const char** sval;  /* Array of parsed argument values */
} arg_rex_t;
// 文件
typedef struct arg_file {
    struct arg_hdr hdr;     /* The mandatory argtable header struct */
    int count;              /* Number of matching command line args*/
    const char** filename;  /* Array of parsed filenames  (eg: /home/foo.bar) */
    const char** basename;  /* Array of parsed basenames  (eg: foo.bar) */
    const char** extension; /* Array of parsed extensions (eg: .bar) */
} arg_file_t;
// 时间
typedef struct arg_date {
    struct arg_hdr hdr; /* The mandatory argtable header struct */
    const char* format; /* strptime format string used to parse the date */
    int count;          /* Number of matching command line args */
    struct tm* tmval;   /* Array of parsed time values */
} arg_date_t;
```



#### 2. 初始化参数(创建空间)

- 这里我直接在注释里面说明

```c++
#include "argtable3.h"
struct arg_int *gpu, *cpu, *opreator, *tn, *l, *r;
struct arg_dbl *fc;
struct arg_lit *verb, *help, *version;
struct arg_end *end;
#define VERSION 0x020101
void parse_args(int argc, char* argv[]) {
    // 初始化一个列表表示参数列表
	void *argtable[] = {
        // arg_litn初始化help，其中h为短参数名，help为长参数名,0为mincount，1为maxcount，后面字符串为提示，其中lit可以省略类型
        // litn可以用简便的比如arg_lit0("h", "help", "xxxxxxxx")表示下面的
        // lit1("h", "help", "xxxxxxx")可以表示mincount=1,maxcount=1
		help = arg_litn("h", "help", 0, 1, "Display Thie Help and Exit"),
		version = arg_litn("v", "version", 0, 1, "Display Version Info And Exit"),
        // 类似同上，只是加了一个类型表示
		cpu = arg_intn("c", "cpu_number","<int>", 0, 1, "The CPU Number, Default is 1"),
		gpu = arg_intn("g", "gpu_number","<int>", 0, 1, "The GPU number, default is 1"),
		opreator = arg_intn("o", "op", "<int>",0, 1, "The opreator of expression default is +(1): 1 is + and -1 is -"),
		tn = arg_intn("t", "threadnum", "<int>",0, 1, "Per CPU Run The Thread Number"),
		fc = arg_dbln("f", "factor", "<double>",0, 1, "The Factor of Expression, default is 1.0"),
		l = arg_intn("l", "left", "<int>", 0, 3, "The factor for left expression, default is 1,1,1, exp: -l 1 -l 1 -l 1"),
		r = arg_intn("r", "right", "<int>", 0, 3, "The factor for right expression, default is 1,1,1,exp -r 1 -r 1 -r 1"),
		end = arg_end(10),
	};
	int exitcode = 0;
	char name[] = "kernel.exe";
	// 解析参数
	int nerrors = arg_parse(argc, argv, argtable);
	// count > 0表示有参数
	if (help->count > 0)
	{
		printf("Usage: %s", name);
		arg_print_syntax(stdout, argtable, "\n\n");
		arg_print_glossary(stdout, argtable, " %-25s %s\n");
		goto exit;
	}
	
	 if (version->count > 0) {
        printf("Version: %d.%d.%d\n", (VERSION >> 16) & 0xff, (VERSION >> 8) & 0xff, VERSION & 0xff);
        goto exit;
    }

	if (nerrors > 0) {
        // 错误处理
        arg_print_errors(stdout, end, name);
        printf("Try '%s --help' for more information.\n", name);
        exitcode = 1;
        goto exit;
    }
	
	if (cpu->count > 0)
	{
		CUDA_CONF::CPUn = cpu->ival[0];
	}

	if (gpu->count > 0)
	{
		CUDA_CONF::GPUn = gpu->ival[0];
	}

	if (opreator->count > 0)
	{
		CALCULATE_CONF::op = opreator->ival[0];
	}

	if (tn->count > 0)
	{
		CUDA_CONF::threadnum = tn->ival[0];
	}

	if (fc->count > 0)
	{
		CALCULATE_CONF::factor = fc->dval[0];
	}

	if (l->count > 0)
	{
		for (int i = 0; i < l->count; i++)
		{
			CALCULATE_CONF::left[i] = l->ival[i];
		}
	}
	// 拥有多个参数处理
	if (r->count > 0)
	{
		for (int i = 0; i < r->count; i++)
		{
			CALCULATE_CONF::right[i] = r->ival[i];
		}
		
	}
	
	printf("CPUn is %d, GPUn is %d, op is %d, factor is %lf\n", CUDA_CONF::CPUn, CUDA_CONF::GPUn, CALCULATE_CONF::op, CALCULATE_CONF::factor);
	run();
	exit:
    // 释放参数列表
    arg_freetable(argtable, sizeof(argtable) / sizeof(argtable[0]));
	return;
}
```

