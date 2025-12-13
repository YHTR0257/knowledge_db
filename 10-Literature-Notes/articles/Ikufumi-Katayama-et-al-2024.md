
## Core Idea
{{core_concept_one_sentence}}
## Overview

**title** : Three-dimensional bonding anisotropy of bulk hexagonal metal titanium demonstrated by high harmonic generation

## Key points

- HHG : High Harmonic Generation 高次高調波発生 によってTiの異方性を評価することに成功した
- Ti単結晶のab平面とac平面でHHGスペクトルを比較した結果、ab平面では等方的、ac平面では異方的な分布を示した
- 入力波の強度(fluence)を高くすると、HHGスペクトルの形状が変化することを確認した
	- ab平面では、3rdも5thも同様の変化を示す
	- ac平面では、3rdと5thで異なる変化を示す
- fluenceを高める(強度を高める)と高次の非線形成分の寄与が増加することが示唆された


## Back grounds

- light-matter(軽い物質、主に気体を指す？)では、HHGの応答が盛んに研究され、そのメカニズムが明らかとなってきた
- しかし、Bulk metalについては電子や電場の影響によって、材料中でのHHGに必要な非線形応答性(nonlinear interactions)が弱くなってしまう。そのために分析ができないと考えられていた。
> the strong screening effect of the electric field is considered to weaken the nonlinear interactions necessary for HHG generation inside a material

