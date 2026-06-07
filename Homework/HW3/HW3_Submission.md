# 量子计算研讨课 HW3

姓名：张芝源

---

## Problem 1

设 qubit 密度矩阵为 $\rho$，并定义

$$
U_{a,b}=\sigma_z^a\sigma_x^b,\qquad
\mathrm{OTP}_{a,b}(\rho)=U_{a,b}\rho U_{a,b}^\dagger,
$$

其中 $a,b\in\{0,1\}$。

### (a)

证明

$$
\mathbb E_{a,b}\,\mathrm{OTP}_{a,b}(\rho)=\frac I2.
$$

任意单比特密度矩阵都可以写成 Bloch 形式：

$$
\rho=\frac12\left(I+r_x\sigma_x+r_y\sigma_y+r_z\sigma_z\right),
$$

其中 $r_x,r_y,r_z\in\mathbb R$。

注意到共轭作用满足

$$
\sigma_z\sigma_x\sigma_z=-\sigma_x,\quad
\sigma_z\sigma_y\sigma_z=-\sigma_y,\quad
\sigma_z\sigma_z\sigma_z=\sigma_z,
$$

以及

$$
\sigma_x\sigma_x\sigma_x=\sigma_x,\quad
\sigma_x\sigma_y\sigma_x=-\sigma_y,\quad
\sigma_x\sigma_z\sigma_x=-\sigma_z.
$$

因此

$$
U_{a,b}\rho U_{a,b}^\dagger
=
\frac12\Bigl(
I+(-1)^ar_x\sigma_x+(-1)^{a+b}r_y\sigma_y+(-1)^br_z\sigma_z
\Bigr).
$$

对 $a,b$ 均匀平均后，
$\sigma_x,\sigma_y,\sigma_z$ 前面的系数都变成 $0$，因为

$$
\mathbb E_{a,b}[(-1)^a]
=
\mathbb E_{a,b}[(-1)^b]
=
\mathbb E_{a,b}[(-1)^{a+b}]
=0.
$$

所以

$$
\mathbb E_{a,b}\,\mathrm{OTP}_{a,b}(\rho)=\frac I2.
$$

### (b)

证明对加密态施加 $X$ 门后，得到的是 $\mathrm{OTP}_{a,b}(X\rho X)$。

只需验证

$$
XU_{a,b}=(-1)^aU_{a,b}X.
$$

因为

$$
XU_{a,b}=X\sigma_z^a\sigma_x^b
=(-1)^a\sigma_z^aX\sigma_x^b
=(-1)^a\sigma_z^a\sigma_x^bX
=(-1)^aU_{a,b}X.
$$

于是

$$
X\,\mathrm{OTP}_{a,b}(\rho)\,X
=XU_{a,b}\rho U_{a,b}^\dagger X
=U_{a,b}(X\rho X)U_{a,b}^\dagger
=\mathrm{OTP}_{a,b}(X\rho X).
$$

也就是说，对密文直接作用 $X$，等价于对明文作用 $X$，且密钥不变。

### (c)

证明对加密态施加 $H$ 门后，得到 $\mathrm{OTP}_{b,a}(H\rho H)$。

利用

$$
H\sigma_zH=\sigma_x,\qquad H\sigma_xH=\sigma_z,
$$

可得

$$
HU_{a,b}=H\sigma_z^a\sigma_x^b=\sigma_x^a\sigma_z^bH.
$$

又因为

$$
\sigma_x^a\sigma_z^b=(-1)^{ab}\sigma_z^b\sigma_x^a,
$$

所以

$$
HU_{a,b}=(-1)^{ab}U_{b,a}H.
$$

从而

$$
H\,\mathrm{OTP}_{a,b}(\rho)\,H
=HU_{a,b}\rho U_{a,b}^\dagger H
=U_{b,a}(H\rho H)U_{b,a}^\dagger
=\mathrm{OTP}_{b,a}(H\rho H).
$$

因此施加 Hadamard 门会交换 one-time pad 的两个密钥位。

---

## Problem 2

