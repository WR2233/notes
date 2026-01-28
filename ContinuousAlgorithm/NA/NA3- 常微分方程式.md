# 連続系アルゴリズム 第6回
- 担当: 吉本さん

# 常微分方程式(ODE)初期値問題
## 問題設定
$$
スカラー値x\\
\frac {dy}{dx} = f(x, y)\\
y(x_0) = y_0
$$
自明にyがベクトルのケースに拡張 -> f(x, y)も拡張\
yの高階微分 -> yの微係数を並べたベクトルをyとする\
例: 
$$
\frac {d^2y}{dx^2} = f(x, y)\\
\rightarrow \frac d {dx} \begin{pmatrix} y \\ \frac {dy}{dx}\end{pmatrix} = \begin{pmatrix} \frac {dy}{dx}\\ f(x, y) \end{pmatrix}\\
$$

## 数値解法
 xの離散的な点$x_i (i= 0,1,2,\dots)$の上の$y(x_i)$を
 初期値$y(x_0)=y_0$から初めて、順に決定する\
 $y_i (i=1,2, 3, \dots)$は解の近似値\
 $y(x)$は真の解

## 基本的なアルゴリズム
$y_i \rightarrow y_{i+1}$を計算\
多くの場合、$x_1, x_2, x_3, \dots$は等間隔$h$

1. 前進Euler法\
    最も単純 前進差分による微分の近似
    $$
      \frac {dy}{dx} \approx \frac{y(x+h)- y(x)}{h}
    $$
    $y_{i+1} = y_i + hf(x_i, y_i)$として$y_{i+1}$を計算する\
    ただし、後退の場合は以下を用いて
      $$
        \frac {dy}{dx} \approx \frac{y(x)-y(x-h)}{h}
      $$
    - 局所誤差\
      テイラー展開から、
      $$
      \begin{aligned}
      y_(x_{i+1}) &= y(x_i)+ h \left.\frac{dy}{dx}\right|_{x = x_i} + O(h^2)\\ 
      &= y(x_i)+ h f(x_i, y(x_i)) + O(h^2)
      \end{aligned}
      $$
      もし、$y_i = y(x_i)$なら、$y_{i+1}$を前進Euler法で計算したときの誤差
      $$
      | y_{i+1} - y(x_{i+1})| = O(h^2)
      $$
      よって局所誤差は2次である
    
      1ステップでの誤差\
      単位長さxを進めた時のfの誤差\
      $$
      | \frac{y_{i+1}-y_i}{h} - \frac {y(x_{i+1})-y(x_i)} h | =  \frac{O(h^2)} h = O(h)
      $$
      よって、前進Euler法は一次の解法

    - 大域誤差\
      $x_0 \rightarrow x_n$の時、$y_n - y(x_n)$はどうなる？

      <仮定>
      1. f(x,y)はyに関してLipshitz連続\
        Lipshitz連続とは、xに依存せずある定数Lが存在して
        $$
          |f(x, y_1) - f(x, y_2)| \leq |y_1 - y_2|
        $$
      2. xに依存しない定数Kによって$y_i=y(x_i)$の時\
          $$
            |y_{i+1}-y(x_{i+1})| \leq Kh^2
          $$
          前進Euler法では、すなわち、
          $$
            |y(x_i) +hf(x_i, y(x_i))- y(x_{i+1})| \leq Kh^2
          $$
    - 大域誤差の計算
      $$
      \begin{aligned}
        y_{n+1} - y(x_{n+1}) &= y_n +h f(x_n, y_n) - y(x_{n+1})\\
        &= \underline{y_n-y(x_n)} +\underline{h f(x_n, y_n) - h f(x_n, y(x_n))} + \underline{y(x_n) +  h f(x_n, y(x_n)) - y(x_{n+1})}
      \end{aligned}
      $$
      仮定1と2から
      $$
      \begin{aligned}
        |y_{n+1} - y(x_{n+1})| 
        &\leq |y_n-y(x_n)|+ hL|y_n-y(x_n)| + Kh^2\\
        &\leq (1+hL)|y_n-y(x_n)| + Kh^2\\
        &\leq \dots\\
        &\leq (1+hL)^{n+1}|y_0-y(x_0)| + Kh^2((1+hL)^n + (1+hL)^{n-1} + \dots + (1+hL)+ 1)\\
        &\leq 0 + Kh^2 \frac {(1+hL)^{n+1}-1}{(1+hL)-1}\\
      \end{aligned}\\
      $$
      不等式 $1+x <  e^x$より
      $$
      \begin{aligned}
      |y_{n+1} - y(x_{n+1})| &= \frac{Kh}L ((1+hL)^{n+1}-1)\\
          &= \frac{Kh}L ((e^{hL})^{n+1}-1)\\
          &= \frac{Kh}L (e^{L(x_{n+1}-x_n)}-1) \ (\because h(n+1) = x_{n+1}-x_n)
      \end{aligned}\\
      $$
      よって大域誤差は1次


    - 安定性についての議論
      - <span style="color:green;">A安定</span>\
        絶対安全領域が左半面を含む解法のこと\
      - <span style="color:green;">絶対安全領域</span>\
        以下の方程式を解いて、$x \rightarrow \infty$で　$y\rightarrow0 $になる$\lambda (\in \mathbb{C})$の領域
        $$
        \frac {dy}{dx} = \lambda y \ (\lambda \in \mathbb{C})
        $$

      1. 前進オイラー法の場合、
          $$
          \begin{aligned}
            y_{n+1} &= y_n + h\lambda y_n\\
            &= (1+h\lambda)y_n
          \end{aligned}\\   
          $$
          $|1+h\lambda| < 1$ ならば、$x \rightarrow \infty で \ y \rightarrow 0$より、\
          絶対安全領域: $|1+h\lambda| < 1$ -> A安定でない
      2. 後退オイラー法の場合、
          $$
          \begin{aligned}
            y_{n} &= \frac {1}{1-h\lambda}y_{n-1}
          \end{aligned}\\   
          $$
          $| \frac {1}{1-h\lambda}| < 1$ ならば、$x \rightarrow \infty で \ y \rightarrow 0$より、\
          絶対安全領域: $|h\lambda-1| > 1$ -> A安定である。


