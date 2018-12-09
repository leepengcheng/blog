---
title: 机器学习 PR与ROC
categories: ml 
tags:
  - PR/ROC
date: 2017-01-02 15:59:19
---

#### TP/FP/TN/FN
>T/N/P/N:True/False/Positive/Negative,记忆方法,例如TN,T*N=负样本，后面的N代表预测值为负，
故为 副样本>预测为负

* TP:正样本>预测为正
* FP:负样本>预测为正
* TN:负样本>预测为负
* FN:正样本>预测为负



#### PR曲线：
![PR](../../assets/PR.png  "PR曲线")

PR曲线(Precision-Recall),X轴：召回率(查全率)) Y轴：准确率(差准率) 
Precision:准确率，TP/(TP+FP),表示所有预测为正的结果中样本值也为正的比例，主体是预测值(宁缺勿滥)  
Recall:召回率,TP/(TP+FN)，所有正样本被预测为正的比例，主体是样本值(宁可错杀一千，不可放过一个)  

如果一个学习器的P-R曲线被另一个学习器的P-R曲线完全包住，则可断言后者的性能优于前者，例如上面的A和B优于学习器C，但是A和B的性能无法直接判断，   
但我们往往仍希望把学习器A和学习器B进行一个比较，我们可以根据曲线下方的面积大小来进行比较，但更常用的是平衡点或者是F1值。   
平衡点（BEP）是查准率=查全率时的取值，如果这个值较大，则说明学习器的性能较好。而F1 = 2 * P * R ／( P + R )，同样，F1值越大，   
我们可以认为该学习器的性能较好   


#### ROC曲线:
![ROC](../../assets/ROC.png  "ROC曲线")

ROC(Receuver Characterristic Curvve),受试者工作特征曲线，又称感受性曲线,  
优点：当测试集中的正负样本的分布变化的时候（例如负样本远多余正样本）,能够保持不变。   
ROC：X轴：FPR  Y轴：TPR，  
AUC(Area Under Curve)表示ROC曲线的面积,小于1


#### 反响传播公式推导

>$\begin{array} { l l } { \delta ^ { L } = \nabla _ { a } C \odot \sigma ^ { \prime } \left( z ^ { L } \right) } & { ( B P 1 ) } \\ { \delta ^ { l } = \left( \left( W ^ { l + 1 } \right) ^ { T } \delta ^ { l + 1 } \right) \odot \sigma ^ { \prime } \left( z ^ { l } \right) } & { ( B P 2 ) } \\ { \frac { \partial C } { \partial b _ { j } ^ { l } } = \delta _ { j } ^ { l } } & { ( B P 3 ) } \\ { \frac { \partial C } { \partial w _ { j k } ^ { l } } = a _ { k } ^ { l - 1 } \delta _ { j } ^ { l } } & { ( B P 4 ) } \end{array}$


https://zhuanlan.zhihu.com/p/37916911

>$\delta ^ { L } = \nabla _ { a } C \odot \sigma ^ { \prime } \left( z ^ { L } \right)$

如果：$Y = \sigma ( X )$，那么
$\frac { \partial C } { \partial X } = \frac { \partial C } { \partial Y } \odot \sigma ^ { \prime } ( X )$，其中
$\sigma(x)是激活函数$
----


>$\delta ^ { l } = \left( \left( W ^ { l + 1 } \right) ^ { T } \delta ^ { l + 1 } \right) \odot \sigma ^ { \prime } \left( z ^ { l } \right)$


>$\frac { \partial C } { \partial b _ { j } ^ { l } } = \delta _ { j } ^ { l }$


>$\frac { \partial C } { \partial w _ { j k } ^ { l } } = a _ { k } ^ { l - 1 } \delta _ { j } ^ { l }$



神经网络就是把 Y=W\cdot X_l+B，X_{l+1}=f(Y) 反复套用的过程。其中 f 为激活函数。

在最后，损失函数 Lost=C(X_L) ， l 是其中某一层， L 神经网络层数（最后一层），即 l 的最大值。