- そこで、authorらはHHGが材料中で観測することができればそれによって電子の非線形応答性を確認することができると考えた。
### HHGとは
> HHG : High Harmonic Generation
- 高次高調波発生［high-harmonic generation］とは、high-harmonic generationの他、high-order harmonic generationもしくはHHGとも表現され、高強度（$>1014 \text{W/cm}^{-2}$）のフェムト秒レーザーパルスを気体に照射すると、波長変換によって高次の倍波（照射した光が持つ振動数の整数倍の振動数を持つ光）が発生する現象を指します。また、この際に発生する短波長の光を高次高調波と呼びます[高次高調波発生とは［high-harmonic generationとは］ by 東京大学石川研究室用語集](https://www.atto.t.u-tokyo.ac.jp/terminology/high-order-harmonic-generation)
- 高次高調波発生は非摂動論的に非線形な過程で、摂動論的高調波とは著しく異なる以下のような特徴があります。
	- 次数が上がっても強度が落ちないプラトー領域が長く続く
	- ある次数（カットオフ）で突然強度が落ち、それ以上の次数の高調波は実質発生しません。
- 高次高調波発生はアト秒レーザーパルスの発生原理であり、また、高調波スペクトルから、電子状態やダイナミクスについての情報を取得することができます。 ![[hhg-velocity-vs-dimention.png]]

## Purpose

- HHGがbulk metal中でも観測されることを確認する
	- atomic numberが低いため軽い金属
	- 一方、単結晶の異方性については考察が進んでいない
- HHGによって電子の3次元的非線形応答を確認する

- HHG挙動がわかると、金属の物理特性を理解し、電子状態（異方性を含む）を特徴付けるための重要な情報を提供できる

> If HHG in bulk metals could be observed as in two-dimensional metallic systems32,33, the nonlinear dynamics of electrons and electronic states could be analyzed, providing important information for understanding the physical properties of metals and characterizing the electronic states, including the anisotropy.
## Conclusion

ab平面とac平面でのHHGスペクトルをpolar plotにして比較した結果を以下に示す
- ab平面ではEBSD上では結晶方位が0001で均一になっている
	- ab平面では、HHGの分布も等方的
		- 3rdは真円、5thはわずかに凹な六角形の形状を示す
- ac平面では結晶方位が様々に分布している
	- ac平面では、HHGの分布も結晶方位に依存して異方的
		- 3rdも5thもくびれと突起を持った独特の形状を示す

fluence依存性について
- 入力波の強度を高くすると、HHGスペクトルの形状が変化することを確認した
	- ab平面では、3rdも5thも同様の変化を示す
	- ac平面では、3rdと5thで異なる変化を示す
- a軸に平行な信号比率のほうがc軸よりも大きな励起fluenceで高くなることを確認した
	- a軸でより顕著にfluence依存性が見られた
- fluenceを高める(強度を高める)と高次の非線形成分の寄与が増加することが示唆された
	- 3rdや5thだけでは説明できない変化が見られた
> The results demonstrated that the shapes of the polar plots varied for different excitation fluence for both the 3rd and 5th harmonics. The ratio of the signal at the polarization parallel to the a-axis to that parallel to the c-axis was higher at larger excitation fluence, indicating contributions from higher-order nonlinear components than the relevant harmonic order


![[ikufumi-hhg-analysis-fig2-a.png]]
弱いレーザー（低フルエンス）:電子は素直に揺さぶられます。3次高調波 ($3\omega$) は、理論通り純粋な「3乗のプロセス」だけで生まれます。
- 式イメージ: $Signal(3\omega) \approx A \times E^3$
強いレーザー（高フルエンス）:電子が激しく揺さぶられすぎて、動きが複雑になります。すると、本来は「5次高調波 ($5\omega$)」や「7次高調波」を作るはずだった高次のエネルギー（高次の非線形成分）の一部が、3次高調波の信号に「漏れ出し」てきます。
- 式イメージ: $Signal(3\omega) \approx (A \times E^3) + (\textbf{B} \times E^5 \text{由来の補正}) + \dots$

There were four and seven non-zero and independent tensor components for the 3rd and 5th order harmonics in Ti


intraband(バンド内)とinterband(バンド間)の両方の遷移がHHGに寄与していることを示した
- どちらかというとintrabandの寄与が大きい
> we showed that the intraband acceleration of conduction electrons induced by infrared pulses plays an important role in generating the HHG signal but with some contribution from interband transitions

intraband currentによる境界
光学異方性によるn次の信号強度$S_n$は以下の式で記述できる
$$S_n \propto \left| r_n \int dt \boldsymbol{J}(r_{\text{MIR}}\boldsymbol{A}_{\text{in}}(t)) e^{-in\omega_0 t} \right|^2 \quad$$

各項の意味
- $S_n$ (左辺):観測される $n$ 次高調波の信号強度（Signal intensity）
	- $|\dots|^2$ (絶対値の二乗):物理学の基本として、観測される光の強度は「振幅の二乗」に比例します。
- $\boldsymbol{A}_{\text{in}}(t)$:入射する中赤外（MIR）レーザーのベクトルポテンシャル（光の波形）
	- これは「金属の外（空気中）」にある元の光です。
- $r_{\text{MIR}}$ (入力の境界条件):「**入射光が金属の中に入るときに、どれくらい透過するか（あるいは反射の影響を受けるか）**」を表す係数です。
	- 本文に "input MIR pulse was reflected... due to the anisotropic reflectance" とある通り、Ti（チタン）は異方性があるため、偏光方向によって金属内に入る光の強さが変わります。それを補正しています。つまり、$r_{\text{MIR}}\boldsymbol{A}_{\text{in}}(t)$ は「金属内部に実際に侵入した有効な光の強さ」を表します。
- $\boldsymbol{J}(\dots)$:発生源となる電流（Current Density）。
	- 金属内の電子が、有効な光 ($r_{\text{MIR}}\boldsymbol{A}_{\text{in}}$) によって揺さぶられ、生成される非線形電流です。これが高調波の源（ソース）になります。
- $\int dt \dots e^{-in\omega_0 t}$:フーリエ変換です。
	- 時間変化する電流 $\boldsymbol{J}(t)$ から、周波数 $n\omega_0$ （つまり $n$ 次高調波の周波数）の成分だけを抜き出す計算操作です。
- $r_n$ (出力の境界条件 effective nonlinear susceptibility tensor):**「金属内部で発生した高調波が、外に出てくるときに受ける影響」** を表す係数です。
	- 本文にある "The 3rd and 5th order polarizations are also affected by similar reflectance" に対応します。発生した $n$ 次の光も、金属表面から出てくるときに一部反射されたり吸収されたりするため、その透過効率（フレネル係数的なもの）を掛けています。


intraband currentは結晶方位ごとに差がある事を確認
- 異方性の発見
	- 実験値と比較してもVASPでの再現性は高いことを確認
![[ikufumi-katayama-hhg-intraband-current.png]]
## Discussion
この記事を受けて自分にどのような影響をもたらすのか

ab平面とac平面でのHHGスペクトルのpolar plotより
- 3rdも5thも異なるfluence

fluence依存性
- 単位面積あたりに入射するエネルギー量の時間積分のこと
	- fluenceを高めると、高次の非線形成分の寄与が増加することが示唆された

なぜ高次の非線形成分の寄与が増加するのか
- fluenceを高める(強度を高める)と、電子が激しく揺さぶられすぎて、動きが複雑になる
- すると、本来は「5次高調波 ($5\omega$)」や「7次高調波」を作るはずだった高次のエネルギー（高次の非線形成分）の一部が、3次高調波の信号に「漏れ出し」てきます。
- つまり、弱いレーザー（低フルエンス）では、電子は素直に揺さぶられ、3次高調波 ($3\omega$) は、理論通り純粋な「3乗のプロセス」だけで生まれます。
	- 式イメージ: $Signal(3\omega) \approx A \times E^3$
- 一方、強いレーザー（高フルエンス）では、電子が激しく揺さぶられすぎて、動きが複雑になります。 すると、本来は「5次高調波 ($5\omega$)」や「7次高調波」を作るはずだった高次のエネルギー（高次の非線形成分）の一部が、3次高調波の信号に「漏れ出し」てきます。
	- 式イメージ: $Signal(3\omega) \approx (A \times E^3) + (\textbf{B} \times E^5 \text{由来の補正}) + \dots$
- **「3次のデータの中に、本来いるはずのない高次の幽霊が見えている」**状態であり、これはレーザーを強くしすぎたことによる物理的な混線（摂動論の破れ）が原因

## Methods

**MIR : Mid-Infrared Radiation pulse**を、Ti単結晶に照射し、発生するHHGを観測
- 4.8μm 波長、0.258eV
	- 周波数は、金属中の自由キャリアの減衰定数よりもわずかに高く設定し、金属材料におけるHHGの信号強度を高めた。
	> The frequency of the infrared pulses was chosen to be slightly higher than the damping constant of the free carriers to enhance the signal intensity of HHG in the metallic material.
- Ti単結晶 : hcp構造

1. フェムト秒レーザーを用いてMIRパルスを生成
2. MIRパルスをTi単結晶に照射
- 特定方向から入射させて、偏光角度を変化させた

### 評価手法

HHGスペクトルの偏光依存性をプロット
- 形状を用いた評価を行う。
- 極座標プロット(polar plot)により、偏光依存性を視覚的に評価した
	- θ軸: 偏光角度
	- r軸: HHG信号強度
- 信号強度自体は補正係数$r_n$を用いて補正した

#### 補正方法
透過係数(本文中では、reflectance terms)である$r_n$と$r_{MIR}$を用いて補正を行った
- $r_n = |\frac{2}{\tilde{n_n} + 1}|$
	- the complex linear refractive indices at the incident MIR frequency
	- 金属内部で発生した高調波が外へ出ていく時、「表面で反射されずにどれだけ外に出て行けるか」を表す
- $r_{MIR}=|\frac{2\tilde{n}_{MIR}}{\tilde{n}_{MIR} + 1}|$
	- 空気から金属へレーザーが入る時、「表面で反射されずにどれだけ中に入り込めるか」を表す


減衰定数 : damping constant
- 電子が金属の中で障害物（原子や他の電子）に衝突する頻度
- 物理学（特に金属の光応答を扱うドルーデモデル:Drude modelなど）において、自由キャリア（電子）は真空中を飛ぶのと違い、金属内部の原子の振動や不純物にぶつかりながら移動する。
	- 物理的意味: 電子の動きが「邪魔される強さ」や「抵抗」のこと
	- 時間の逆数: しばしば $\gamma$ （ガンマ）で表され、単位は周波数と同じ [Hz] や [rad/s] の次元を持つ。「1秒間に何回衝突してエネルギーを失うか（散乱レート）」と捉えるとわかりやすい。

The induced current: 誘起される電流
- HHGを構成しているのは、ほとんどがconduction band内での電流である。そのためバンド構造を解析することで、HHGの異方性を理解できると考えた。

bに示すのは、Tiのバンド構造である。
cはEBSDとそれに対応する単位格子の方向である
- 軸によって様々な結晶方位が現れる方向と単一方向しか現れない方向があることがわかる
![[ikufumi-katayama-hhg-fig1.png]]

- 3番目と5番目のバンドを用いて、分析を行った
	- 多分フェルミエネルギーをより多く通るバンドを選択したのだと思う
	- Tiでは、4つのバンドがフェルミ面を横切っている![[ikufumi-katayama-hhg-fig1-b.png]]
	- 高次での影響を見たかったが、7次を見ると複雑すぎるため、3番目と5番目のバンドに絞ったのかも？

### 実験結果の評価方法

the effective nonlinear susceptibility tensor(有効非線形感受率テンソル)を用いて、HHGの評価を行った。実験もシミュレーションも同様の評価を行った。
- 非線形感受率テンソルとは、材料が電場に対してどのように応答するかを記述する数学的な表現であり、特に高次高調波発生（HHG）などの非線形光学現象を理解するために重要です。
- 有効非線形感受率テンソルは、実際の材料の複雑な構造や特性を考慮に入れた上で、材料の非線形応答を簡略化して表現するために使用されます。
- これにより、材料の異方性（方向によって異なる特性）を評価し、HHGの発生メカニズムを理解するのに役立ちます。
	- 物質に強い光（電場）を当てたときに、物質内の電子が「入力した光の強さ（電場 $E$）に単純比例しない（＝非線形な）応答」をどの程度示すかを表す
	- 冪級数展開(摂動論)を用いて計算される

- 実験においては補正係数を用いて、観測された信号強度を補正した


> rn  j2=ðenn þ 1Þj and rMIR j2enMIR=ðenMIR þ 1Þj are the amplitude reflectance of the nth-order nonlinear polarization and the MIR pump pulses
> with enMIR and nen representing the complex linear refractive indices at the incident MIR frequency and at the frequency of the output nth-order nonlinear polarization,

### 実験方法について

1. 光を作る:
	- 800 nmのレーザーを、OPA（光パラメトリック増幅器）という装置を2台使って波長変換し、差周波発生（DFG）によって 0.258 eV（約4.8 $\mu$m）の中赤外パルス を作成。
2. 回す（Polar plot用）:The intensity and polarization... were tuned by changing the angles of two polarizers after a quarter waveplate.
	- ここで偏光板と波長板を回転させて、入射する光の偏光角度 $\theta$ を精密に制御しています。（なお、サンプルの方を回しても同じ結果が得られることも確認済みとのこと）。
3. 撮る:
	- 発生したHHGを分光器に入れ、カメラで撮影。
	- 3次高調波: InGaAsカメラ（赤外線に感度がある）を使用。
	- 5次高調波: シリコンCCDカメラ（可視光に近い領域に感度がある）を使用。
	- ここで得られたスペクトルを積分して、Polar plotの「点の距離」にしています。

以下の2つの装置を用いて、Tiの違法的な高額定数を測定

1. FTIR（フーリエ変換赤外分光光度計）:
	- 役割: 主に 中赤外（MIR）領域、つまり今回使ったレーザー（0.258 eV）付近の反射率を測定するために使用したと考えられます。
	- VERTEX-80v は真空中で測定できる高級機で、空気による赤外吸収の影響を除去して精密に測っています。
2. エリプソメーター（Ellipsometer）:
	- 役割: 主に 可視〜紫外領域、つまり発生したHHG（3次：0.77 eV、5次：1.29 eV）付近の複素屈折率（$n$ と $k$）を決定するために使用します。
	- 先ほど解説した通り、偏光状態の変化を見ることで、物質の光学定数を直接決定できる装置です。

A Ti:sapphire regenerative amplifier was employed with a center wavelength of 800 nm, an output power of 7 W and a repetition rate of 1 kHz. The output was used to pump two optical parametric amplifiers (OPAs), and the difference frequencies of the outputs of the OPAs were used to generate MIR pulses with an output power of 6 mW and photon energy of 0.258 eV. The MIR pulses were focused on the sample surface with reflective objective mirrors after dispersion compensation. The intensity and polarization of the pump pulse were tuned by changing the angles of two polarizers after a quarter waveplate. We also confirmed that similar results of polarization dependence were obtained by rotating the sample instead of changing the polarization of incident excitation pulses. The emitted 3rd and 5th harmonics were directed to a spectrometer equipped with a charge-coupled device (CCD) camera: a silicon CCD camera was used for the 5th harmonics, whereas an InGaAs camera was used for the 3rd harmonics. Wavelength and sensitivity corrections were applied for the spectrometer and camera system. The polarization dependence of the sensitivity is within 10% of the signal that do not significantly modify the results shown in this paper. The integrated intensities from spectra of the 3rd and 5th harmonics were plotted in polar plots. Sample A was prepared with the floating zone method and Sample B was annealed at 1000 °C for four weeks after melt-solidification process to ensure that the crystal domain size was larger than the spot size of the MIR pump pulses. The crystal orientation of each observed domain was analyzed using a scanning electron microscope (JSM-7100F, JEOL Corp.) equipped with an EBSD system (OIM, TSL Solutions Corp.). The 3 mm × 3 mm single crystalline domain with c axis 78° tilted from the surface normal (grown by the same method as that used for sample A) was used to investigate the linear anisotropy of the optical constants. The obtained data are presented in the Supplementary Fig. 1. Anisotropic reflectance was evaluated using a Fourier transform infrared spectrometer (VERTEX-80v, Bruker Corp.) equipped with a microscope (Hyperion, Bruker Corp.) and ellipsometer (SENresearch Family SE 850 DUV, SENTECH GmbH).

### シミュレーション方法について

hcp Tiのバンド構造をab initio density-functional theory (DFT)を用いて計算した。

- PAW法: projector-augmented wave method
- VASP: Vienna ab initio simulation package
- plane wave cut-off energy: 500 eV
- kpoints : Γ-centered 20 × 20 × 16 k points with equal separation

fitting
- 各バンドをhexagonal symmetryを持つcosinusoidal expansionにフィッティングした
- フィッティングでは、Tiの格子定数aの7倍以内に原子の位置があるベクトル成分のみを考慮した

- 参照式
$$E_n(\boldsymbol{k}) = \sum_{i} C_i \cos(\boldsymbol{G}_i \cdot \boldsymbol{k})$$

> Simulation of HHG in Ti  The band structure of hexagonal close-packed (hcp) Ti (Figs. 1b and 3) was calculated from ab initio density-functional theory (DFT) with a standard projector-augmented wave (PAW) method using the Vienna ab initio simulation package (VASP)44,45. Plane waves were used up to a cut-off kinetic energy of 500 eV46. A unit cell of the hexagonal reciprocal momentum space was divided into Γ-centered 20 × 20 × 16 k points with equal separation. Each band was fitted to a cosinusoidal expansion with hexagonal symmetry using Eq. (1). In this fitting, we only considered vector components where the positions of the atoms were within 7 times the lattice constant a of Ti. 

HHGを計算するために
1. Fermi SeaをBrillouin zone内で$\delta K$だけシフト
2. 誘起される電流を計算
3. 実験で測定された補正係数を用いて、観測される信号強度を補正
4. 各$\Delta K$値と方向について3次元のBrillouin zone全体で積分した。

MIR pump pulseのモデリング
1. Gaussian-sinusoidal function(ガウス-正弦波関数)を100fsでモデル化
2. 誘起される電流は、次式の推定される波数ベクトルシフトを考慮して推定した
	$$\Delta K (t) = \frac{e}{\hbar} A_{\text{MIR}}(t) \quad$$
3. 誘起電流の計算された波形をフーリエ変換し、周波数関数としてプロット
4. 3rdおよび5thの応答の強度をスペクトルから抽出し、偏光依存性を決定した

注意点
- 電子分布関数の考慮はしていない
	- k-spaceにおける電子温度と化学ポテンシャルの変化を無視した
	- つまり、絶対零度でかつ平衡状態にあると仮定した
- 反射損失は次式を用いて計算した
- $$S_n \propto \left| r_n \int dt \boldsymbol{J}(r_{\text{MIR}}\boldsymbol{A}_{\text{in}}(t)) e^{-in\omega_0 t} \right|^2 \quad$$

> To calculate the HHG, the Fermi Sea was shifted by an amount ΔK in the Brillouin zone and the induced current was calculated and integrated in the whole three-dimensional Brillouin zone for each ΔK value and direction. We first calculated the ΔK dependence of the induced current and used these data for simulating the HHG. The MIR pump pulses were modeled with a Gaussian-sinusoidal function with a pulse duration of 100 fs and the induced current was estimated by considering the estimated wavevector shift of Eq. (2). The calculated waveforms of the induced current were Fourier transformed and plotted as a function of frequency. The intensities of the 3rd and 5th order responses were extracted from the spectra to determine the polarization dependence. We ignored changes of the electron distribution function (electronic temperature and chemical potential) in the k-space for simplicity. The reflection losses of the induced nonlinear polarization were calculated using Eq. (4).

### なぜ補正を実験値で行ったのか？

**VASPだけで補正係数（屈折率や反射率）を計算すること自体は可能です。**

VASPには「線形応答（Linear Optics）」を計算する機能があり、これを使えば理論上の反射率を出すことができます。

しかし、この論文の筆者たちがVASPだけに頼らず、わざわざ **実測値（実験データ）** を使ったのには、 **「VASPの計算だけでは超えられない3つの壁（精度の限界）」** があるからです。

---

### 1. VASPでやる方法（理論的にはこうやります）

VASPで `LOPTICS = .TRUE.` というタグなどを設定すると、 **周波数依存の誘電関数 $\epsilon(\omega)$** を計算してくれます。これが出れば、あとは高校物理＋αの計算で屈折率と反射率が出ます。

1.  **VASPが出力:** 誘電関数 $\epsilon(\omega) = \epsilon_1(\omega) + i\epsilon_2(\omega)$
2.  **変換:** これを複素屈折率 $\tilde{n} = n + ik$ に変換。
3.  **反射率:** フレネルの式で反射率 $R$ を計算。

これで「理論上の補正係数」は手に入ります。

### 2. なぜVASPだけではダメだったのか？（3つの壁）

それでも筆者たちが実験値を使ったのは、以下の理由で**VASPの計算結果が現実とズレるリスクが高いから**です。

#### ① 表面の「汚れ」や「酸化」を再現できない
VASPで計算するのは、真空中にある**「完璧で綺麗なチタンの結晶」**です。
しかし、実際の実験室にあるチタンサンプルの表面には、数ナノメートルの**酸化膜（酸化チタン）**があったり、研磨による微細な**凹凸**があったりします。
* **VASP:** 完璧な鏡のような反射率を出す。
* **現実:** 酸化膜で少し吸収されたり、散乱されたりする。

この論文のように「微妙な異方性」を議論したい場合、この「現実の汚れ」によるズレが命取りになります。

#### ② DFT計算特有の「バンドギャップ誤差」
VASP（DFT計算）は、一般的に**バンドギャップを過小評価する**という弱点があります（実際の値より小さく出る）。
バンドギャップがズレると、「どの波長の光を吸収するか」というピークの位置もズレてしまいます。
* 特にHHG（高調波）の領域（可視～紫外）では、バンド間遷移（電子のジャンプ）が重要になるため、このズレが致命的になります。

#### ③ 金属特有の「ドルーデ項（自由電子）」の調整
中赤外（MIR）領域では、金属の反射率は**自由電子の動き（ドルーデ項）**で決まります。
- VASPでもこれを計算できますが、電子が不純物にぶつかる頻度（散乱レート）などのパラメータは、結局実験に合わせて手動で調整しなければならないことが多いです。
- 「どうせ実験に合わせて調整するなら、最初から実験値を使ったほうが正確だ」という判断になります。

* **なぜやらなかったか？:**
    **「表面の酸化状態」や「厳密なエネルギー位置」を完璧に合わせるため**です。論文として「これは計算の誤差ではなく、本当に物理現象だ」と言い切るためには、不確定要素の多い計算値よりも、実測値を使うほうが説得力があるからです。

## Reference

- Author : Ikufumi Katayama 1,2 , Kento Uchida3, Kimika Takashina1, Akari Kishioka1, Misa Kaiho1, Satoshi Kusaba 1, Ryo Tamaki1, Ken-ichi Shudo1, Masahiro Kitajima1,4, Thien Duc Ngo 5, Tadaaki Nagao 5,6, Jun Takeda 1,2, Koichiro Tanaka 3 & Tetsuya Matsunaga
- Journal : communications physics
- DOI : https://doi.org/10.1038/s42005-024-01906-0

