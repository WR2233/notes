# 連続系アルゴリズム 第12回?
- 担当: 吉本さん

## ポアソン方程式
  二次元の場合、$u$: 未知関数, $f$: 既知関数として以下
  $$
    -\left(
      \frac{\partial^2u}{\partial x^2} +\frac{\partial^2u}{\partial y^2}
      \right) = f(x, y)
    \tag{1}
  $$
  特に $f(x,y) \equiv 0$の時、ラプラス方程式という

## 境界条件
1. 第一種境界条件 (ディリクレ境界条件) \
  境界上のuの値が固定
2. 第一種境界条件 (ディリクレ境界条件)
  境界上の$u$の勾配で、境界に対して直交成分が固定\
- 注: 1と2が混在している条件の場合がある。
- 注: 1の条件がどこにもない場合、解に定数分の自由度が残る

## ポアソン方程式の数値解法「差分法」
- 計算領域\
  $(x_{i_x}, y_{i_y}) \rightarrow (i_x \Delta x, i_y \Delta y)$ として対応付ける\
  %ずを入れる\
  データ$u[i_x, i_y] \leftarrow u(x_{i_x}, y_{i_y})$, $f[i_x, i_y] \leftarrow f(x_{i_x}, y_{i_y})$として、配列で表現する

- 方程式の近似\
  2階微分は
    $$
      \left. \frac{\partial^2u}{\partial x^2} \right|_{x,y} = \frac {u(x+\Delta x, y) + u(x-\Delta x, y) - 2u(x,y)}{{\Delta x}^2 }+ O({\Delta x}^2) \\

      \left. \frac{\partial^2u}{\partial y^2} \right|_{x,y} = \frac {u(x, y+\Delta y) + u(x, y-\Delta y) - 2u(x,y)}{{\Delta y}^2 }+ O({\Delta y}^2)
    $$
  よって、式(1)は
    $$
    \begin{aligned}
      - \frac {u[i_x+1, i_y] + u[i_x-1, i_y] - 2u[i_x, i_y]}{{\Delta x}^2 } &\\
      - \frac {u[i_x, i_y+1] + u[i_x, i_y-1] - 2u[i_x, i_y]} {{\Delta y}^2 } &= f[i_x, i_y] \
    \end{aligned}
      \tag{2}

    $$
  $u[i_x, i_y] \rightarrow u[i]$として配列を一元化すると\
  式(2)は元の数$N_x\times N_y$の連立一次方程式 -> 係数行列は サイズ$(N_x\times N_y)^2$\
  係数行列の各行の非ゼロ要素の数は5個$(5 \ll N_x\times N_y)$
  - $[i_x, i_y]$の行の非ゼロ成分は $[i_x, i_y], [i_x\pm 1 , i_y\pm1]$
  - 係数行列は、五重対角行列となる
  - %図を挿入

- 境界条件の表現
  1. 第一種境界条件 (ディリクレ境界条件)\
    境界上の$u_{i_x, i_y}$を固定する
     
  2. 第一種境界条件 (ディリクレ境界条件)\
      勾配を隣り合う$u$の差分として表現する\
      例: 条件が$i_y = N_y で、\frac {\partial u}{\partial y} = b(x_i)$の時
      $$
      \left. \frac {\partial u}{\partial y} \right|_{x, \Delta y(N_y+\frac 1 2)}= b(x_i)
      $$
      中心差分をとって
      $$
      \frac {u[i_x, Ny+1]- u[i_x, N_y]}{\Delta y} = b[i_x] + O(\Delta y) \\
      \begin{aligned}
      u[i_x, Ny+1] =  u[i_x, N_y] + b[i_x] \Delta y \\
      \end{aligned}
      \tag3
      $$
      式(２)のうち、$i_y=N_y$の時の、$i_y+1 = N_y+1$の部分を式(3)を用いて書き直す。\


