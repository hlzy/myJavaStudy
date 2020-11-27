LFM

用隐语义模型来进行协同过滤的目标:

- 揭示隐藏特征

通过矩阵分解进行降维分析：

- 协同过滤依赖历史数据，而历史数据往往稀疏，这就需要隐藏特征

隐语义模型的实例：

- 基于概率的隐语义分析(pLSA)
  - 模型求解算法交替最小二乘法(Alternatting least squares ALS)
- 隐式迪利克雷分布模型LDA
- SVD

---

ALS推导过程: 20:00

## 1. 矩阵分解表

设用户，物品频分为表R

- U表示用户,设一共又n个用户
- I表示物品:设一共又m个物品
- C表示分类:设一共又c个分类
- 数字表示频分，空的为未评分

| R    | -I1  | -I2  | I3   |
| ---- | ---- | ---- | ---- |
| U1   | 1    |      | 3    |
| U2   |      | 2    |      |
| U3   | 3    |      | 4    |
| U4   | 1    | 2    |      |

用户分类得分P

| P    | C1   | C2   | C3   | C4   |
| ---- | ---- | ---- | ---- | ---- |
| U1   | P11  | P12  | P13  | P14  |
| U2   | P21  | P22  | P23  | P24  |
| U3   | P31  | P32  | P33  | P34  |
| U4   | P41  | P42  | P43  | P44  |

分类物品得分Q
| Q    | I1   | I2   | I3   |
| ---- | ---- | ---- | ---- |
| C1   | Q11  | Q12  | Q13  |
| C2   | Q21  | Q22  | Q23  |
| C3   | Q31  | Q32  | Q33  |
| C4   | Q41  | Q42  | Q43  |

## 2.目标

使 P*Q 接近R，（注意如果R对应位置没有评分则不应该减）
$$
LOSS = |R-P*Q|^2 + \gamma |P|^2 + \gamma|Q|^2
$$
公式可以根据对单个用户计算：
$$
LOSS = \sum_i^n (|R_i-P_i*Q| + \gamma|P_i|^2) + \gamma|Q|^2 \\
	 = \sum_i^n (\sum_j^m(R_{ij}-\sum_k^cP_{ik}*Q_{kj})^2 + \gamma\sum_k^cP_{ik}^2)
$$

$$
\frac {dLOSS} {dPik} =2\sum_j^m(R_{ij}-\sum_k^cP_{ik}*Q_{kj})*-Q_{kj} + 2\gamma P_{ik}
\\= -2(R_i - P_i*Q)*Q_k^T + 2\gamma P_{ik}
$$

进一步推导
$$
\frac {dLOSS} {dPi} =-2(R_i - P_i*Q)*Q^T + 2\gamma P_{i}
$$
在进一步推导
$$
\frac {dLOSS} {dP} =-2(R - P*Q)*Q^T + 2\gamma P
$$
update 21:00

由于这是凸函数，令其
$$
\frac {dLOSS} {dP} = 0
$$
推导
$$
-2(R - P*Q)*Q^T + 2\gamma P = 0\\
P = RQ^T(\gamma I + QQ^T)^{-1}
$$
同样可以推导当P确定时
$$
Q=RP^T(\gamma I + PP^T)^{-1}
$$


## 3.优化

R如果有空数，不做0处理，遇到R为空的部分不做计算