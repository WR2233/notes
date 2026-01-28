
## 高速フーリエ変換
- 離散フーリエ変換(DFT)\
  データ$f_0, f_1, \dots, f_{N-1} \in\mathbf{C}$\
  DFTを以下で定義
  $$
    c_K = \sum_{j=0}^{N-1}f_j e^{-2\pi ijK/N} \ (K= 0, 1, 2, \dots, N-1)
  $$
  (一点あたりの計算コストはN)\
  $\{f_j\} \rightarrow \{c_K\}$はコスト$O(N^2)$ 

  - 備考：逆フーリエ変換
  $$
     f_j = \frac 1 N \sum_{K=0}^{N-1}c_K e^{2\pi ijK/N} 
  $$
- 高速フーリエ変換　\
  離散フーリエ変換を$O(N \log N)$で行うアルゴリズム 
  - フーリエ変換の分解\
    $N = L \times M$ 点 DFT を因数分解
    $$
     c_K = \sum_{j=0}^{N-1}f_j e^{-2\pi ijK/N} 
    $$
    - $j = pL+q$とする\
       p = 0, 1, ..., M-1 商\
       q = 0 ,1, ..., L-1 余り
    - $K = rM+s$とする\
       r = 0, 1, ..., L-1 商\
       s = 0 ,1, ..., M-1 余り
    $$
    \begin{aligned}
    C_K &= C_{rM+s}\\
    &= \sum_{q=0}^{L-1} e^{-2\pi iqr/L} \times  e^{-2\pi iqs/N} \sum_{p=0}^{M-1} f_{pL+q} e^{-2\pi ps/M}
    \end{aligned}
    $$
    ここで以下はM点DFTの形だから、文字でおく
    $$
    C_{qis} = \sum_{p=0}^{M-1} f_{pL+q} e^{-2\pi ps/M}
    $$

  - 以下3ステップの計算に分解する
    1. M点DFT $\times$ Lセット $\Rightarrow C_{qis}$を計算  計算コストは、$O(M^2L) = O(MN)$
    2. $C_{qis} \times e^{-2\pi iqs/ N}$ 計算コストは、$O(N)$
    3. ステップ2は、L点データ$\times$ Mセット\
      これに対して、L点DFTをMセット実行 計算コストは、$O(L^2M) = O(NL)$

    まとめると、$O(N(L+M))$\
    例: $L, M \approx \sqrt{N}$の時、$O(N\sqrt N)$

  - 因数分解を繰り返す\
    $N = L_1\times L_2\times \dots \times L_n $と因数分解\
    先ほどの分解を繰り返して計算コスト$O(N(L_1+ \dots + L_n))$\
    例: $L_1 = L_2 = \dots = 2$なら、$O(N\log_2N)$

- プログラム実装\
  データの個数と「基数」\
  実用プログラムでは小さい基数(2,3,5)しか用いない

  - Nが2のべき乗のケース \
    $L = 2, M = N/2$
      $$
      C_{qis} = \sum_{p=0}^{M-1} f_{2p+q} e^{-2\pi ips/M}
      $$
      $$
      \begin{aligned}
      C_K &= C_{rM+s}\\
      C_s &= C_{0;s} + ( e^{-2\pi is/NC_{i;s}}) \\
      C_{M+s} &= C_{0;s} - ( e^{-2\pi is/NC_{i;s}}) 
      \end{aligned} 
      \tag{1}
      $$

    - データ構造\
      作業領域を使わない
      式(1)は、sについて独立だから、\
      あるsについて\
      $C_{o;s}, C_{1;s} \Rightarrow C_s, C_{M+s}$を実行すれば他では、$C_{o;s}, C_{1;s}$は不要　(上書きできる)

      出力側で、\
      $c_0, c_1, \dots, c_{m+0}, c_{m+1},c _{m+2}, \dots$と並べるには、\
      $c_{0;0}, c_{0;1}, c_{0;2}, \dots, c_{1;0}, c_{1;1}, c_{1;2}, \dots$ と$C_{q;s}$を並べる必要がある。

      $$
        C_{q;s} = \sum_{p=0}^{M-1} f_{2p+q} e^{-2\pi ips/M}
      $$
      は、qについて独立\
      cをfの上書きで計算したいなら、
      $$
        f_0,f_2, f_4, \dots, f_{N-1}, f_1, f_3, f_5, \dots
      $$
      の順に事前に格納すれば良い

    - 多段に拡張して考察すると
      $$
        f_0,f_4, f_8, \dots,  f_2, f_6, f_10, \dots f_1, f_5, f_9, \dots f_3, f_7, f_11, \dots
      $$
      一段進むたびに、\
        前の並びの連続ブロックを2飛びで分割して(余り先に回転)、それを並べ替える(余り後で回転)
    
      - 例: 8点DFTのケース\
      $f_0, f_4, f_2, f_6, f_1, f_5, f_3, f_7$の順 すなわち、\
      $f_{000}, f_{100}, f_{010}, f_{110}, f_{001}, f_{101}, f_{011}, f_{111}$ (ビット逆順)