- 連立一時方程式の解法\
  正則行列解法を用いる\
  係数行列が、対称行列かつ正定値 -> CG法
  - 反復型アルゴリズム $A\times (ベクトル)$ \
  <プログラミング>\
    物理的2D格子についてループで回って、境界ベクトルを求める

    実装上は、確保する配列$u$のサイズを大きくして固定値で埋めて、条件分岐が無いように工夫する

- 解の収束性\
  第一種境界条件で、長方形領域の時とする
  - A. 適合性\
    $A \boldsymbol{u} = \boldsymbol{b}$の残差$\boldsymbol{r} =\boldsymbol{b} - A \boldsymbol{u}$ とする\
    解析解をサンプリングして、$\boldsymbol{u}^*$とする\
    適合性とは、以下が成り立つこと
    $$
      \boldsymbol{r}^* =\boldsymbol{b} - A \boldsymbol{u}^*が( \Delta x , \Delta y) \rightarrow (0, 0)で0に近づくこと
    $$
  - B. 安定性\
    安定性とは以下が成り立つこと
    $$
      A \boldsymbol{u} = \boldsymbol{b} の解の大きさ \max_{u_i \in \boldsymbol{u}}(|u_i|)がC\times \max(\max_{i: 内部}(|f_i|), \max_{i: 境界}(|u_i|))で抑えられること
    $$
  - C. 収束性\
    収束性とは以下が成り立つこと
    $$
    A \boldsymbol{u} = \boldsymbol{b} の解は、( \Delta x , \Delta y) \rightarrow (0, 0)で、\boldsymbol{u} \rightarrow \boldsymbol{u}^*
    $$
  - (AかつB) -> C を示す
    1. 以下の補題が成り立つ
    $$
      \begin{cases}
        \forall(i_x, i_y) \in \Omega, 
          \begin{aligned}
            4u[i_x, i_y]-u[i_x, i_y-1]-u[i_x-1, i_y]&\\-u[i_x, i_y+1]-u[i_x+1, i_y] \geq 0

          \end{aligned}\\
        \partial \Omega 上のある(i_x, i_y)について、u[i_x, i_y] \geq 0
      \end{cases} \
      \Rightarrow \forall (i_x, i_y) \in \Omega, u[i_x, i_y] \geq 0 
    $$

    2. Bを言い換えると以下
    $$
      \exists d, \max(|u_i|) \leq d\max(\max_{i \in \Omega}(|f_i|), \max_{i \in \Omega}(|u_i|))
    $$

    3. Aを言い換えると以下\
        $u(x,y)$は$c^4$級\
        領域$\Omega$内の$\forall (x, y)$で$\left| \frac{\partial^4u}{\partial x^4}\right| < M, \left|\frac{\partial^4u}{\partial y^4}\right| < M$\
        ここでテイラー展開より、
        $$
        \forall (x, y)\in \Omega, \forall h > 0, \exists \theta_+, \theta_- > 0, \\
        u(x+h, y) + u(x-h, y ) = 2u(x, y) + h^2\frac{\partial^2u}{\partial x^2} + \frac {h^4}{24}\left( \left.\frac{\partial^4u}{\partial x^4} \right|_{x+\theta_++h,y} + \left.\frac{\partial^4u}{\partial x^4} \right|_{x+\theta_-+h,y} \right) 
        $$
        $ h = \Delta x$ として
        $$
        \begin{aligned}
          \frac{u(x+\Delta x, y) + u(x-\Delta x, y) - 2u(x, y)}{\Delta x^2} - \frac{\partial^2u}{\partial x^2}  &= \frac {\Delta x^2}{24}\left( \left.\frac{\partial^4u}{\partial x^4} \right|_{x+\theta_++\Delta x,y} + \left.\frac{\partial^4u}{\partial x^4} \right|_{x+\theta_-+\Delta x,y} \right) \\
          &< \frac{M}{12}\Delta x^2
          \end{aligned}
        $$
        同様にして
        $$
           \frac{u(x, y+\Delta y) + u(x, y-\Delta y) - 2u(x, y)}{\Delta y^2} - \frac{\partial^2u}{\partial y^2} < \frac{M}{12}\Delta y^2
        $$
        よって、
        $$
          |\boldsymbol{b} - A \boldsymbol{u}^*| < \frac M{12} (\Delta x^2 + \Delta y^2)
        $$

    4. $\phi(x,y) = C_0 - \frac{(x^2+y^2)}4$を考える\
      $C_0$を重分のおきくして、$\partial \Omega$上で$\phi \geq 1 \Rightarrow{v_i} \geq 1$とする\
      この$\phi$をサンプリングして$v_i$を作る
      $$
      \begin{aligned}
      4v[i_x, i_y]-v[i_x+1, i_y] -v[i_x-1, i_y]-v[i_x, i_y+1] -v[i_x, i_y-1]=h^2
      \end{aligned} 
      \tag{3}
      $$
    5. $z_i = Cv_i \pm u_i$を考える
        $$
        \begin{aligned}
          &4z_i[i_x, i_y] -z_i[i_x+1, i_y]-z_i[i_x-1, i_y]-z_i[i_x, i_y-1]-z_i[i_x, i_y+1]  \\
          &= ch^2 \pm f_ih^2 \ (\because 式(2), 式(3))\\  
          &= h^2(c\pm f_i) 
        \end{aligned}
        $$
        $\lambda \in \partial \Omega$の場合
        $$
        Cv_i \pm u_i \leq C- \max_{}(|u_i|) \geq 0 \\
        \therefore 4z_i[i_x, i_y] -z_i[i_x+1, i_y]-z_i[i_x-1, i_y]-z_i[i_x, i_y-1]-z_i[i_x, i_y+1] \geq 0
        $$
        まとめて、
        $i \in \Omega , \partial \Omega$ でも
        $$
          z_i \geq 0 \Rightarrow u_i \leq Cv_i,  \ -u_i \leq Cv_i \Rightarrow|u_i| \leq Cv_i
        $$
        よって、$d = \max(|\phi|) = \max(|v_i|)$とすれば、安定性が示せた
      
    

