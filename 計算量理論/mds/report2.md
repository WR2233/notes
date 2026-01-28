
計算量理論レポート
- 05251031
- 和久田隆平

# (1) $SAT \leq_m^p 3SAT$を示せ
以下の2ステップでSATを3SATに帰着することができる
1. 入力の命題論理式$\phi$を連言標準形$\phi'$に変換する
2. 連言標準形$\phi'$を3CNF $\phi''$に変換する


## 1. 任意の命題論理式$\phi$は、連言標準形に変換できることを示す
1. $\phi$を否定標準形$\psi$に変換する\
    以下のように$\phi$中の部分式を変換することで$\psi$は得られる。
    - $A \Rightarrow B \rightarrow \ \neg A \lor B$
    - $\lnot (\lnot A) \ \rightarrow \ A$
    - $\neg (A \land B) \ \rightarrow \ \neg A \lor \neg B$
    - $\neg (A \lor B) \ \rightarrow \ \neg A \land \neg B$

    $\psi$中には、否定記号は原子論理式のみにかかり、$\Rightarrow$は使用されない。\
  こうして得られる$\psi$は$\phi$と論理的に等価である

2. 否定標準形$\psi$をTseitin変換に基づいてCNFに変換する\
  論理式の最も内側にある$\land, \lor$から順にあらたな変数$z$を導入して、ネストを解消していく。
    - $A\land B$を変換する時\
      $A\land B$を$z$に置き換えて、
      $(z \rightarrow A \land B) \land(A \land B \rightarrow z)$ すなわち、\
      節$Z= (\neg z \lor A) \land (\neg z \lor B) \land (\neg A \lor \neg B \lor z)$を生成する

    - $A\lor B$を変換する時\
      $A\lor B$を$z$に置き換えて、
      $(z \rightarrow A \lor B) \land(A \lor B \rightarrow z)$ すなわち、\
      節$Z = (\neg A \lor z) \land (\neg B \lor z) \land (\neg z \lor A \lor B)$を生成する
  
    最後に、$\psi$全体を置き換えた変数を$z_{root}$として、
    $$
      \phi' = z_{root} \land Z_1 \land Z_2 \dots 
    $$
    とする。
    こうして得られる論理式$\phi'$は連言標準形となっていて、$\psi$と充足可能性は等価である
## 2. 任意の連言標準形の命題論理式$\phi'$は、3CNFに変換できることを示す
$\phi'$中の各節$C$ついて、$C$に含まれるリテラル数に応じて場合分けして以下のように置き換える。
- リテラル数1の時\
  $C=l$ ($l$はリテラル)と表せて以下のように変換する
  $$
    C \rightarrow l \lor l\lor l
  $$
- リテラル数2の時\
  $C=l_1 \lor l_2$ ($l_i$はリテラル)と表せて以下のように変換する
  $$
    C \rightarrow l_1\lor l_2\lor l_2
  $$
- リテラル数3の時\
  変換する必要はない

- リテラル数が$k \geq 4$の時\
  $C=l_1 \lor l_2 \lor \dots \lor l_k$ ($l_i$はリテラル)と表せて、$k-3$個の新たな変数$y_i (1 \leq i \leq k-3$)を導入して以下のように変換する
  $$
  \begin{aligned}
    C \rightarrow 
    & (l_1 \lor l_2 \lor y_1) \land \\\\
    & (\neg y_1 \lor l_3 \lor y_2) \land \\\\
    & (\neg y_2 \lor l_4 \lor y_3) \land \\\\
    & \dots \\\\
    & (\neg y_{k-4} \lor l_{k-2} \lor y_{k-3}) \land \\\\
    & (\neg y_{k-3} \lor l_{k-1} \lor l_k)
  \end{aligned}
  $$

以上より、入力の命題論理式$\phi$は、それと充足可能性が等価な3CNF $\phi''$に多項式時間で変換することができる。\
よって、$SAT \leq_m^p 3SAT$である。

# 節数$m$, 変数の数$n$として変換の時間計算量を求めよ
節数が与えられていることから、入力は連言標準形に限られるとする。\
(1)のアルゴリズムのうちステップ2のみ考えればよく、各節に対して、節の長さは最大$n$であり時間計算量$O(n)$がかかるため、\
全体として、$O(mn)$


### 参考文献
Tseitin変換と、最初からCNFを作る考え方. https://qiita.com/jkr_2255/items/f750f00913d395da4905