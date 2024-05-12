***
# Verilog 基本概念(跳过)
## module 模块
### synthesis 综合
- Level1：行为级描述
``` 
module muxtwo(out,a,b,sl);
	input a,b,sl;
	output out;
	reg out;
	always @ (sl,a,b)
	if(!sl) out=a;
	else    out=b;
endmodule
```
- Level2：逻辑表达式描述
```
module muxtwo(out,a,b,sl);
	input a,b,sl;
	output out;
	assign out=(a&(~sl))|(b&sl);
```
- Level3：内置原语描述(primitive)
```
module muxtwo(out,a,b,sl);
	input a,b,sl;
	output out;
	not    u1(nsl,sl);
	and #1 u2(sela,a,nsl);
	and #1 u3(selb,b,sl);
	or  #1 u4(out,sela,selb);
endmodule
```
综合(synthesis)就是Level1经Level2最后转为Level3的过程

***
# Module,DataType,Arguments,Symbolic(跳过)
## Module
module是设计的基本单元，由IO block和Function block构成
定义模块：
```
module thenameofmudule(io1,io2,...);
```
引用模块：
```
thenameofmodule userdefinedname(connect1,connect2,...);
```
这种引用必须和模块定义时端口一一对应
不需要对应的引用方式如下
```
thenameofmodule userdefinedname(.io1(connect1),...);
```
IO口说明格式：
```
input  [maxsize-1:0] portname;
...
output [maxsize-1:0] portname;
...
inout  [maxsize-1:0] potname;
```
合并形式的：
```
module thenameofmodule(input  [maxsize-1:0] INname,
					   output [maxsize-1:0] OUTname);
```
内部信号说明：
```
reg  [maxsize-1:0] var1,var2;
wire [maxsize-1:0] var3,var4;
```
Function block说明：
```
assign out=in;
always @ (var1,var2,...)begin
						...
						end
thenameofmodule userdefinedname(.io1(connect1),...);
```
注意：
initial,always块和assign语句、例化语句都是并行
表示一种变量名互相连接的关系
只有assign语句和例化语句可以独立于initial,always语句而作为Function block
## DataType
Fundamental data type 
`reg,wire,integer,parameter`
### Constant vars
1. Digital
```
<width><base><digital>
<base><digital>    //默认位宽，至少32位
<digital>          //默认十进制
```
`x 不定值  z or ? 高阻值`
`负数  -<width><base><digital>`
`use '_': 16b'1010_0101_0110_0000`
2. Parameter
```
parameter para1=value1,para2=valye2,...;
```
3. Example
- parameter trans
```
module Decoder(A,F)
	parameter Width=1,Polarity=1;
	...
endmodule

module top;
	wire [3:0] A4;
	wire [4:0] A5;
	wire [15:0] F16;
	wire [21:0] F32;
	Decoder #(4,0) D1(A4,F16);
	Decoder #(5)   D2(A5,F32);
	...
endmodule
```
- change parameter
use `defparam`
```
`include "Top.v"
`include "Block.v"
`include "Annotate.v"

module Test;
	wire W;
	Top T();
endmodule

module Top;
	wire W;
	Block B1();
	Block B2();
endmodule

module Block;
	parameter P=0;
endmodule

module Annotate;
	defparam
		Test.T.B1.P=2;
		Test.T.B2.P=3;