### A,B  -> 収束性
主張
uは$C^4$級と仮定する。この時
$$
\max_{i \in 内部}　\{|u_i^*-u_i\} \leq ch^2M
$$

1. $e_i := u_i^* - u_i
$とする
    $$
    \begin{aligned}
    &4e(i_x, i_y) -e(i_x+1, i_y)-e(i_x-1, i_y)-e(i_x, i_y+1)-e(i_x, i_y-1) \\
    &= 4u^*(i_x, i_y) -u^*(i_x+1, i_y)-u^*(i_x-1, i_y)-u^*(i_x, i_y+1)-u^*(i_x, i_y-1) \\ 
    & \  \ - (4u(i_x, i_y) -u(i_x+1, i_y)-u(i_x-1, i_y)-u(i_x, i_y+1)-u(i_x, i_y-1)) \\
    &= 4u^*(i_x, i_y) -u^*(i_x+1, i_y)-u^*(i_x-1, i_y)-u^*(i_x, i_y+1)-u^*(i_x, i_y-1) \\
    &  \  \ - h^2f(i_x, i_y) \\
    &:= r^*(i_x, i_y)と書く
    \end{aligned}
    $$
    右辺の$f\Rightarrow r^*$とした$h^2f(i_x, i_y)$の解は$e_i$である\
    境界上では$u_i^*=u_i$と固定しているので$e_i=0$

2. $r^*$は要するに残差\
  適合性から、$|r^*_i| \leq \frac{Mh^2}{6}$となるMが存在する
3. 安定性から
    $$
      \exists d, |e_i|\leq d \max_{i \in 内部} {|r^*_i|}
    $$
4. 
    $$
      \max_{i \in 内部}\{|u_i^* - u_i| \} = \max_{i \in 内部}\{|e_i|\} \leq d \max_{i \in 内部} {|r^*_i|} \leq \frac {d}{6}Mh^2
    $$
    $c= \frac {d}{6}M $とすれば、収束性が成り立つ
