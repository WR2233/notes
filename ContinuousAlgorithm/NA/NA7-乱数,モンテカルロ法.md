
## 乱数・モンテカルロ法
数値計算で利用する乱数は通常「擬似乱数」\
決定論的アルゴリズムで生成されるランダムに見える数列(<- 内部状態依存)\
内部状態は有限サイズだから、何らかの周期性がある

初期条件を決めると数列が<u>確定</u>する -> 乱数を使う計算を再現できる
- 実用： [0, 1)区間の一様乱数を発生させるアルゴリズムを出発点に所望の分布の乱数を発生させる

### 一様乱数生成のアルゴリズム
1. 線形合同法
    $$
      x_n= ax_{n-1}+ C (\mod{m})
    $$
    特に、$C=0$の時、乗算合同法
2. lagged Fibonacchi 乱数
    $$
      x_n= x_{n-q} \ XOR \ x_{n-p} \  (q < p)
    $$
    $x_{n-1} \rightarrow  x_{n-p}$までで$x_n$が決定 $\Rightarrow$ 最大周期$2^p-1$\
    特に、最大周期$2^p-1$になるものを M系列(最長周期系列)乱数という。

3. Mersenne Teister法
    - 近年よく利用される方法(パラメータMT19937)
    - 周期$2^{19937}-1$ $\rightarrow$
非常に長い
    - 623次元で均等分布

    異なる初期状態を並列に用意して並列に乱数生成しても安全\
    $\Rightarrow$ <u>特に理由がなければこれを選択</u>

### 擬似乱数利用の注意点
1. 下位ビット側のランダム性が悪いことがある(線形合同法)\
  $\Rightarrow$ ($x_n \mod t$)
  剰余を使って範囲の狭い乱数を発生させないこと\
  - 正しくは以下
  $$
    t \left(　\frac{x_n}{RAND_MAX +1.0}　\right)
  $$

2. $(x_n, x_{n-1})$を2次元プロット $\Rightarrow$ ランダム分布せず、結晶構造が出ることがある(線形合同法)

### 一様分布以外の生成
1. 逆関数法 (inverse CDF method)\
    累積密度関数(CDF)
    $$
      f(x) = \int_{-\infty}^x p(t) dt
    $$
      - $f(x)$は単調
      - $f(-\infty) = 0, \ f(+\infty) = 1, $

  - アルゴリズム: $p(x)$に従う乱数を生成
    [0, 1)の一様乱数$U$から、以下を計算する
      $$
        X = f^{-1}(U)
      $$

  - 説明: \
    $X$が$p(x)$に従うなら、$0 \leq y \leq 1$の$y$に対して、
    $$
    \begin{aligned}
      確率P[f(X)\leq y] &= P[X\leq f^{-1}(y)]\\\\
      &= \int_{-\infty}^{f^{-1}(y)}p(t)dt \\\\
      &= y
    \end{aligned}
    $$
    であるから、$f(X)$は[0, 1)で一様分布する\
    逆に、Yが$P[Y\leq y] =y$と一様分布するなら
    $$
      f(x) = P[Y\leq f(x)] = P[f^{-1}(Y)\leq x]
    $$
    である。\
    $P[f^{-1}(Y)\leq x] \Rightarrow$ CDF $= f(x)$より、$f^{-1}(x)$は$p(x)$に従う


