計算量理論レポート
- 05251031
- 和久田隆平

# (1) 古典の場合、どんな戦略を取ってもAliceとBobの勝つ確率は3/4以下であることを示せ

AliceとBobはそれぞれビットnを受け取ったとき、$a_n$, $b_n$を返す戦略を取るとする($n=0, 1$)

$(x,y)$の全てのパターンで勝つことのできる戦略が存在すると仮定すると、
- $(x,y)=(0,0)$の時、勝つ条件は$a_0\oplus b_0 = 0$
- $(x,y)=(0,1)$の時、勝つ条件は$a_0\oplus b_1= 0$
- $(x,y)=(1,0)$の時、勝つ条件は$a_1\oplus b_0 = 0$
- $(x,y)=(1,1)$の時、勝つ条件は$a_1\oplus b_1 = 1$
以上4式の排他的論理和をとると、
$$
  (a_0\oplus b_0)\oplus (a_0\oplus b_1)\oplus(a_1\oplus b_0) \oplus (a_1\oplus b_1) = 0\oplus0\oplus0\oplus1 
  \tag{1}
$$
ここで、
$$
\begin{aligned}
(\text{左辺}) &= (a_0\oplus a_0)\oplus (a_1\oplus a_1)\oplus(b_0\oplus b_0) \oplus (b_1\oplus b_1) \\\\
  &= 0\oplus0\oplus0\oplus0 \\\\
  &= 0 \\\\
(\text{右辺}) &= 1
\end{aligned}
$$
よって、矛盾。４パターン全てで勝つことのできる戦略は存在しない。\
従って、AliceとBobの勝つ確率は $\frac {4-1} 4 = \frac {3} 4$以下である。

# (2) 量子状態が共有される時、問題の戦略を考えた時のAliceとBobの勝つ確率を求めよ。

$R(\theta) = 
\begin{pmatrix}
\cos\theta & -\sin \theta \\\\
\sin\theta & \cos\theta
\end{pmatrix}$
とすると、
$$
\begin{aligned}
R(\theta)|0 \rangle &= \cos\theta |0 \rangle+ \sin\theta |1\rangle \\\\
R(\theta)|1 \rangle &= -\sin\theta |0 \rangle+ \cos\theta |1\rangle
\end{aligned}
$$

Alice, Bobがそれぞれ$\theta_A$, $\theta_B$の回転をそれぞれの量子ビットに加えた後の量子状態$|\psi'\rangle$を考えると
$$
  |\psi'\rangle = (R(\theta_A) \otimes  R(\theta_B)) |\psi\rangle
$$

ここで、
$$
\begin{aligned}
  &(R(\theta_A) \otimes  R(\theta_B)) |00\rangle \\\\
  = &R(\theta_A)|0\rangle \otimes  R(\theta_B)|0\rangle \\\\
  = &\cos\theta_A \cos\theta_B |00\rangle + \cos\theta_A \sin\theta_B |01\rangle+  \sin\theta_A \cos\theta_B |10\rangle + \sin\theta_A \sin\theta_B |11\rangle \\\\
  &(R(\theta_A) \otimes  R(\theta_B)) |11\rangle \\\\
  = &R(\theta_A)|1\rangle \otimes  R(\theta_B)|1\rangle \\\\
  = &\sin\theta_A \sin\theta_B |00\rangle - \sin\theta_A \cos\theta_B |01\rangle-  \cos\theta_A \sin\theta_B |10\rangle + \cos\theta_A \cos\theta_B |11\rangle
\end{aligned}
$$

よって、
$$
\begin{aligned}
|\psi'\rangle &= \frac 1 {\sqrt 2} ((R(\theta_A) \otimes  R(\theta_B)) |00\rangle + (R(\theta_A) \otimes  R(\theta_B)) |11\rangle) \\\\
&=  \frac 1 {\sqrt 2} (\cos(\theta_A -\theta_B)|00\rangle- \sin(\theta_A -\theta_B)|01\rangle + \sin(\theta_A -\theta_B)|10\rangle + \cos(\theta_A- \theta_B)|11\rangle) \\\\
&=  \frac 1 {\sqrt 2} (\cos(\theta_A -\theta_B)(|00\rangle + |11\rangle) + \sin(\theta_A -\theta_B)(|10\rangle - |01\rangle))
\end{aligned}
$$

従って、
$$
\begin{aligned}
P(00) &= \left| \frac 1 {\sqrt 2} \cos(\theta_A -\theta_B) \right|^2 \\\\
 &= \frac 1 2 \cos^2(\theta_A -\theta_B) \\\\
&\text{同様にして、} \\\\
P(11) &= \frac 1 2 \cos^2(\theta_A -\theta_B) \\\\
P(01) &= \frac 1 2 \sin^2(\theta_A -\theta_B) \\\\
P(10) &= \frac 1 2 \sin^2(\theta_A -\theta_B)
\end{aligned}
$$

よって、aとbが一致する確率$P(a=b)$, 一致しない確率$P(a\neq b)$は、
$$
\begin{aligned}
P(a=b)     &= P(00) + P(11) = \cos^2(\theta_A -\theta_B) \\\\
P(a\neq b) &= P(01) + P(10) = \sin^2(\theta_A -\theta_B)
\end{aligned}
$$

四パターンそれぞれについて勝利する確率を計算する
1. $(x,y) = (0,0)$の時、勝利条件は$a=b$で\
$\theta_A = 0$, $\theta_B = \pi/8$だから、
$$
P(a=b) = \cos^2(0 -\pi/8) = \cos^2(\pi/8)
$$

2. $(x,y) = (0,1)$の時、勝利条件は$a=b$で\
$\theta_A = 0$, $\theta_B = -\pi/8$だから、
$$
P(a=b) = \cos^2(0 +\pi/8) = \cos^2(\pi/8)
$$

3. $(x,y) = (1,0)$の時、勝利条件は$a=b$で\
$\theta_A = \pi/4$, $\theta_B = \pi/8$だから、
$$
P(a=b) = \cos^2(\pi/4 -\pi/8) = \cos^2(\pi/8)
$$

4.  $(x,y) = (1,1)$の時、勝利条件は$a\neq b$で\
$\theta_A = \pi/4$, $\theta_B = -\pi/8$だから、
$$
P(a\neq b) = \sin^2(\pi/4 +\pi/8) = \sin^2(3\pi/8) = \cos^2(\pi/8)
$$

以上より、いずれの場合も成功確率は$\cos^2(\pi/8)$だから、\
全体として成功確率は$\cos^2(\pi/8) \approx 0.85$