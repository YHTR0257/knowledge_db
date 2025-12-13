
WSL（Ubuntu等）での uv のセットアップ手順と、機械学習プロジェクトの始め方を解説します。

### 1. uv のインストール

WSLのターミナルを開き、以下のコマンドを1行実行してください。
（公式のインストーラーです。Rust製の単一バイナリがダウンロードされるだけなので、システムを汚しません）
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```
### 2. パスの反映
インストールが終わると「再起動するか、以下のコマンドを実行してください」と表示されます。手っ取り早く反映させるために、以下のコマンドを実行してください。
```bash
source $HOME/.local/bin/env
```

※もしうまくいかない場合は、一度ターミナルを閉じて開き直せばOKです。
確認のためにバージョンを表示してみましょう。
```bash
uv --version
# 表示例: uv 0.5.x (またはそれ以降)
```
---

### 3. 実践：機械学習プロジェクトを作ってみる

ここからが `uv` の真骨頂です。**「Pythonのバージョン管理」と「仮想環境の作成」と「ライブラリインストール」を同時に行います。**

適当な作業ディレクトリを作って試してみてください。

#### ① プロジェクトの初期化

```bash
# ディレクトリ作成
mkdir my-ml-project
cd my-ml-project

# uvプロジェクトとして初期化（pyproject.tomlなどが作られます）
uv init
```

#### ② Pythonバージョンの指定

WSLにもともと入っているPythonではなく、このプロジェクト専用のPythonをインストール・指定します。

```bash
# 例: Python 3.12 を使う（自動でダウンロード・インストールされます）
uv python pin 3.12
```

#### ③ 機械学習ライブラリのインストール

Condaでは時間がかかっていた作業ですが、`uv`なら一瞬です。仮想環境（.venv）も自動で作られます。

```bash
# PyTorch, NumPy, Pandas をインストール
uv add torch torchvision numpy pandas
```

※ `uv add` を実行した瞬間に仮想環境が構築され、ライブラリが入ります。
従来通り、requirements.txtを使うことも可能です。

#### ④ 実行確認

以下のコマンドで、Pythonが起動し、PyTorchからGPU（CUDA）が見えているか確認できます。

```bash
# uv経由でPythonコードをワンライナーで実行
uv run python -c "import torch; print(f'Torch: {torch.__version__}, CUDA: {torch.cuda.is_available()}')"
```

`CUDA: True` と出れば成功です！

---

### 💡 便利な使い方のコツ

- 仮想環境に入りたい場合:
    
    従来どおり source .venv/bin/activate で入れますが、基本的には uv run python script.py のように、uv run を頭につければ、仮想環境に入らなくてもその環境で実行してくれます（とても楽です）。
    
- Pythonのバージョンを変えたい場合:
    
    uv python pin 3.10 と打つだけで、そのプロジェクトのPythonバージョンが切り替わります。
    
- **Jupyter Labを使いたい場合:**
    い
```bash
uv add --dev jupyterlab
uv run jupyter lab
```
    
    これでブラウザからアクセスできます。
    

まずはこれで動かしてみて、もし「特定の古いライブラリが動かない」などの問題が出れば、その時だけConda（Pixi）を検討するという流れが一番スムーズです。