已知系统状态以相同概率取为 $\ket{\psi_0}$ 或 $\ket{\psi_1}$，且

$$
|\langle\psi_0|\psi_1\rangle|=s>0.
$$

设计一个三结果 POVM $\{M_0,M_1,M_*\}$，满足：

1. 若输出 $0$，则状态一定是 $\ket{\psi_0}$；
2. 若输出 $1$，则状态一定是 $\ket{\psi_1}$；
3. 输出 $*$ 的概率尽可能小。

这就是两纯态的 **unambiguous state discrimination** 问题。
下面先讨论非平凡情形 $0<s<1$。若 $s=1$，说明两态实际上相同，此时不可能无误地区分它们，只能总是输出 $*$。

不妨取一组正交基 $\{\ket0,\ket1\}$，使得

$$
\ket{\psi_0}=\ket0,\qquad
\ket{\psi_1}=s\ket0+\sqrt{1-s^2}\ket1.
$$

定义各自的正交态

$$
\ket{\psi_0^\perp}=\ket1,\qquad
\ket{\psi_1^\perp}=\sqrt{1-s^2}\ket0-s\ket1.
$$

构造 POVM：

$$
M_0=c\,\ket{\psi_1^\perp}\bra{\psi_1^\perp},\qquad
M_1=c\,\ket{\psi_0^\perp}\bra{\psi_0^\perp},\qquad
M_*=I-M_0-M_1.
$$

这个构造不会误判，因为

$$
\langle\psi_1|M_0|\psi_1\rangle
=c|\langle\psi_1^\perp|\psi_1\rangle|^2=0,
$$

以及

$$
\langle\psi_0|M_1|\psi_0\rangle=0.
$$

所以输出 $0$ 只可能来自 $\ket{\psi_0}$，输出 $1$ 只可能来自 $\ket{\psi_1}$。

接下来选择最大的 $c$，使得 $M_*\succeq0$。在基 $\{\ket0,\ket1\}$ 下，

$$
\ket{\psi_1^\perp}\bra{\psi_1^\perp}
+
\ket{\psi_0^\perp}\bra{\psi_0^\perp}
=
\begin{pmatrix}
1-s^2 & -s\sqrt{1-s^2}\\
-s\sqrt{1-s^2} & 1+s^2
\end{pmatrix}.
$$

该矩阵特征值为 $1-s$ 和 $1+s$，因此为了保证

$$
M_0+M_1\preceq I,
$$

必须有

$$
c\le \frac1{1+s}.
$$

最优取值就是

$$
c=\frac1{1+s}.
$$

于是最优 POVM 为

$$
M_0=\frac1{1+s}\ket{\psi_1^\perp}\bra{\psi_1^\perp},\qquad
M_1=\frac1{1+s}\ket{\psi_0^\perp}\bra{\psi_0^\perp},\qquad
M_*=I-M_0-M_1.
$$

对 $\ket{\psi_0}$，失败概率为

$$
\Pr[*|\psi_0]
=\langle\psi_0|M_*|\psi_0\rangle
=1-\frac{1-s^2}{1+s}
=s.
$$

同理

$$
\Pr[*|\psi_1]=s.
$$

由于两种状态先验概率相同，总的 inconclusive probability 也是

$$
\Pr[*]=s,
$$

这就是最小可能值。

---

## Problem 3

证明：对两比特态 $\rho$，

1. 先做一次 CNOT（第一比特为控制，第二比特为目标）；
2. 再对第二比特做 $Z$ 基测量；

这一过程等价于直接对 $\rho$ 测量可观测量 $Z\otimes Z$。

设

$$
\Pi_0=I\otimes\ket0\bra0,\qquad
\Pi_1=I\otimes\ket1\bra1.
$$

若 $U_{\mathrm{CNOT}}$ 表示上述 CNOT 门，则测得结果 $b\in\{0,1\}$ 的概率为

$$
p_b
=
\operatorname{Tr}\!\left(
\Pi_b\,U_{\mathrm{CNOT}}\rho U_{\mathrm{CNOT}}^\dagger
\right)
=
\operatorname{Tr}\!\left(
U_{\mathrm{CNOT}}^\dagger\Pi_bU_{\mathrm{CNOT}}\rho
\right).
$$

