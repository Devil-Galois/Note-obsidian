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
