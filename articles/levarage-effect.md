---
title: "レバレッジエフェクトの計測"
emoji: "💨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Julia, Finance]
published: True
---


この記事では、レバレッジエフェクト (leverage effect, LE) という金融市場で観測される経験則 (stylized fact) を紹介しよう。LEとは、過去に発生した負（正）の価格変動が将来のボラティリティを上昇（下降）させる現象を指す。この現象を定量的に定義するために、時刻$t$に発生した価格変動を$r(t)$、ボラティリティを$\sigma(t):=r^2(t)$と定義しよう。LEとは、過去に発生した価格変動と将来のボラティリティ間に現れる非線形関係のことを指す:

$$
\begin{align*}
L(\tau) :&=\langle r(t) \sigma(t+\tau)\rangle\\
         &= \frac{1}{T}\sum_{t}r(t)\sigma(t+\tau)\\
         &\propto -e^{-\tau/\bar{\tau}}.
\end{align*}
$$

次に、実データを用いてこの現象を観測する方法について説明しよう。
```julia
using MarketData, Plots, DataFrames
#データの読み込み
Start = DateTime(1980,1,1)
End = DateTime(2020,1,1)
data = values(yahoo(:VZ, YahooOpt(period1 = Start,period2 = End)).Close)
#特徴量の計算
r = diff(log.(data))
σ = r.^2
#L(τ)の計算
save = zeros(Float64,200)
x = collect(1:200)
for i in x
    save[i] = mean((r[1:end-i]) .* (σ[i+1:end]) )
end
#無次元化する
save = save./mean(σ)^2
```

![](/images/levarage_f.png)