```
4. variables
- `wire,tri`
表示物理连接
	`wire` 单驱动
	`tri`   多驱动
输入输出默认是`wire`
```
wire [n-1:0] name1,name2,...;
```
- `reg`
register abstract eqauls D-flipflop
default value: x
```
reg [n-1:0] name1,name2,...;
```
当reg variable rega used as 操作数 in a expression,value of reg >0
- `memory`
存储器抽象
RAM,ROM,reg file
```
reg [n-1:0] thenameofmemory [m-1:0];
```
n:the length of each unit
m:the number of unit
## Operators and Expressions
- `+,-,*,/,%`
- `=,<=`
- `<,>,=,>=,<=`
- logical operators: `&&,||,!`
- `?:`
- bit operators:`~,|,^,&,^~`
- `<<,>>`
- `{}`
- others
tips:
two datas(different length)----bit operation----'Align right'----fill the high bit with 0
***
# Operators,Assignment,Structure
## Logical Operators
- priority: `&&，||<relational operators`, `!>arithmetic operators`
- `!>&&>||`
## Relational Operators
`if a<b  a<b=1;if a>=b a<b=0`
- priority: `relational operators<arithmetic operators`
## Equality Operators
- `==,! =,= = =,! = =`
- `==`不比较`x`,遇见`x`就认为是不等
- `= = =`比较`x,z`,必须同时为`x or z`
## Shift Operators
`a >> n`
fill vacancies with 0
```
4'b1001<<2=6'b100100;
```
## Bit Concatenation Operators
`{}`
`{the-bit-of-signal1,the-bit-of-signal2,...}`
```
{a,b[3:0],w,3'b001}
{4{w}}
{b,3{a,b}} //Nested
```
## Reduction Operators
将操作数从低位开始进行位运算，运算结果再与高位进行位运算
```
reg [3:0] b;
reg c;
c=&b;// c=((b[0]&b[1])&b[2])&b[3]
```
## Priority Table

|    Operators     |
|:----------------:|
|       !  ~       |
|     *  /  %      |
|       + -        |
|      <<  >>      |
|   <  <=  >  >=   |
| ==  !=  !==  === |
|        &         |
|     \^  \^~      |
|        \|        |
|        &&        |
|       \|\|       |
| ?:                 |
## Key Words
## Assignment,Block
1. Assignment
- `b< =a`
	- 块结束后才能完成这次赋值操作，所赋值时上次赋值得到的
- `b=a`
	- 在赋值语句执行完后立即改变值
2. Block
- 顺序块
	- 顺序执行，每条语句延迟时间是相对于前一条语句仿真时间
```
begin
...
end
```
```
begin :the name of the block
...
end
```
- 并行块
	- 语句同时执行
	- 块内语句时间是相对于块开始时间
	- 可用`disable`提前终止
```
fork
...
join
```
- 块名
	- 可以在块内定义和使用局部变量
	- 可以允许块被其他语句调用(比如把某个块disable掉)
	- 因为变量都是静态的，所以跳入、跳出块不影响值
块名提供了在任意时刻确认变量值的方法
***
# Conditional Statement,Loop Statement,Block Statement,Generate Statement

## `if_else`
```
if(expression0)
	statement1;
else if(expression1)
	statement2;
else if(expression2)
	statement3;
	...
else if(expressionk)
	statementk;
else
	statementk+1;
```
- 条件语句必须在`initial or always`块中使用
- `0,z,x`按false处理，`1`按true处理
- `if`嵌套
```
if(expression1)
	if(expression11) statement0;
	else             statement1;
else
...
```
- `else`总是与最近的`if`匹配
- `begin...end`可以用于确定`if`与`else`的匹配关系
## `case,casez`
1. `case(expression)  <branches>  endcase`
2. `casez(expression) <branches>  endcase`
3. `casex(expression) <branches>  endcase`
- Example
```
reg [15:0] rega;
reg [9:0]  result;
case(rega)
	16'd0:result=10'b0111111111;
	16'd1:result=10'b1011111111;
	16'd2:result=10'b1101111111;
	16'd3:result=10'b1110111111;
	16'd4:result=10'b1111011111;
	16'd5:result=10'b1111101111;
	16'd6:result=10'b1111110111;
	16'd7:result=10'b1111111011;
	16'd8:result=10'b1111111101;
	16'd9:result=10'b1111111110;
	default: result=10'bx;
endcase
```
- case分支项的值必须不同
- 执行某项后就直接跳出case
- `case,casez,casex`真值

| case |  0  |  1  |  x  |  z  |
|:----:|:---:|:---:|:---:|:---:|
|  0   |  1  |  0  |  0  |  0  |
|  1   |  0  |  1  |  0  |  0  |
|  x   |  0  |  0  |  1  |  0  |
|  z   |  0  |  0  |  0  |  1  |

| casez |  0  |  1  |  x  |  z  |
|:-----:|:---:|:---:|:---:|:---:|
|   0   |  1  |  0  |  0  |  1  |
|   1   |  0  |  1  |  0  |  1  |
|   x   |  0  |  0  |  1  |  1  |
|   z   |  1  |  1  |  1  |  1  |

| casex |  0  |  1  |  x  |  z  |
|:-----:|:---:|:---:|:---:|:---:|
|   0   |  1  |  0  |  1  |  1  |
|   1   |  0  |  1  |  1  |  1  |
|   x   |  1  |  1  |  1  |  1  |
|   z   |  1  |  1  |  1  |  1  |

- `casez`忽略高阻位的比较，`casex`忽略高阻和不定位的比较
## `if_else,case`注意点
```
always @(al or d)
	begin
		if(al) q=d;
	end
```
没有包含`al=0`的情况，`q`保持原值，生成一个锁存器
```
always @(al or d)
	 begin
		 if(al) q=d;
		 else   q=0;
	 end
```
这种不会综合出锁存器

```
always @(sel[1:0],a,b)
	case (sel[1:0])
		2'b00: q < =a;
		2'b11: q < =b;
	endcase
```
不包含`default`且未包含所有情况，会综合出锁存器
```
always @(sel[1:0],a,b)
	case (sel[1:0])
		2'b00: q < =a;
		2'b11: q < =b;
		default:q < ='b0;
	endcase
```
这种不会综合出锁存器
**在组合逻辑中把输出赋值给自己也会产生latch**

## `while`
## `for`
```
for(inite variables,conditions,step)
	begin
	...
	end
```
## `repeat`
执行固定次数，次数可以是常量、变量、信号；如果是变量，信号，则执行次数是执行开始时的值。
```
integer count;

initial 
begin
	count=0;
	repeat(128)
	begin
		$display("Count=%d",count);
		count=count+1;
	end
end

module data_buffer(data_start,data,clock);
parameter cycles=8;
input data_start;
input [15:0] data;
input clock;

reg [15:0] buffer [0:7];
integer i;

always @ (posedge clock)
begin
	if(data_start)
		begin
			i=0;
			repeat(cycles)
			begin
				@(posedge clock) buffer[i]=data;
				i=i+1;
			end 
		end
	end
endmodule
```
## `forever`
不断执行直到遇到`$finish`,可以使用`disable`语句进行终止
```
//例1
//使用forever实现时钟发生
reg clock;

initial
begin
	clock=1'b0;
	forever #10 clock=~clock;//时钟周期为20个时间单位
end
//例2
reg clock;
reg x,y;

initial forever
		@(posedge clock) x=y;//在时钟上升沿处使两个寄存器值一致
```
## 块语句的特点
- 嵌套块
顺序块和并行块混合使用
```
initial
begin
	x=1'b0;
	fork
		#5 y=1'b1;
		#10 z={x,y};
	join
	#20 w={y,x};
end
```
- 命名块
1. 命名块中可以声明局部变量
2. 命名块中的局部变量可以通过层次名进行访问
3. 命名块可以被禁用
```
module top

initial 
begin :block1 //命名为block1
integer i;  //i 可以通过top.block1.i进行访问
...
end

initial
fork :block2
integer i;  //i 可以通过top.block2.i进行访问
...
join
...
```

```
reg [15:0] flag;
integer i;
initial
	flag=16'b 0010_0000_0000_0000;
	i=0;
	begin :block1
	while(i<16)
		begin
			if(flag[i])
			begin
				$diaplay("%d",i);
				disable block1;
			end
			i=i+1;
		end
	end
end
```
## 生成块
```
generate
...
endgenerate
```
生成实例：
- 模块
- 自定义原语
- 门级原语
- 连续赋值语句
- initial，always块

可以包含的数据类型：
- `net,reg`
- `integer,real,time,realtime`
- `event`
1. 循环生成语句
```
module bitwise_xor(out,i0,i1);
parameter N=32;
output [N-1]out;
input  [N-1]i0,i1;

genvar j;  //用于生成块的循环计算

generate for(j=0;j<N;j=j+1)
	begin: xor_loop   //for内部的模块名
	xor g1(out[j],i0[j],i1[j]);
	end
endgenerate


//另一种编写形式
//reg [N-1,0]out;
//generate for(j=0;j<N;j=j+1)
//         begin:bit
//         always @(i0[j],i1[j])
//               out[j]=i0[j]^i1[j];
//         end
//endgenerate

endmodule
```
- 生成变量的值只能由循环生成语句改变
- 生成变量只能用于生成块中
- 上例中`xor_loop`是循环生成语句的名字，使用`xor_loop[0].g1,xor_loop[1].g1,...,xor_loop[31].g1`得到异或门的相对层次名
2. 条件生成语句
```
//如果数据位宽大于8就调用树形乘法器，否则调用超前进位乘法器
module multiplifier(product,a0,a1);
//参数声明（可修改）
parameter a0_width=8;
parameter a1_width=8;

//本地参数不能用defparam修改,也不能通过传递参数进行修改，如
//#(param1,param2,...)
localparam product_width=a0_width+a1_width;

output [product_width-1:0]product;
input  [a0_width-1:0]a0;
input  [a1_width-1:0]a1;

generate 
if(a0_width< =8||a1_width< =8)
	cla_multiplier #(a0_width,a1_width) m0(product,a0,a1);
else tree_multiplier #(a0_width,a1_width) m0(product,a0,a1);
endgenerate

endmodule
```
3. `case`生成语句
```
//实现N位加法器
module adder(co,sum,a0,a1,ci);
parameter N=4;

output co;
output [N-1:0]sum;
input  [N-1:0]a0,a1;
input  ci;

generate
	case(N)
	1: adder_1bit adder1(co,sum,a0,a1,ci);
	2: adder_2bit adder2(co,sum,a0,a1,ci);
	default: adder_cla #(N) adder3(co,sum,a0,a1,ci);
	endcase
endgenerate
endmodule
```
***
# Task and Function
## Differences

| Function                                   | Task                                  |
| ------------------------------------------ | -------------------------------------- |
| 函数可以调用另一个函数，不可以调用任务     | 任务既可以调用另一个任务也可以调用函数 |
| 函数在仿真0时刻开始执行                    | 任务可以在非零时刻执行                 |
| 函数内部不可以包含时序控制语句             | 任务可以包含                           |
| 函数至少一个输入变量                       | 任务可以有或者无输入、输出、双向变量   |
| 函数只能返回一个值，不可以有输出、双向变量 | 任务不返回值                           |
- 任务和函数的作用范围仅限于定义所在的模块
- 任务用于替换Verilog代码
- 函数用于替换组合逻辑
- 可以声明`reg,time,integer,event,real`，不可以声明`wire,net`
- 不可以包含`always,initial`块
## Task
```
task
...
endtask
```
以下情况下必须用task而不能用function
- 子程序含延迟、时序、事件控制
- 没有输出或者多输出
- 没有输入
### 任务声明和调用
```
//声明
task [automatic] task_identifier;
{task_item_declaration}
statement
endtask

//或者
task [automatic] task_identifier (task_port_list);
{block_item_declaration}
statement
endtask

//item声明
tf_input_declaration;
tf_output_declaration;
tf_inout_declaration;

task_port_type:reg real time realtime integer
```
模块的`input,output,inout`用于和外部连接，任务的则只是传入变量
```
module operation;
...
parameter delay=10;
reg [15:0] A,B;
reg [15:0] AB_AND,AB_OR,AB_XOR;

always @(A,B)
begin
	bitwise_oper(AB_AND,AB_OR,AB_XOR,A,B);
end


task bitwise_oper;
input [15:0]a,b;
output [15:0] ab_and,ab_or,ab_xor;
begin
	#delay ab_and=a&b;
	ab_or=a|b;
	ab_xor=a^b;
end
endtask
...
endmodule
```
### 自动可重入任务
防止一个任务在两处被同时调用时，二者对一块地址空间进行操作
```
module top;
reg [15:0] cd_xor,ef_xor;
reg [15:0] c,d,e,f;

task automatic bitwise_xor;
output [15:0] ab_xor;
input  [15:0] a,b;
begin 
	#delay ab_xor=a^b;
end
endtask

always @(posedge clk)
	bitwise_xor(ef_xor,e,f);
always @(posedge clk2)//clk2是clk频率的二倍
//且同步
	bitwise_xor(cd_xor,c,d);
endmodule
```
## Function
适用情况：
- 子程序不含延迟、时序或者控制结构
- 子程序只有一个返回值
- 至少一个输入变量
- 没有输出、双向变量
- 没有非阻塞赋值语句
### 函数声明和调用
```
function [automatic] [signed] [rangeortype]
function_indentifier;
function_item_declaration
function_statement;
endfunction

rangeortype:range|integer,real,realtime,time
```

```
//偶校验位的计算

```
