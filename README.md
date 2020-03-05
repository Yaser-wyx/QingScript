# QS

## 简介

QS全名QinScript，是一门面向过程的脚本语言，语法参考了JS与C，属于个人实验性质项目。

*关于名字由来：51696ee698afe68891e5a5b3e58f8be79a84e5a793efbc88e7a7a6efbc89efbc8ce59ba0e4b8bae698afe8849ae69cace8afade8a880efbc8ce68980e4bba5e58f88e59ca8e5908ee99da2e58aa0e4ba86e4b8aa536372697074efbc8ce8bf99e5b0b1e69e84e68890e4ba8651696e5363726970740a*

## 语言元素

### 模块

QS中一个文件就是一个模块。

  - 模块定义使用`@module: 模块名`来定义，模块定义必须在第一行。

  - 模块导入使用`import: 模块名`来导入。

  - 模块中对于要导出的函数量使用`export: 导出对象`来定义。

  - 模块中要是用其它模块中导出的元素，需要使用模块名前缀：`模块名::导出元素`

- 一个QS项目中必须要有一个main函数（有且只有一个），该函数作为程序入口。
  样例：
  
    ```
    @module: Main;//Main模块
    import: Math;//导入Math模块
    export: temp;//导出temp变量
  
    let temp;//模块变量
    fun main(){//入口函数
    	let a = 12;
    	let b = 33;
    	let c = Math::min(a,b);//调用其它模块函数
    }
    ```

### 变量

QS中的变量定义使用let关键字，变量分为三种：模块变量、局部变量与静态变量。

变量类型有：

- string

- number

- boolean

- null

- array

- complexus（复合体，静态函数的返回值，详情见静态函数）

#### 模块变量

在QS的变量中，只有模块变量是可以导出的，在全局可以访问。

#### 局部变量

局部变量在函数内定义，根据局部变量定义时所处的作用域，只可以在该作用域或子作用域中访问，且可以被子作用域中同名局部变量覆盖。

#### 静态变量

静态变量只能在静态函数中定义，使用`static`关键字定义，可以与局部变量同名，在静态函数或内部函数中访问时需要加`@`前缀，在静态函数外部访问需要使用**复合体**类型的变量来间接访问。

样例：

```
static fun test(){//静态函数定义
	static let staticVariable = 0;//静态变量
	@staticVariable = 12;//静态变量访问
}
fun main(){
	let temp = test();//静态函数调用
	temp.staticVariable = 99;//静态变量访问
}
```

### 函数

QS语言中函数是第一成员，函数有三种，一种是普通函数，一种是静态函数，还有一种是内部函数。

#### 普通函数

普通函数就是模块内函数，可以导出，拥有的功能与其他语言中的函数完全一致。

样例：

```
fun test(arg1,arg2){//普通函数定义
	//语句
}
```

#### 静态函数、内部函数*

静态函数属于模块内部，使用`static`关键自定义，内部函数属于静态函数内部，定义与普通函数一致，但需要加前缀`@`。

##### 详细说明

- 静态函数可以视为劣化版的类或是进阶版结构体，它介于二者之间，执行后会将内部的静态变量与内部函数一个整体返回，在这里称这个返回值为**复合体**。
- 静态函数有**静态变量**，可视为成员变量，且**只有静态函数才有静态变量**。
- 静态函数有**内部函数**，内部函数与普通函数完全一样，除了**不可以直接调用**，可以将它视为成员方法，且**只有静态函数有内部函数**。
- 静态函数与普通函数使用**完全一致**，除了**没有return语句**，可以单独调用，也可以赋值给一个变量。
- 静态函数单独调用与普通函数的单独调用，在**功能上完全一样**。
- 静态函数如果赋值给变量，则该变量称为**复合体变量**。
- 静态函数在调用之后，会执行除内部函数的其它语句，**可认为这是对静态函数的内部函数以及静态变量的初始化过程**。
- 静态变量与内部函数也**属于语句**，**可以包裹在控制语句中**，但**不可以使用循环语句包裹**，换句话说，**静态变量与内部函数可以在调用静态函数的时候动态生成**。
- 静态变量与内部函数在静态函数内**只可定义一次，多次定义会出错**。
- 在**静态函数外部访问静态变量与内部函数**时，**必须通过复合体变量来间接访问**，不可以直接访问，访问方式是`复合体变量.静态变量或内部函数`。
- 在静态函数中，静态变量**定义之后就可使用**，内部函数**只可以在内部函数中或是通过复合体变量调用**，**不可以在调用静态函数时调用**。
- 静态变量与函数的局部变量不冲突，内部函数与普通函数不冲突，**在静态函数内使用静态变量需要使用`@`做前缀**，**在内部函数中调用本静态函数的其它内部函数也需要使用`@`做前缀**。
- 内部函数**只可访问静态函数的静态变量或模块变量**，**不可以访问静态函数的局部变量**。

样例：