### 总结
神经网络就是把 $Y=W\cdot X_l+B，X_{l+1}=f(Y)$ 反复套用的过程。其中 `f `为激活函数。

在最后，损失函数 $Loss=C(X_L)$ ，`l` 是其中某一层， L 神经网络层数（最后一层），即 `l` 的最大值。
反向传播：
* (1)首先计算 $\frac{\partial C}{\partial X_L}$
* (2)计算 $\frac{\partial C}{\partial Y}$
* (3)计算$\frac{\partial C}{\partial W}，\frac{\partial C}{\partial X_l}，\frac{\partial C}{\partial B}$ 

(2)--(3)--(2)-(3)--.反复循环，直到输入层

(1)计算 $\frac{\partial C}{\partial X_L}$


##### Sarsa和Qlearning的对比

* Sarsa

>$Q ( S , A ) \leftarrow Q ( S , A ) + \alpha \left[ R + \gamma Q \left( S ^ { \prime } , A ^ { \prime } \right) - Q ( S , A ) \right]$

处于状态 s 时，根据当前 Q网络以及一定的策略来选取动作 a，进而观测到下一步状态 s'，并再次根据当前 Q 网络及相同的策略选择动作 a'，这样就有了一个[ s，a，r，s'，a']序列。
处于状态 s' 时，就知道了要采取哪个 a'，并真的采取了这个动作。动作 a 的选取遵循 e-greedy 策略，目标 Q 值的计算也是根据策略得到的动作 a' 计算得来。


* Qlearning

>$Q ( S , A ) \leftarrow Q ( S , A ) + \alpha \left[ R + \gamma \max _ { a } Q \left( S ^ { \prime } , a \right) - Q ( S , A ) \right]$

处于状态 s 时，根据当前 Q 网络以及一定的策略来选取动作 a ，进而观测到下一状态 s' ，并再次根据当前 Q 网络计算出 下一步采取哪个动作会得到 max Q  值，用这个 Q 值作为当前状态动作对 Q 值的目标。这样就有了一个[s，a，r，s']序列。
处于状态 s' 时，仅计算了 在 s' 时要采取哪个 a' 可以得到更大的 Q 值，并没有真的采取这个动作 a'；动作 a 的选取是根据当前 Q 网络以及策略（e-greedy），目标 Q 值的计算是根据 Q 值最大的动作 a' 计算得来



#### 策略梯度


* Bellman公式的核心是求精确的值函数，然后求最优的策略$\pi^*$
$\boldsymbol { a } ^ { * } = \operatorname { argmax } _ { \boldsymbol { a } } Q ( \boldsymbol { s } , \boldsymbol { a } ) , \boldsymbol { a } ^ { * } = \pi ^ { * } ( \boldsymbol { s } )$

* 策略梯度采用了另外一种思路，其目标是最大化长期回报期望，其中$\tau$ 代表轨迹序列，$r(\tau)$代表这条轨迹序列的总体回报。
$\pi ^ { * } = \operatorname { argmax } _ { \pi } E _ { \tau \sim \pi ( \tau ) } [ r ( \tau ) ]$

求策略梯度的步骤
* (1)计算$\nabla _ { \theta } J ( \theta )$
* (2)$\tilde { \theta } = \theta + \alpha \nabla _ { \theta } J ( \theta )$

其中
$\begin{aligned} \nabla _ { \theta } J ( \theta ) & = \int _ { \tau \sim \pi _ { \theta } ( \tau ) } \pi _ { \theta } ( \tau ) \nabla _ { \theta } \log \pi _ { \theta } ( \tau ) r ( \tau ) \mathrm { d } \tau \\ & = E _ { \tau \sim \pi _ { \theta } ( \tau ) } \left[ \nabla _ { \theta } \log \pi _ { \theta } ( \tau ) r ( \tau ) \right] \end{aligned}$

即当前$log(p(s,a))*r(s,a)$

