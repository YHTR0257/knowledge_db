
# 今の状況

- SDカードの移行完了
- L12での構造最適化の実行
	- FCCの代表元素8種類を用いて作成したL12型金属間化合物の構造最適化を実行
	- Al, Au, Cu, Ir, Ni, Pb, Pd, Pt
- ELFの直接造形が面白いと思っている
	- 検討している最中だが、ここに問題がある
	- 密度法での実装がCOMSOLで行えるよう
- 球ー梁での梁径制御を実行
	- ELFの<111>での梁径の比を固定
- WCCMでの発表内容の整理
	- アブストはとりあえず書いている。
	- spreadsheetにまとめている [20260719\_WCCM発表資料構成 - Google スプレッドシート](https://docs.google.com/spreadsheets/d/10jBGSep81-xfJDY7ZBV4RJE70ratK9pe8jlKo58bpkA/edit?usp=sharing)
	- Section1 : 細田さんの原子模倣
    - Section2 : 自分の電子状態模倣

# 結果

## ELFグリッドデータのFEM化手法を整理
- 4つの手法（密度法、六面体メッシュ、二値化六面体、等値面抽出）を比較検討
- 球-梁モデルの座屈問題を解決する代替手法として有望
- [[20260105_comsol-hexamesh]] にCOMSOL実装方法を詳細記録
- PBCとの相性を考慮すると六面体メッシュが最有力候補

## 手法の選定方針
- **短期**: 六面体メッシュで実装（ライセンス不要、PBC対応）
- **中長期**: Optimization Module導入を検討（滑らかな形状、汎用性）

# 迷っていること
### Windowsの容量が限界を迎えている・データ蓄積の場所を必要としている
- 2TBのうち、鐘ヶ江さんのデータが1.2TBを占めている
- マザーボードのメモリ規格によって2TBに抑えられている
- 変更するには、マザーボードから交換が必要になる
### アブストの体裁について
- 引用とか、著者とか
	- †と\*の違いがよくわからない
	- Abstracts should briefly outline the main features, results, and conclusions of the work, as well as its overall significance, and should include relevant references. The abstract must be converted to **Portable Document Format (PDF)** before being submitted through the Conference website.
	- The abstract must be written in **English** using **Times New Roman** font. The body of the abstract should not exceed **300 words**.
	- Each abstract must include the **full name and complete address** of all author(s). In the case of joint authorship, the name of the author who will actually present the paper at the Congress should be indicated with an asterisk (\*).
	- Abstracts will only be accepted on the understanding that the work will be **presented in person** at the Congress.
	- For any further information, please contact the Secretariat:
	- CIMNE Congress Bureau  Building C1 – Office C4, Campus Norte UPC  C/ Gran Capità, S/N  08034 Barcelona, Spain  Tel: +34 93 405 46 96  
	- E-mail: WCCM-ECCOMAS2026_sec@cimne.upc.edu

### WCCMでの発表内容確認
- spreadsheetにまとめている [20260719\_WCCM発表資料構成 - Google スプレッドシート](https://docs.google.com/spreadsheets/d/10jBGSep81-xfJDY7ZBV4RJE70ratK9pe8jlKo58bpkA/edit?usp=sharing)
- 内容としてこれで大丈夫なのかをチェックしてほしい
### 球ー梁モデルの限界が見られ始めている
- 右上を目指そうと思うと、梁系を小さくするのがいい傾向は見られている
- しかしこれでは座屈が起きるため弾性定数の計算精度が著しく低い
	- 計算対象以外の応力が5%以下に収束しない
- そこで密度法モデルでの再現がいいと思っている
	- ELFの空間分布が異方性に影響する→それを再現すると異方性制御が行えるのでは？
	- 再現ができると、その分布を元にして3次元グリッドデータを生成して異方性制御を実装できる
	- 形状と異方性の関係性を解き明かすことにつながる
	- フォノン分布 or 固有値分布と応力分布の関係性から異方性に与える影響を可視化できるのではないか
### ELFの直接造形の方針を迷っている
- 密度法のモジュールがライセンスに含まれていない
	- Optimization Moduleの導入を検討するのがいいと思っている
	- ✅ 3次元グリッドデータ(x,y,z,value)を用いたジオメトリ作成
	- ✅ 滑らかな曲面の再現

### ELFの直接造形の方針について

| 方法 | メリット | デメリット | 推奨度 |
|------|----------|------------|--------|
| **密度法<br>(Density Model)** | ✅ 滑らかな形状<br>✅ 汎用性高い | ❌ Optimization Module必要<br>❌ PBC設定が複雑 | ⭐⭐⭐ |
| **六面体メッシュ<br>(Hexahedral Mesh)** | ✅ ライセンス不要<br>✅ PBC対応容易<br>✅ 実装単純 | ❌ 形状が階段状 | ⭐⭐⭐⭐<br>**最有力** |
| **二値化六面体<br>(Binary Voxel)** | ✅ 最も単純な実装 | ❌ 形状が粗い<br>❌ 応力集中大 | ⭐⭐<br>検証用 |
| **等値面抽出STL<br>(Marching Cubes)** | ✅ 滑らかな形状 | ❌ PBC対応不可 | ⭐<br>不適 |

**結論**: 六面体メッシュで実装（詳細は[[20260105_comsol-hexamesh]]）

### 決定事項

バーチマーナハンの状態方程式を用いて、L12型の格子定数を計測する
- E-Vグラフから、極小値となるVを求めて格子定数を計測
- 原子位置だけを緩和、体積は固定 ISIF 2
	- Aの格子定数 $\times$ 0.75+Bの格子定数$\times$ 0.25　を中心にして、+-30%ぐらいで近似
	- [jstage.jst.go.jp/article/jshpreview/30/3/30\_237/\_pdf](https://www.jstage.jst.go.jp/article/jshpreview/30/3/30_237/_pdf)
	- L12で見て、B2が必要か判断。

- どこまで行ったら座屈するのかを判定する
	- 構造として面白いところが
	- 梁の形を変えるのも一つの手段
	- 球ー梁モデルの限界をまとめる。どこまで制御できるのかを確認する
	- 一次梁と二次梁で、どの領域までできるのかというまとめる。