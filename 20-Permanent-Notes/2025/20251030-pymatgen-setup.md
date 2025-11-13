---
id: 202510301
created: 2025-10-30
updated: 2025-10-30
tags: [permanent, python, pymatgen, vasp, setup, simulation]
type: permanent-note
status: active
confidence: high
source_type: knowledge
---

# Keypoints
- pymatgenはPythonベースの材料科学ライブラリで、VASPの入力ファイル生成に便利
- VASPのPOTCARファイルはライセンスの関係で配布できないため、ユーザーが公式サイトからダウンロードする必要がある
- pymatgenの設定ファイルにPOTCARファイルのパスを登録することで、自動的にPOTCARを利用可能にできる
- pymatgenのMPRelaxSetを使うと、Alの構造緩和計算のVASP入力ファイルを簡単に生成できる

# Introduction

- このノートでは、Pythonの材料科学ライブラリであるpymatgenを使用して、VASPのPOTCARファイルを設定し、Al（アルミニウム）の構造緩和計算に必要なVASP入力ファイルを自動生成する方法を解説します。
- 最終的に、pymatgenのMPRelaxSetを利用して、Alの構造緩和計算に必要なVASP入力ファイル一式（INCAR, POSCAR, KPOINTS, POTCAR）を生成するPythonスクリプトを作成します。

# What is pymatgen?

- pymatgen (Python Materials Genomics)は、材料科学のための強力なPythonライブラリであり、結晶構造の操作、材料データの解析、VASPなどの第一原理計算ソフトウェアの入力ファイル生成に広く利用されています。
- pymatgenは、Materials Projectという大規模な材料データベースのバックエンドとしても使用されており、信頼性の高い計算パラメータセットを提供しています。
- pymatgenを使用することで、VASPの入力ファイルを手動で作成する手間を大幅に削減でき、計算の自動化やワークフローの構築が容易になります。

# 前提条件

- VSCodeやPyCharmなどのPython対応IDEがインストールされていること
- Python 3.8以降がインストールされていること
- pipやcondaなどのパッケージマネージャーがインストールされていること

## 前提の確認

```bash
python --version
# Python 3.8.x 以降が表示されることを確認   
```

以下はどちらかがインストールされていればOKです。自分が使用しているマネージャーに合わせましょう。
```bash
pip --version
# pipのバージョンが表示されることを確認
conda --version
# condaのバージョンが表示されることを確認
```

# 1. pymatgenのインストール

以下の手順でpymatgenをインストールします。

## 1. pipを使用したインストール

```bash
pip install pymatgen
```

## 2. condaを使用したインストール

```bash
conda install -c conda-forge pymatgen
```

# 2. POTCARファイルの取得

VASPのPOTCARファイルは、VASPのライセンスを持っているユーザーのみがアクセスできます。公式のページからダウンロードしてください。