2. Runge-Kutta法
- 一般\
  S段
  $$
  y_{n+1} = y_n + h\sum_{i=1}^S b_i k_i\\
  k_i = f(x_n+c_ih, y_n+ \sum_{j=1}^S a_{i,j}k_j)\\

  \sum_{j=1}^Sb_i =1, \ c_i = \sum_{j=1}^Sa_{i,j}, \ 0 \leq c_i \leq 1
  $$
  となる定数$a, b, c$

  $k_j$: 順にじめに計算できるには、$a_{i,j}$が狭義下三角行列であれば良い 

- 前進Euler法\
  $S=1, a_{1,1}= 0, b_1 =1, c_1= 0$
- 後退Euler法\
  $S=1, a_{1,1}=1, b_1=1, c_1=1$
- 中点則
  $$
  S=1, a_{1,1}=\frac 1 2, b_1 =1 , c_1 =\frac 1 2\\
  k_i = f(x_n+\frac 1 2 h, y_n+\frac 1 2 h k_1 ) \\
  y_{n+1}=  y_n + hk_1
  $$
- 2段陽的RK法
  $$
  $$
- 4段4次の陽的RK法
  - ルンゲによる公式
  $$
  y_{n+1} = y_n + h(\frac 1 6 k_1 + \frac 1 3 k_2 + \frac 1 3 k_3 + \frac 1 6 k_4 )\\
  k_1 = f(x_n, y_n)\\
  k_2 = f(x_n+\frac1 2 h, y_n+\frac1 2 h k_1)\\
  k_3 = f(x_n+\frac1 2 h, y_n+ \frac1 2 h k_2)\\
  k_4 = f(x_n+ h, y_n +h k_3)\\
  $$
  - クッタによる公式
  $$
  y_{n+1} = y_n + h(\frac 1 8 k_1 + \frac 3 8 k_2 + \frac 3 8 k_3 + \frac 1 8 k_4 )\\
  k_1 = f(x_n, y_n)\\
  k_2 = f(x_n+\frac1 3 h, y_n +\frac1 3 h k_1)\\
  k_3 = f(x_n+\frac2 3 h, y_n + h k_2- \frac 1 3 h k_1)\\
  k_4 = f(x_n+ h, y_n +h k_3 - hk_2+ hk_1)\\
  $$

- コメント\
  計算量の主要部はf(x,y)$の計算 -> ここの計算回数が問題\
  hのコントロール (刻み幅制御)\
  h, h/2 を比較 -> 誤差推定


