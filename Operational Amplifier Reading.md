***
# 理想运放相关
## 一些注意点
1. 对**FET**运放，输入端电流在**1pA**以下，对双极高速运放有时在数十uA。
2. 小电流下，多数运放$Z_o$在零点几欧。
3. 许多跟随器中保留$R_F$(20k):保护反相端不受过压侵犯，对于电流反馈型运放，保留$R_F$是为了使电路稳定。$R_G$可以直接拿掉。
4. 差分放大器：用于剥离DC成分，或抑制共模噪声。但输入端的输入阻抗不容易匹配。
5. 复杂反馈网络：在反馈环路中加入低阻抗接地通路。标准的反向运放无法完成，因为其输入电阻取决于驱动电路，反馈电阻由增益指标规定。可以使用下面的T型网络实现：<br>
![T型网络插入反馈回路](https://github.com/Devil-Galois/ImageOfNoteBook/blob/master/scheme1.png)<br>
计算公式：$-\dfrac{V_{out}}{V_{in}}=\dfrac{R_2+R_3+\dfrac{R_2R_3}{R_4}}{R_1}$<br>
在某些需要特定增益和输入电阻以及对输出电阻**<mark style="background: #FF5582A6;">都</mark>**有要求的情况下可以使用这个电路。
6. 视频放大器：同轴电缆进行发送、接收。用来连接的电缆具有75欧的阻抗，为防止反射，输入和输出电路用75欧进行阻抗匹配。<br>
**反射**：产生失真和重影。
7. 当$R_F$和$R_G$选择在数百、几千欧时，对输出电路阻抗影响很小，可以方便得实现阻抗匹配。此外还要在下一级得输入端放置匹配电阻$R_T$实现匹配。（构成分压器）<br>
***
# 单电源运放设计技术
## 单电源和双电源差别
双电源中，接地点位自动以电源中心电压为地，输出也是。<br>
单电源中接地实际相当于接入双电源的VEE.<br>
这时必须增加偏置实现理想的电压摆幅<br>
信号源以非地电位为参考，会使偏置一起放大，减少动态范围，如果不是故意加的偏置，则需要使用输入偏置消除之。<br>
<mark style="background: #FFB86CA6;">用电压反馈型运放施加共模电压技术进行抑制放大。</mark><br>
另外还要注意输入级的反向击穿电压，在任何启动状态下避免输入端承担过多的反向偏压。<br>
## 四种单电源设计案例

### 范例一：$V_{out}=+mV_{in}+b$<br>
![范例一电路](https://github.com/Devil-Galois/ImageOfNoteBook/blob/master/Scheme2.png)<br>
$C_1,C_2$去耦电容：0.01u,降低高频噪声，提高电路抑制噪声能力。
当使用基准电压时，特别注意基准电压的稳压性和噪声成分，因为它们会被电路增益放大。<br>
$V_{out}=V_{in}\dfrac{R_2}{R_1+R_2}\dfrac{R_F+R_G}{R_G}+V_{ref}\dfrac{R_1}{R_1+R_2}\dfrac{R_F+R_G}{R_G}$<br>
一般选取VCC为基准电压$V_{ref}$
$m=\dfrac{R_2}{R_1+R_2}\dfrac{R_F+R_G}{R_{G}}\qquad b=V_{ref}\dfrac{R_1}{R_1+R_2}\dfrac{R_F+R_G}{R_G}$
电阻的绝对大小应考虑：**电源电流，频率响应，运放的驱动能力**。
输入回路电阻值很大，输入失调电流影响大；更高的频率响应，必须降低阻值；阻值减小会增加电源电流；阻值太小，使其他电路或运放难以驱动。
### 范例二：$V_{out}=+mV_{in}-b$<br>
![范例二电路](https://github.com/Devil-Galois/ImageOfNoteBook/blob/master/Scheme3.png)<br>
$R_3=R_{G}\qquad R_4=R_F$
$V_{out}=V_{in}\dfrac{R_F+R_G+R_1||R_2}{R_G+R_1||R_2}-V_{ref}\dfrac{R_2}{R_1+R_2}\dfrac{R_F}{R_G+R_1||R_2}$<br>
实际中假定$R_G>>R_1||R_2$ &#160 &#160 $R_1,R_2$阻值过小会使得5%误差特性变大。
### 范例三：$V_{out}=-mV_{in}+b$<br>
![范例三电路](https://github.com/Devil-Galois/ImageOfNoteBook/blob/master/Scheme4.png)<br>
$V_{out}=-V_{in}\dfrac{R_F}{R_F+R_G}+V_{ref}\dfrac{R_1}{R_1+R_2}\dfrac{R_F+R_G}{R_G}$<br>
当VCC关闭，$V_{in}$带来的负电压对反向输入端有危险，这种启动状态不安全，应该在反相输入端接入一个二极管到地，钳制电位大于$-V_D$（百mV级无害)。
更保险的办法是把$R_G$分裂成两个电阻，在二者之间接入二极管，这样其中一个电阻可以作为限流电阻。
而正常工作状态下，二极管开路，对电路无影响。
### 范例四：$V_{out}=-mV_{in}-b$<br>
![范例四电路](https://github.com/Devil-Galois/ImageOfNoteBook/blob/master/Scheme5.png)<br>
$V_{out}=-V_{in}\dfrac{R_F}{R_{G1}}-V_{ref}\dfrac{R_F}{R_{G2}}$<br>
同样使用接地二极管钳制反相输入端电压，进一步把$R_{G2}$分裂成两个电阻，二者之间接入0.01u的接地电容，抑制基准电压纹波。
## 设计步骤
- 带入输入、输出指标得到直线方程
- 根据方程形式确定范例
- 计算参数
- 搭建电路，验证性能
- 对电路进行非正常状态测试：**切断电源但仍加上信号，然后对输入的过压、欠压进行测试**
- 按需要加入保护元件
- 重新测试
## 其他的电路示例
### 零偏同相衰减器
分压器接入跟随器（不用负增益反馈是为了电路稳定性）
### 正偏同相衰减器
分压器一端接入$V_{ref}$作为第二个输入
### 负偏同相衰减器
在零偏基础上在反相输入端接入标准反向放大电路
### 零偏反相衰减器
![零偏反相衰减器](https://github.com/Devil-Galois/ImageOfNoteBook/blob/master/Scheme6.png)<br>
$R_{INA}$和$R_{INB}$的和不可以大于$R_F$,$R_{ATTEN}$提供了衰减自由度。
### 正偏反相衰减器
在上面电路基础上在同相输入端接入$V_{ref}$和分压器电路。
### 负偏反相衰减器
在零偏基础上，在反相输入端接入连接到$V_{ref}$的$R_G$（$V_{ref}$放大通路的增益 要大于1，否则不稳定）。
## 总结
以上都是接口电路，用于输入电压和数据转换器直接，通常还可以增加滤波功能。

***
# 反馈与稳定性理论
## 反馈理论的价值
- 运放开环增益随f增而下降，增益下降引起运放偏离理想运放，反馈理论用于研究闭环增益。
- 反馈理论适用于高频，帮助理解振铃和振荡。
## 反馈方程

![反馈框图](https://github.com/Devil-Galois/ImageOfNoteBook/blob/master/Scheme7.png)
$\dfrac{V_{out}}{V_{in}}=\dfrac{A}{1+A\beta}$
当环路增益$A\beta$非常大时，忽略1，$G=\dfrac{1}{\beta}$
环路增益接近-1时，$G\rightarrow\infty$，输出迅速增加，当输出接近电源电压时，有源器件出现非线性现象，使得环路增益不再是-1，这时有两种可能状态：
1. 电路稳定在电源电压附近，称为自锁
2. 电路反转，向反方向增加，称为振荡
系统误差方程：
$E=V_{in}-\beta V_{out}=\dfrac{V_in}{1+A\beta}$
## 波特图分析
### 如何绘制
- 增益-20dB/十倍频或者-6dB/倍频，转折点下降-3dB，从而绘制幅频波特图
- 相位近似曲线，绘制$-30^{\circ},-45^{\circ},-60^{\circ},-90^{\circ}$点即可大致作出
- 极点使增益转折下降-20dB/十倍频，零点使增益转折上升20dB/十倍频
### 传递函数波特图绘制示例
考虑一个带阻滤波器：<br>
$G=\dfrac{(1+\tau s)(1+\tau s)}{2(1+\dfrac{\tau s}{0.44})(1+\dfrac{\tau s}{4.56})}$<br>
各个零极点的曲线图：<br>
![零极点简化图](https://github.com/Devil-Galois/ImageOfNoteBook/blob/master/Scheme8.png)<br>
零极点组合图：<br>
![零极点组合图](https://github.com/Devil-Galois/ImageOfNoteBook/blob/master/bodefigure1.png)<br>
各个极点或零点处的相角连接形成相频特性。
### 波特图应用于稳定性分析
- 稳定性由环路增益所决定，当$A\beta=-1=|1|\angle -180^{\circ}$时，出现不稳定或者振荡，如果增益幅度超过1，一般会因非线性减小到1，所以振荡出现在增益超过1的情况。
#### 举例分析
考虑环路增益       $A\beta=\dfrac{K}{(1+\tau_{1}s)(1+\tau_{2}s)}$，  $K$是DC增益
对应波特图：<br>
![示例波特图](https://github.com/Devil-Galois/ImageOfNoteBook/blob/master/bodefigure2.png)<br>
两个转折点：$\omega=\frac{1}{\tau_1}\qquad\omega=\frac{1}{\tau_2}$
转折点处积累$-45^{\circ}$的相移和-20dB/十倍频的增益下降。

曲线穿过0dB时的斜率指出了电路的相移和产生振荡的可能性。
由此可知：
**一个单斜率曲线只能积累起$90^{\circ}$的相移，所以当传递函数以单斜率的速率穿越0dB时是不会引起振荡的**。

相位裕度$\phi_M$和增益裕度$G_M$用于描述稳定性。
相位裕度是0dB时实际相移和$180^{\circ}$之间的差值；增益裕度是相移$180^{\circ}$时，实际增益和0dB之间的差值。
相位裕度过小->很强的过冲和振铃。

- 如果把环路增益增加到$K+C$，就使得幅度曲线上移，相位裕度就会下降，进而可能引起振荡。

- 电路极点靠的很近，会引起相移积累更快，更易振荡。
- 稳定的振荡器在穿越$180^{\circ}$相移时要有一个突变的过程。
- 增加闭环增益会使得电路更加稳定（$\beta$减小，$A\beta$减小）。
## 二次方程的振铃和过冲
$1+A\beta=1+\dfrac{K}{(1+\tau_{1}s)(1+\tau_{2}s)}=0\rightarrow s^2+s\dfrac{\tau_1+\tau_2}{\tau_1\tau_2}+\dfrac{1+K}{\tau_1\tau_2}=0$
$s^{2}+2\xi\omega_Ns+\omega_N^2=0\qquad\omega_N=\sqrt{\dfrac{1+K}{\tau_1\tau_2}}\qquad\xi=\dfrac{\tau_1+\tau_2}{2\omega_N\tau_1\tau_2}$
$\phi_M-M=\arctan(2\xi)\qquad$两个极点必须间隔足够远
两个参数分别是固有频率和阻尼系数。
***
# 非理想运放
## 