因此，这个流程等价于对输入态 $\rho$ 直接测量 POVM 元素

$$
E_b=U_{\mathrm{CNOT}}^\dagger\Pi_bU_{\mathrm{CNOT}}.
$$

观察 CNOT 在计算基上的作用：

$$
\ket{00}\mapsto\ket{00},\quad
\ket{01}\mapsto\ket{01},\quad
\ket{10}\mapsto\ket{11},\quad
\ket{11}\mapsto\ket{10}.
$$

于是

$$
E_0=\ket{00}\bra{00}+\ket{11}\bra{11},
$$

$$
E_1=\ket{01}\bra{01}+\ket{10}\bra{10}.
$$

另一方面，$Z\otimes Z$ 在

- $\ket{00},\ket{11}$ 上的本征值为 $+1$；
- $\ket{01},\ket{10}$ 上的本征值为 $-1$。

因此它的谱分解对应的两个投影算符是

$$
\frac{I+Z\otimes Z}{2}
=\ket{00}\bra{00}+\ket{11}\bra{11},
$$

$$
\frac{I-Z\otimes Z}{2}
=\ket{01}\bra{01}+\ket{10}\bra{10}.
$$

也就是说

$$
E_0=\frac{I+Z\otimes Z}{2},\qquad
E_1=\frac{I-Z\otimes Z}{2}.
$$

这正是测量 $Z\otimes Z$ 时对应的两个结果投影，所以两种过程完全等价。

---

## Problem 4

设 $f:\{0,1\}^n\to\{0,1\}^{n-1}$ 是一个 $2$-to-$1$ 函数，每个
$y\in\{0,1\}^{n-1}$ 都恰好有两个不同原像 $x,x'$。

要求说明：量子计算机像 Simon 算法那样，能够高效产生一个均匀随机的 $y$ 以及对应的 $n$ 比特态 $\ket{\phi_y}$，然后无论裁判要求 Task 0 还是 Task 1，都能成功完成。

### 第一步：生成 $y$ 和 $\ket{\phi_y}$

量子计算机先制备

$$
\frac1{\sqrt{2^n}}\sum_{x\in\{0,1\}^n}\ket x\ket{0^{n-1}},
$$

然后调用可高效实现的量子线路计算 $f$，得到

$$
\frac1{\sqrt{2^n}}\sum_{x\in\{0,1\}^n}\ket x\ket{f(x)}.
$$

测量第二个寄存器后，得到某个 $y\in\{0,1\}^{n-1}$。由于 $f$ 是 $2$-to-$1$，每个 $y$ 恰有两个原像 $x\neq x'$，而且所有 $y$ 出现概率相同，因此 $y$ 是均匀随机的。

此时第一个寄存器坍缩为