## 数列の加速
収束が遅い数列から収束が早い数列を作る

## Richardson加速
$$
  a_n = a + c_1\lambda_1^n + c_2 \lambda_2^n + L(n) \\
  1 > |\lambda_1| > |\lambda_2| 
\tag{1}
$$

$\lambda_1$が既知ならば、$式(1)\times \lambda_1$より
$$
  \lambda_1 a_{n-1} = \lambda_1 a + c_1\lambda_1^n + c_2 \lambda_1 \lambda_2^{n-1} +\lambda_1  L(n-1)
  \tag{2}
$$
式(1)-式(2)より、
$$
    a_n - \lambda_1 a_{n-1} = a - \lambda_1 a + c_2  \lambda_2^n  -  c_2 \lambda_1 \lambda_2^{n-1} + L(n)- \lambda_1  L(n-1)
    \tag{3}
$$
すなわち、
$$
   \frac {a_n - \lambda_1 a_{n-1}}{1-\lambda_1} = a + \frac{1- \frac{\lambda_1}{\lambda_2}}{1-\lambda_1}c_2\lambda_2^n + \frac 1 {1-\lambda_1}(L(n)- L(n-1))
    \tag{3}
$$
数列$ \frac {a_n - \lambda_1 a_{n-1}}{1-\lambda_1}$は、$|\lambda_1| > |\lambda_2| $より、数列$a_n$より早く収束する\

## AitKen加速
$\lambda_1$が未知の場合, $\lambda_1$を推測する
$$
a_n - a_{n-1} = c_1 (\lambda_1 -1)\lambda_1^{n-1} + c_2(\lambda_2 -1)\lambda_2^{n-1} + L(n) - L(n-1)
$$
項$c_1 (\lambda_1 -1)\lambda_1^{n-1}$が隣接する項との主要な差であるから、以下のように$\lambda_1$を推測する
$$
\lambda_1 = \frac {a_n- a_{n-1}}{a_{n-1} -a_{n-2}}
\tag{4}
$$

これを使ってRichardson加速を行う\
新しい数列を$a_n^{(1)}$として、
$$
\begin{aligned}
  a_n^{(1)} &= \frac {a_n^{(n)} - \lambda_1 a_{n-1}}{1- \lambda_1}\\
  &= a_n + \frac{\lambda_1(a_n-a_{n-1})} {1- \lambda_1}
\end{aligned}
$$
式(4)を代入して
$$
\begin{aligned}
  a_n^{(1)} &= a_n + \frac{\frac {a_n- a_{n-1}}{a_{n-1} -a_{n-2}}(a_n-a_{n-1})} {1- \frac {a_n- a_{n-1}}{a_{n-1} -a_{n-2}}} \\
  &= a_n - \frac{(a_n - a_{n-1})^2}{a_{n-2} - 2a_{n-1}- a_{n}}
\end{aligned}

\tag{5}
$$
式５をAitKen加速の式とする
- 注意:
  - 前提が成り立っていないと意味がない
  - 特に他段階に加速すると、誤った結果につながる

- 応用例
  - Romberg積分
  $$
  T = \int_a^b f(x) dx
  $$
  の区間(a, b)に幅hの格子点をおく
  $$
    x_i = a + ih \\
    a = x_0, b= x_{N-1}
  $$
  台形公式での近似値
  $$
  T(h) = h \left( \frac 1 2 f(x_0) + f(x_1) + f(x_2)+\dots +  f(x_{N-2}) + \frac 1 2 f(x_{N-1})\right)
  $$
  オイラーマクローリンの公式より、
  $$
  T(h) - \int_a^b f(x) dx \sim \sum_{r=1}^\infty (-1)^{r-1}\frac {Br}{(2r)!}h^{2r}(f^{(2r-1)}(b)- f^{(2r-1)}(a))
  $$

  