```
static fun staticFun( val ){//静态函数定义
	static let staticVariable = 0;//静态变量定义与初始化
	@staticVariable = 12;//静态变量访问
	@fun innerFun0(){
		return 0;
	}
	if(val == 0){//动态定义同一个内部函数的不同版本
		@fun innerFun(){
			@staticVariable = @innerFun0();//调用其它内部函数
		}
	}else{
		@fun innerFun(){//内部函数
            @staticVariable = 88;
		}
	}
}
fun main(){
	let temp = staticFun(1);//静态函数调用
	temp.staticVariable = 99;//静态变量访问与赋值
	//因为动态创建的时候val的值为1，所以是else中的innerFun被执行了。
	temp.innerFun();
	//print为原生函数，用于打印数据
	print(temp.staticVariable);//打印结果为88
}
```

### 语句

以下语句样例均不包含静态函数相关语句，静态函数的语句可以参考上文中的样例。

#### 定义语句

```
let a;//模块变量定义

fun fun2(){//普通函数定义
	let b = 99;//局部变量定义
}

```

#### if语句

```
if(表达式){
	语句;
}else{
	语句;
}
```

#### while语句

```
while(表达式){
	语句;
}
```

#### 函数调用语句

```
fun0(表达式列表);
```

#### return语句

```
return 表达式;
return;
```

#### 赋值语句

```
ID = 表达式;
ID[表达式] = 表达式
```

#### 表达式

```
ID;
1 * 12 + 43 - 4;
"11231254231";
[1, 2, 3];
```

## QS实现细节

​	QS使用模块化设计，一共有四个模块，词法分析器、语法分析器、解释器以及虚拟机。

### 词法分析器

词法分析器的源码均在[lexer文件夹](src/Lexer)下，主程序是[Lexer.ts](src/Lexer/Lexer.ts)，词法分析器是手工编码的方式进行实现的。

### 语法分析器 

语法分析源码在[parser文件夹下](src/Parser)，主程序是[ParseModule.ts](src/Parser/ParseModule.ts)，使用LR1语法分析方法，主要分为两个部分，一个部分是[解析器](src/Parser/AnalyzeGrammar/Index.ts)，用于解析[语法文件](grammar.txt)，并生成对应的LR1分析表，另一部分是[语法树生成器](src/Parser/BuildAST/Index.ts)，用于构建语法树，供解释器执行时使用。

### 解释器

解释器源码在[Interpreter文件夹](src/Interpreter)下，主程序是[Interpreter.ts](src/Interpreter/Interpreter.ts)。
### 虚拟机

#### TODO

## 运行方式

1. 安装[node.js](https://nodejs.org/en/)。
2. 安装[Yarn](https://classic.yarnpkg.com/en/)。
3. 使用命令行到项目根目录下。
4. 执行`yarn install`进行依赖安装。
5. 执行`yarn run dev`运行。




## 开发计划

### 当前开发状态：
**第一阶段已完成。**

- 当前阶段运行样例
```js
@module:Main;
let res=100;
fun main(){
    let max = res;
    res = 0;
    getSum(max);
    print(res);
}
fun getSum(max){
    while(max>0){
        res = res + max;
        max--;
    }
}
```
- 输出结果：

![Snipaste_2020-03-05_20-04-54](Snipaste_2020-03-05_20-04-54.png)


### 第一阶段

#### 任务

第一阶段的任务主要是：文法设计、词法分析、LR1分析表构建、语法树部分构建、解释器部分构建，部分语言功能的实现。

该阶段主要保证项目的整体可运行性，构建基础设施，带有测试目的。

#### 需要完成的功能

- 整体结构上，仅支持单模块，不支持多模块导入等功能。
- 数据类型上暂时支持：string、number、boolean与null
- 语句方面，暂时支持：普通函数定义、模块变量与局部变量定义、call语句、block语句、assign语句、while语句、return语句、可计算表达式（calExp）。
- 标准库方面，实现原生函数print()

#### 进度

- [x] [文法设计（初稿）](grammar.temp.txt)
- [x] 词法分析
- [x] LR1分析表构建
- [x] AST部分构建
- [x] 解释器部分构建
- [x] 语言功能的部分实现

### 第二阶段

- 计划实现AST的全部构建，解释器的全部构建，实现全部语言功能，包含一个QS标准库，发布正式版V1

### 第三阶段

- 设计并实现虚拟机，包括设计一套字节码，编写字节码执行器、GC、以及JIT。

## 参考资料

### 书籍：

- [编译器设计](https://book.douban.com/subject/20436488/)
- [自制编程语言 基于C语言](https://book.douban.com/subject/30311070/)

### 视频：

- [编译原理](https://www.icourse163.org/course/NUDT-1003101005)

### Git项目：

- https://github.com/Xiang1993/jack-compiler
- https://github.com/dejavudwh/C2j-Compiler
- https://github.com/Yaser-wyx/the-super-tiny-compiler-modified

### 博客：

- https://www.cnblogs.com/secoding/p/11393720.html