$$
\ket{\phi_y}=\frac{\ket x+\ket{x'}}{\sqrt2}.
$$

这就是所需的态。

### Task 0

如果裁判要求给出一个满足 $f(x)=y$ 的原像，那么直接把 $\ket{\phi_y}$ 在计算基上测量即可。

测量结果是 $x$ 或 $x'$，两者概率各为 $1/2$，并且都满足

$$
f(x)=y.
$$

因此 Task 0 一定成功。

### Task 1

令

$$
s=x\oplus x'.
$$

对 $\ket{\phi_y}$ 施加 $H^{\otimes n}$：

$$
H^{\otimes n}\ket{\phi_y}
=
\frac1{\sqrt2}\cdot\frac1{\sqrt{2^n}}
\sum_{a\in\{0,1\}^n}
\left((-1)^{a\cdot x}+(-1)^{a\cdot x'}\right)\ket a.
$$

提取 $(-1)^{a\cdot x}$ 后，

$$
H^{\otimes n}\ket{\phi_y}
=
\frac1{\sqrt{2^{n+1}}}
\sum_{a\in\{0,1\}^n}
(-1)^{a\cdot x}\left(1+(-1)^{a\cdot(x\oplus x')}\right)\ket a.
$$

若

$$
a\cdot(x\oplus x')=1\pmod2,
$$

则括号中为 $0$，该项振幅消失。

若

$$
a\cdot(x\oplus x')=0\pmod2,
$$

则括号中为 $2$，这些 $a$ 的振幅绝对值全部相同。

所以

$$
H^{\otimes n}\ket{\phi_y}
=
\frac1{\sqrt{2^{n-1}}}
\sum_{a\cdot(x\oplus x')=0}
(-1)^{a\cdot x}\ket a.
$$

对该态在计算基上测量，就会在集合

$$
\left\{
a\in\{0,1\}^n:\ a\cdot(x\oplus x')=0\pmod2
\right\}
$$

上均匀采样。

这正是 Task 1 的要求，因此也一定成功。

### 结论

量子计算机可以高效生成均匀随机的 $y$ 与对应态

$$
\ket{\phi_y}=\frac{\ket x+\ket{x'}}{\sqrt2},
$$

并且无论裁判要求 Task 0 还是 Task 1，都能以 100% 成功率完成。

---

## Problem 5

设 $U$ 是一个 $n$ 比特酉矩阵，输入态为

$$
\rho_{\mathrm{in}}=\ket0\bra0\otimes\frac{I}{2^n}.
$$

对第一个比特依次执行：

1. 施加 Hadamard 门；
2. 施加 controlled-$U$；
3. 再施加一个 Hadamard 门；
4. 在计算基测量第一个比特。

证明测得结果 $0$ 的概率是

$$
P(0)=\frac12\left(1+\operatorname{Re}\left[\frac{\operatorname{Tr}(U)}{2^n}\right]\right).
$$

### 第一步：第一次 Hadamard 之后

因为 $H\ket0=\ket+$，所以

$$
\rho_1=\ket+\bra+\otimes\frac{I}{2^n}
=
\frac12
\begin{pmatrix}
1&1\\
1&1
\end{pmatrix}
\otimes\frac{I}{2^n}.
$$

### 第二步：施加 controlled-$U$

设

$$
C_U=\ket0\bra0\otimes I+\ket1\bra1\otimes U.
$$

则

$$
\rho_2=C_U\rho_1C_U^\dagger
=
\frac1{2^{n+1}}
\begin{pmatrix}
I & U^\dagger\\
U & I
\end{pmatrix},
$$

这里把矩阵按第一个比特的基 $\{\ket0,\ket1\}$ 写成分块形式。

### 第三步：对后面 $n$ 个比特取偏迹

第一个比特的约化态为

$$
\rho_{\mathrm{ctrl}}
=
\frac12
\begin{pmatrix}
1 & \operatorname{Tr}(U^\dagger)/2^n\\
\operatorname{Tr}(U)/2^n & 1
\end{pmatrix}.
$$

### 第四步：第二次 Hadamard 后测量

测量结果为 $0$ 的概率是

$$
P(0)
=
\operatorname{Tr}\!\left(\ket0\bra0\,H\rho_{\mathrm{ctrl}}H\right).
$$

利用

$$
H\ket0\bra0H=\frac{I+X}{2},
$$

得到

$$
P(0)
=
\operatorname{Tr}\!\left(\frac{I+X}{2}\rho_{\mathrm{ctrl}}\right)
=
\frac12\left(\operatorname{Tr}(\rho_{\mathrm{ctrl}})+\operatorname{Tr}(X\rho_{\mathrm{ctrl}})\right).
$$

由于 $\operatorname{Tr}(\rho_{\mathrm{ctrl}})=1$，并且

$$
\operatorname{Tr}(X\rho_{\mathrm{ctrl}})
=
\frac{\operatorname{Tr}(U)+\operatorname{Tr}(U^\dagger)}{2^{n+1}}
=
\operatorname{Re}\left(\frac{\operatorname{Tr}(U)}{2^n}\right),
$$

所以

$$
P(0)
=
\frac12\left(
1+\operatorname{Re}\left[\frac{\operatorname{Tr}(U)}{2^n}\right]
\right).
$$

证毕。
