***
# Verilog 基本概念
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
# Module,DataType,Arguments,Symbolic
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
### constant vars