2. 棄却法 rejection method\
  2D矩形領域中の一様分布から発生したい分布($=f(x)$)を切り出す\
  - 条件
    - $a \leq f(x) \leq b$の外では$f(x) =0$
    - $f(x) \leq c$

    %ずを挿入 

    矩形$[a, b]\times [0,c]$の中で、2D一様分布する(X,Y)を以下のようにして生成する
    $$
    \begin{aligned}
      X &= a + U (b-a)\\\\\\\\
      Y &= cV
    \end{aligned}\\\\\\\\
    ただし、U, Vは[0, 1)の一様分布
    $$

  - アルゴリズム\
    $f(x)$に従う$X$を生成
    1. X, Yを生成
    2. $f(X) \leq Y$ であれば、$X$を採択、でなければ$(X,Y)$ を捨てる
  
  - 説明：\
    条件付き確率を計算
    $$
      P(f(X)\leq Y かつ X \leq x) = \frac 1 {c(b-a)} \int_a^x f(t) dt
    $$
    $$
    \begin{aligned}
      P(f(X)\leq Y) &= \frac 1 {c(b-a)} \int_a^b f(x) dx\\\\
        &=\frac 1 {c(b-a)}
    \end{aligned}
    $$
    よって、
    $$
    \begin{aligned}
    P(X\leq x | f(X) \leq Y) &= \frac {P(f(X)\leq Y かつ X \leq x)}{P(f(X)\leq Y)} \\\\
      &= \int_a^x f(t)dt \  (= CDF)
    \end{aligned}
    $$
  - 注意点\
    このアルゴリズムは捨てられる(X, Y)が多いと効率が悪い

  - 変形\
    別な確率分布$q(x)$と定数$S$について
    $$
      p(x) \leq Sq(x)
    $$
    を仮定する
    - アルゴリズム$p(x)$を一様確率分布と$q(x)$から生成する
      1. 一様乱数U, $q(x)$に従う$X$を生成する
      2. $Sq(X)U \leq　p(X)$なら、$X$を採択。さもなければ、$U$,$X$を捨てる

    - 説明
      $$
      \begin{aligned}
        P(X\leq x かつSq(x)U \leq p(X) ) &= P(X\leq x かつ U \leq \frac{p(X)} {Sq(x)} )\\\\
        &= \int_{-\infty}^x q(t) \frac{p(t)} {Sq(x)} dt \\\\
        &= \frac 1 S \int_{-\infty}^x p(t)dt
        \end{aligned}\\\\
        P(Sq(x)U \leq p(X))= \frac 1 S
      $$
      よって、
      $$
      \begin{aligned}
        P(X\leq x | Sq(x)U \leq p(X) ) &= \frac {P(X\leq x かつSq(x)U \leq p(X) )}{P(Sq(x)U \leq p(X))}\\\\
        &= \int_{-\infty}^x p(t)dt
      \end{aligned}
      $$

### 標準正規分布の生成: ボックス・マラー法
独立な二次元標準正規分布$(X,Y)$を得る方法
- アルゴリズム\
  $U_1$を[0, 1)の一様分布\
  $U_2$を(0, 1]の一様分布とする
  $$
    X =\sqrt{-2\log {U_1}}\cos{(2\pi U_2)}\\\\
    Y =\sqrt{-2\log {U_2}}\sin{(2\pi U_2)}
  $$

- 説明
  $$
  \begin{aligned}
    du_1du_2 &=\frac 1 {\sqrt{2\pi}} e^{-\frac {x^2} 2}\frac 1 {\sqrt{2\pi}} e^{-\frac {y^2} 2}dxdy\\\\
    &= \frac 1 {2\pi} e^{-\frac {r^2}2}rdrd\theta
  \end{aligned}
  $$
  ただし、$(x, y ) = (r\cos \theta, r\cos \theta)$とした\
  この時、積分区間は  $\thetaは[0, 2\pi)$であるから、\
  $s = r^2$とすると、
  $$
  \begin{aligned}
    du_1du_2 &= \frac 1 2 e^{-\frac {s}2}ds
  \end{aligned}
  $$
  よって、
  $$
    CDF = \frac 1 2 \int_{-\infty}^x e^{-\frac {s}2}ds\\\\
      = e^{-x/2}
  $$
  すなわち、
  $$
    CDF = -2\log(u)
  $$
  逆関数法を用いて、一様なUから$S=-2\log(U)$として\
  あとで、$S, \theta \Rightarrow X, Y$にもどす

- 備考
  ボックス・マラー法でのsの分布関数$1/2 e^{-s/2}$の生成に必要な累積分布関数は
  $f(x) = 1/2 \int_0^x e^{-s/2} ds = 1 - e^{-x/2}$
  で$f^{-1}(x) = -2 \log(1-x)となる$
  Xが[0,1)の一様分布であれば1-Xは(0,1]の一様分布である\
  逆関数法を使うことができて$S = -2\log(U)$と生成する