公式ページ: [VASP公式サイト](https://www.vasp.at/)

自分が使用するポテンシャルを選択し、POTCARファイルをダウンロードします。

例：
- `potpaw_PBE.64.tgz` (PBEポテンシャル、2025年現在の最新バージョン)
- `potpaw_PBE.54.tgz` (PBEポテンシャル、古いバージョン)

# 3. POTCARファイルの配置

自身がわかりやすい場所にPOTCARファイルを配置します。例えば、以下のようなディレクトリ構造にします。この時、pymatgenが指定するフォルダ名を使用します。これは、pymatgenが認識できるようにするためです。フォルダ名は、

```
~/vasp_potentials/
├── potpaw_PBE.64/
│   ├── POTCAR
│   ├── ...
├── potpaw_PBE.54/
```

# 4. pymatgenにPOTCARの場所を認識させる

pymatgenがVASPのPOTCARファイルを自動的に見つけられるように、設定を行う必要があります。これは`pmg`というコマンドラインツールを使って簡単に行えます。

## 4.1. POTCARファイルの展開

まず、VASP公式サイトからダウンロードしたPOTCARのアーカイブファイル（例：`potpaw_PBE.64.tgz`）を、任意の場所に展開します。

```bash
# 例：ホームディレクトリにvasp_potentialsというフォルダを作成
mkdir ~/vasp_potentials
cd ~/vasp_potentials

# ダウンロードしたファイルをここに移動し、展開
# tar -zxvf /path/to/your/potpaw_PBE.54.tgz
tar -zxvf potpaw_PBE.54.tgz
```

これにより、`~/vasp_potentials/potpaw_PBE.54` のようなディレクトリが作成されます。

## 4.2. pymatgenの設定ファイル更新

次に、`pmg config`コマンドを使い、pymatgenにPOTCARファイルの場所を教えます。

```bash
pmg config --add PMG_VASP_PSP_DIR ~/vasp_potentials/potpaw_PBE.64
```

このコマンドは、ホームディレクトリにあるpymatgenの設定ファイル `~/.pmgrc.yaml` を作成または更新します。ファイルの中身は以下のようになります。

```yaml
# ~/.pmgrc.yaml
PMG_VASP_PSP_DIR: /Users/your_username/vasp_potentials/potpaw_PBE.64
```

これで、pymatgenのスクリプトからPOTCARファイルを自動で利用する準備が整いました。

### 4.3. デフォルトのVASPポテンシャルを設定

さらに、デフォルトで使用するVASPポテンシャルのバージョンとタイプを設定できます。これにより、スクリプト内で明示的に指定しない限り、常にこの設定が使用されます。

```bash
pmg config --add PMG_DEFAULT_FUNCTIONAL PBE_64
```

> **（参考）公式ドキュメントの引用**
> For the code to generate POTCAR files, it needs to know where the VASP pseudopotential files are. We are not allowed to distribute these under the VASP license. The good news is that the pmg command line utility includes a config functionality.
>
> After the <MY_PSP> directory is generated, you should add it to your Pymatgen configuration file as follows:
>
> `pmg config --add PMG_VASP_PSP_DIR <MY_PSP>`
>
> In practice, this entire process might look something like the following:
>
> ```bash
> pmg config -p /path/to/pseudos/potcar_PBE.54 /path/to/pseudos/pmg_potcars
> pmg config -p /path/to/pseudos/potcar_LDA.54 /path/to/pseudos/pmg_potcars
> pmg config --add PMG_VASP_PSP_DIR /path/to/pseudos/pmg_potcars
> ```
>
> If desired, you may specify a default version and type of pseudopotentials as follows:
>
> `pmg config --add PMG_DEFAULT_FUNCTIONAL PBE_52`
>
> For additional options, run the help command to see the full list of choices.

---

# 5. 実践: Alの構造緩和計算の入力ファイルを生成する

ここまでの設定を活かして、pymatgenでアルミニウム（Al）の構造緩和計算に必要なVASP入力ファイル一式（`INCAR`, `POSCAR`, `KPOINTS`, `POTCAR`）を自動生成するPythonスクriptを作成します。

## 5.1. Pythonスクリプトの作成

以下の内容を `create_al_relaxation.py` という名前で保存してください。

```python
from pymatgen.core import Structure
from pymatgen.io.vasp.sets import MPRelaxSet

# 1. Alの結晶構造 (fcc) をプログラムで定義
# Materials Projectから取得したり、CIFファイルから読み込むことも可能です
# 例: structure = Structure.from_file("Al.cif")
al_fcc = Structure.from_spacegroup(
    "Fm-3m",  # fccの空間群
    lattice=[[4.05, 0, 0], [0, 4.05, 0], [0, 0, 4.05]], # 格子ベクトル
    species=["Al"], # 原子種
    coords=[[0, 0, 0]] # 原子の座標
)

# 2. VASP入力ファイルセットを生成
# MPRelaxSetは、Materials Projectで標準的に使われている構造緩和計算用の推奨パラメータセットです。
# これを使うことで、INCARやKPOINTSのパラメータを自分で細かく設定する手間が省けます。
vasp_input_set = MPRelaxSet(al_fcc)

# 3. "vasp_al_relaxation" という名前のディレクトリに入力ファイルを書き出す
output_dir = "vasp_al_relaxation"
vasp_input_set.write_input(output_dir)

print(f"VASP input files for Al relaxation have been generated in the '{output_dir}' directory.")
print("\nGenerated files:")
print(f"- {output_dir}/INCAR")
print(f"- {output_dir}/POSCAR")
print(f"- {output_dir}/KPOINTS")
print(f"- {output_dir}/POTCAR")

# 生成されたINCARの内容を確認
print("\n--- INCAR content ---")
with open(f"{output_dir}/INCAR", "r") as f:
    print(f.read())
```

## 5.2. スクリプトの解説

- **`Structure.from_spacegroup(...)`**: 空間群の対称性を利用して結晶構造を簡単に定義しています。Alはfcc構造なので、空間群`Fm-3m`を指定しています。CIFファイルやPOSCARファイルから構造を読み込むこともできます。
- **`MPRelaxSet(al_fcc)`**: これがpymatgenの強力な機能の一つです。`MPRelaxSet`は、Materials Projectのデータベース構築に使われている、信頼性の高い計算パラメータ（INCARタグやKPOINTSのメッシュ密度など）を自動で設定してくれます。これにより、VASPに慣れていないユーザーでも質の高い入力ファイルを簡単に準備できます。
- **`vasp_input_set.write_input(output_dir)`**: この一行で、設定されたパラメータに基づいて`INCAR`, `POSCAR`, `KPOINTS`, `POTCAR`の4つのファイルが指定したディレクトリに書き出されます。`POTCAR`は、ステップ4で設定したパスから、`POSCAR`に含まれる元素（今回はAl）に対応するものが自動的に選ばれてコピーされます。

## 5.3. スクリプトの実行と結果

ターミナルで以下のコマンドを実行します。

```bash
python create_al_relaxation.py
```

実行すると、カレントディレクトリに`vasp_al_relaxation`というフォルダが作成され、その中にVASPの計算に必要な4つのファイルが生成されます。

```bash
$ ls vasp_al_relaxation/
>> INCAR KPOINTS POSCAR POTCAR
```

これで、`vasp_al_relaxation`ディレクトリに移動してVASPを実行すれば、すぐに計算を開始できます。ユーザーが準備したのはPythonスクリプト数行のみで、VASPの入力ファイルをすべて自動生成できました。

# 6. できること

- pymatgenを使うことで、VASPの入力ファイル生成が大幅に簡素化される
- 計算する系に合わせて、kpointsやincarのパラメータを自動調整
- 格子に変形を与える
- 欠陥を導入する
- 表面構造を作成する

などなど、多様な第一原理計算を用いた材料シミュレーションの準備が効率的に行える。

# 参考文献
- [pymatgen公式ドキュメント POTCAR setup](https://pymatgen.org/installation.html#potcar-setup)
- [pymatgen公式ドキュメント - VASPポテンシャルの設定](https://pymatgen.org/usage/vasp.html#setting-up-vasp-potentials)
- [VASPポテンシャルの設定 - wak-tech](http://wak-tech.com/archives/670)
- [pymatgen GitHubリポジトリ](http://pymatgen.org/_modules/pymatgen/io/vasp/inputs.html)
