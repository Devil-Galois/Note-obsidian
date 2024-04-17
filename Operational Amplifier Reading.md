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
<mark style="background: #BBFABBA6;">$\phi_M=\arctan\dfrac{2\xi}{\sqrt{\sqrt{4\xi^4+1}-2\xi^2}}</mark>\qquad$两个极点必须间隔足够远
两个参数分别是固有频率和阻尼系数。

下面求过冲：
求其单位阶跃响应为 $h(t)=1-\dfrac{e^{-xi\omega_{N}t}}{\sqrt{1-\xi^2}}\sin(\omega_N\sqrt{1-\xi^2}t+\arctan\dfrac{\sqrt{1-\xi^2}}{\xi})$
$h(t)=0\rightarrow t_p=\dfrac{\pi}{\sqrt{1-\xi^{2}\omega_{N}}}\rightarrow\text{过冲为}$<mark style="background: #BBFABBA6;">$OS=100\%\times\exp(\dfrac{-\xi\pi}{\sqrt{1-\xi^2}})$</mark>
从上式还可以知道相位裕度和百分比过冲的关系。


***
# 非理想运放方程
## AC误差
- CMRR 随工作频率升高而下降
- 输出阻抗
- 电源抑制比
- 峰到峰输出电压
- 差分增益
- 相位裕度
## 三种典型电路--电路的开环增益、运放开环增益、闭环增益
### 反向运放
设运放开环增益为a，电路的开环增益为A，则
$\dfrac{V_{out}}{V_{in}}=\dfrac{\frac{-aZ_F}{Z_G+Z_F}}{1+\frac{aZ_G}{Z_G+Z_F}}\longrightarrow A=\dfrac{-aZ_F}{Z_G+Z_F}$
### 同相运放
$\dfrac{V_{out}}{V_{in}}=\dfrac{a}{1+a\frac{Z_G}{Z_G+Z_F}}\longrightarrow A=a$
### 差分运放
$\dfrac{V_{out}}{V_+-V_-}=\dfrac{\frac{aZ_F}{Z_F+Z_G}}{1+\frac{aZ_G}{Z_F+Z_G}}\longrightarrow A\beta=\dfrac{aZ_G}{Z_G+Z_F}$
***
# 电压反馈运放的补偿
## 内部补偿
运放是一个多极点系统，在内部补偿之后，运放在很大的频率范围内看起来是单极点系统，但补偿大大降低闭环带宽。
补偿的一种：使用密勒效应，电容容值较小。<br>
![弥勒电容内部补偿](https://github.com/Devil-Galois/Image-NoteBook/blob/master/Scheme10.png)<br>
密勒效应把电容扩大了大约等于这一级增益的那么多倍。
## 外部补偿
- 增加电路稳定性
- 降低噪声
- 使幅频响应平坦
- 获取更大可能的带宽
### 主极点补偿
以电容作为输出负载的电路叫做主极点补偿。如果输出电阻和电容的极点与零频靠得很近，这个极点就处于支配地位。
容易计算同相放大电路的环路增益：
$A\beta=\dfrac{aZ_G}{Z_G+Z_F}\dfrac{1}{Z_OC_Ls+1}$
带入运放二阶模型：$a=\dfrac{K}{(s\tau_1+1)(s\tau_2+1)}$
$A\beta=\dfrac{K}{(s\tau_1+1)(s\tau_2+1)}\dfrac{Z_G}{Z_G+Z_F}\dfrac{1}{Z_OC_Ls+1}$
假定$\frac{1}{\tau_1}<\frac{1}{\tau_2}$ ,由$Z_O$和$C_L$引起的极点向低频区移动，接近$\frac{1}{\tau_1}$就会引起相移增加，从而使得相位裕度下降。
*实际电路中容性负载很常见，尤其是电缆*
把一个主极点$\omega_D$放置在合适的位置使得增益提前下降，在$\frac{1}{\tau_1}$时，穿越0dB点，这时仅产生$45^{\circ}$的相移。
补偿前后的环路增益波特图：<br>
![主极点补偿曲线](https://github.com/Devil-Galois/Image-NoteBook/blob/master/bodefigure3.png)<br>
### 增益补偿
可以增加改变闭环增益使电路变得稳定。
*这种补偿不适应于CFA，因为CFA的环路增益与理想闭环增益之间不存在数学关系*
闭环增益从1改成9的波特图变化：提前过0dB点，相移减小。<br>
![增益补偿](https://github.com/Devil-Galois/Image-NoteBook/blob/master/bodefigure4.png)<br>
增益补偿适用于反相和同相电路，因为环路增益方程中包含了闭环增益，闭环增益增加会降低带宽。
制造商会给出最低稳定增益。
### 超前补偿
- 有时候运放的封装和连线引起分布电容迫使超前补偿。
- 有时候我们自己设置超前补偿。
超前补偿电路：<br>
![超前补偿](https://github.com/Devil-Galois/Image-NoteBook/blob/master/Scheme11.png)<br>
与$R_F$并联的电容往往是由于走线与地平面之间的分布电容组成。
*反馈回路下方不宜普通就是这个原因吧，大概........*
*高频电路中通常使用很长的走线降低这个电容*
但实际中可以增加这个电容稳定运放、减小噪声。
环路增益：
$A\beta=\dfrac{R_G}{R_G+R_F}\dfrac{R_FCs+1}{R_F||R_GCs+1}\dfrac{K}{(s\tau_1+1)(s\tau_2+1)}$
补偿电容引入了一个极点和一个零点，零点出现在极点前。我们不太关心极点，而是要恰当放置零点在$\frac{1}{\tau_2}$附近从而抵消这个极点产生的相移。
超前补偿带来的波特图的变化：<br>
![超前补偿波特图](https://github.com/Devil-Galois/Image-NoteBook/blob/master/bodefigure5.png)<br>
超前补偿给闭环增益带来一个$\dfrac{1}{R_FCs+1}$的因子，降低了带宽，降低的带宽数值取决于$\dfrac{1}{R_FC}$，这对于同相和反相电路都是一样的。
但是同相电路还带来了一个$\dfrac{1}{R_F||R_GC}$的极点。
### 使用补偿衰减器
- 运放输入端的杂散电容会降低稳定性并引起频率响应的峰值。
如反相电路中，反相输入端有一个连接到地的杂散电容$C_G$(pF数量级)，引入极点$\dfrac{1}{R_F||R_GC_G}$，大概在几百千，低于许多运放的$\frac{1}{\tau_2}$。
同样引入超前补偿电容$C_F$抵消极点。

如何运用$C_F$:
电容可以使用PCB上的一条很宽的举行铜片制作，铜片一端和输出连接，然后铜片置于$R_F$下面，紧贴地平面之上，切勿连接另一端。逐步切去铜片对电路进行调节，知道所有峰值均去除，然后测出尺寸，再在PCB上铺设一条完全一样的走线。
当$R_GC_G=R_FC_F$时，不会影响反相增益。
### 超前滞后补偿
- 使电路稳定，不会牺牲闭环增益
- 经常用于未补偿运放，较好高频性能
超前滞后补偿电路图：<br>
![超前滞后补偿](https://github.com/Devil-Galois/Image-NoteBook/blob/master/Scheme12.png)<br>
环路增益$A\beta$引入新的因子$\dfrac{RCs+1}{\dfrac{RR_G+RR_F+R_GR_F}{R_G+R_F}Cs+1}$，新引入的极点在零点之前。极点先带来相移，零点补偿相移，但极点的引入使得增益下降，所以环路增益更早地到达0dB点，增加电路稳定性。

对于闭环增益，引入因子$\dfrac{R+\frac{1}{sC}}{R+R_G+\frac{1}{sC}}$，带来的极点先出现，导致增益先上升，再在极点处下降。事实上，由于环路增益足够大，以及$R$连接到虚地，对闭环增益几乎不会有影响。

## 各种补偿方法比较
- 容性负载（电缆）经常会引起内部补偿运放振铃
- IC设计中经常使用主极点补偿，因为容易实现，很少用于外部补偿，很大的负载电容会起到稳定作用
- 最简单的补偿形式是增益补偿，提高闭环增益可以放开去做
- 超前补偿使用杂散电容更方便，但会降低闭环增益
- 反相输入端的杂散电容接到地，与接地电阻并联，降低稳定性，经常出现在CMOS运放构成的高阻抗电路中，可以增加一个反馈电容（利用输出端引出的导线和地平面来构建）进行补偿
- 超前滞后补偿既可以稳定也可以获得好的闭环增益
***
# 电流反馈运放
- CFA不存在GBW的限制
- 摆速很高
- 高频场合>100M
- 应用于动态范围要求不严的DC耦合视频
- 更快的上升时间和更低的交调失真（不同频率成分互相调谐）
## CFA模型
同相输入端与内部输出缓冲器相连，具有很大的输入阻抗；反相端输入电阻等于缓冲器输出阻抗，一般较小 **(<50)**。
输出缓冲器增益接近于1，提供很低的输出阻抗，基本可以忽略。只有在驱动重负载或者容性负载才会考虑。输入缓冲器的输出阻抗不可忽略，影响高频性能。
跨阻在M欧数量级。
示意图:<br>
![CFA](https://github.com/Devil-Galois/Image-NoteBook/blob/master/Scheme13.png)<br>
## 环路增益
运放同相端接地，反相端构成标准反馈回路。
稳定性计算电路：<br>
![等效图](https://github.com/Devil-Galois/Image-NoteBook/blob/master/Scheme14.png)<br>
$Z_B$是输入缓冲级的输出电阻。这是断开输出点与反馈环路后在$Z_F$上加$V_t$的等效图，我们要计算$V_{out}$。
最终$A\beta=\dfrac{Z}{Z_F(1+\frac{Z_B}{Z_F||Z_G})}$
### 同相CFA
闭环增益方程：
$\dfrac{V_{out}}{V_{in}}=\dfrac{A\beta(1+\frac{R_F}{R_G})}{1+A\beta}$
$Z_B\rightarrow 0$