- 他次元の正規分布\
  平均$\mu_i$, 共分散$V_{i,j}$\
  共分散行列$V$は正定値対称より、$V =LL^\top$ (コレスキー分解)\
  $\boldsymbol{y} = \boldsymbol{\mu} + L \boldsymbol{x} $として$\boldsymbol{y}$を決めると、
  $$
    \boldsymbol{x} = L^{-1}(\boldsymbol{y} - \boldsymbol{\mu})\\\\
    d\boldsymbol{x} = L^{-1}\boldsymbol{y}
  $$
  これを使って、
  $$
    \prod_i N(x_i)dx_i = \frac1{(2\pi)^{N/2}} e^{-\frac 1 2 \boldsymbol{x}^\top \boldsymbol{x}} \prod_i dx_i\\\\
    = \frac1{(2\pi)^{N/2}} \exp{(-\frac 1 2 {(\boldsymbol{y} - \boldsymbol{\mu})}^\top L^{-1\top}L^{-1} {(\boldsymbol{y} - \boldsymbol{\mu})})} \det(L^{-1}) dy_1dy_2\dots dy_N \\\\
    = \frac1{(2\pi)^{N/2}} \exp{(-\frac 1 2 {(\boldsymbol{y} - \boldsymbol{\mu})}^\top V^{-1}{(\boldsymbol{y} - \boldsymbol{\mu})})} (\det(V))^{-1/2} dy_1dy_2\dots dy_N
  $$


### 乱数の応用
- モンテカルロ法による積分\
  $p(x)$にしたがう互いに独立な$X_i$に対して
  $$
    I_N = \frac 1 N \sum_{i=1}^N f(X_i)
  $$
  とすると、$\int p(x) dx =1$であり、
  $$
  \begin{aligned}
  E(I_N)&= E(\frac 1 N \sum_{i=1}^N f(X_i))\\\\ 
        &= \int dx_1dx_2\dots dx_N \frac 1 N \sum_i (f(x_i)p(x_1)p(x_2)\dots p(x_N)) \\\\
        &= \frac 1 N \sum_i \int dx_i f(x_i)p(x_i)\\\\
        &= \int f(x)p(x)dx\\\\
  \end{aligned}
  $$
  従って、$I_N$によって積分$I=\int{ f(x)p(x)dx}$を近似できた\
  明らかに多次元でも実行可能\
  分散$V(I_N)$を考える
  $$  
  \begin{aligned}
  V(I_N) &= E((\frac 1 N \sum_{i=1}^N f(X_i) - I)^2) \\\\
         &= \frac 1 {N^2} E(\sum_{i,j} (f(X_i) - I)(f(X_j) - I)) \\\\
         &= \frac 1 {N^2} \int dx_1dx_2\dots dx_N\sum_{i,j} (f(x_i) - I)(f(x_j) - I)p(x_1)p(x_2)\dots p(x_N)\\\\
  \end{aligned}
  $$
  ここで、$i\neq j$の時、
  $$  
  \begin{aligned}
  &\int dx_1dx_2\dots dx_N (f(x_i) - I)(f(x_j) - I)p(x_1)p(x_2)\dots p(x_N)\\\\ 
  = &\int dx_i (f(x_i) - I)p(x_i)dx_i \int dx_j (f(x_j) - I) p(x_j)dx_j\\\\
  = &0
  \end{aligned}
  $$
  よって、
  $$
  \begin{aligned}
  V(I_N) &=\frac 1 {N^2} \sum_i \int dx_i (f(x_i)-I)^2p(x_i)\\\\
         &= \frac 1 N V[f(X)]
  \end{aligned}
  $$
  以上より\
  分散が$1/N$となる
  $$
  \sigma(I_N) = \sqrt {V_N} = \frac {\sigma(f(X))}{\sqrt{N}}
  $$

- 備考
  格子方でおこなうと
  サンプル数Nでd次元で考えると、メッシュ1辺$\propto N^{1/d}$\
  $d \rightarrow \infty$で分散が大きくなる