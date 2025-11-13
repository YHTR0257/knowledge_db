
# Overview

A Program to Analyze Chemical Bonding Based on Plane-Wave and PAW Output

# Requirements

- DFT計算ができるソフトウェア
	- VASP
	- ABINET
	- Quantum ESPRESSO

# Usage

## 1. Installation

LOBSTERは単一のバイナリファイルとして提供される。`$PATH`が通ったディレクトリにコピーするだけで使用可能。

```bash
cp lobster-5.0.0 ~/bin/lobster
chmod +x ~/bin/lobster
```

**特徴:**
- バージョン1.1.0以降、ランタイム依存関係なし
- 多くのLinuxディストリビューションで動作

## 2. VASP計算の準備

### 2.1 必須INCAR設定

LOBSTERでVASP出力を解析する際の**必須設定**:

| パラメータ | 設定値 | 説明 |
|---------|--------|------|
| `NSW` | `0` | 静的計算必須（原子の移動なし） |
| `LWAVE` | `.TRUE.` | WAVECARファイルを出力 |
| `ISYM` | `0` or `-1` | 対称性の扱い（時間反転対称性のみ対応） |
| `NBANDS` | 手動設定 | local basisの軌道数以上が必要（後述） |

**注意事項:**
- 静的計算（single-point calculation）で実行すること
- 既存のWAVECARは削除してから実行
- 複素数版VASPを使用（gamma-only版は非対応）

### 2.2 POTCAR設定

**必須:**
- PAW擬ポテンシャルを使用
- **禁止:** Ultrasoft擬ポテンシャル（US-PP）は使用不可

**推奨:**
- `_sv`擬ポテンシャルを使う場合は特に注意
- local basis functionsを手動で指定すること（spilling増加を防ぐため）

## 3. k-Point設定の最適化

### 3.1 対称性の扱い

LOBSTERが要求するk-meshの条件:
- 完全なk-mesh、または
- 時間反転対称性を利用した半分のk-mesh

### 3.2 推奨計算手順

効率的な2段階計算法:

**Step 1: SCF計算（完全対称性使用）**
- 完全な対称性を利用した自己無撞着計算
- 高速で数値的に安定

**Step 2: Non-SCF計算（LOBSTER用）**
- Step 1の電荷密度を使用
- k-mesh設定を変更（時間反転対称性のみ、または対称性なし）
- この出力をLOBSTERで解析

この方法により、バンド構造計算と同様の効率化が可能。

## 4. バンド数（NBANDS）の設定

### 4.1 必要なバンド数

**pCOHP解析を行う場合:**
```
NBANDS ≥ local basisの軌道数
```

数学的な理由により、この条件を満たさないとpCOHP計算は実行されない。

### 4.2 DFTソフトウェア別パラメータ

| ソフトウェア | パラメータ名 |
|------------|------------|
| VASP | `NBANDS` |
| ABINIT | `nband` |
| Quantum ESPRESSO | `nbnd` |

**注意:**
- PAWコードのデフォルト設定ではバンド数が不足している
- pCOHPが期待通りに出力されない場合はまずNBANDSを確認
- LOBSTERの警告メッセージを真剣に受け止めること
- pDOS、pCOOP計算は影響を受けない（常に実行される）

## 5. Local Basis関数の指定

### 5.1 基本原則

正確な化学結合解析には、適切なbasis関数の選択が**必須**:

1. **自動推定を過信しない** - LOBSTERの推定が最適とは限らない
2. **spilling値を最小化** - 絶対値が小さいほど結果が正確
3. **解析前に必ず確認** - spillingが小さくなるまで調整

### 5.2 Basis関数選択の手順

**Step 1: 擬ポテンシャルの電子配置を確認**
- POTCARファイル（VASP）
- XMLファイル（ABINIT）
- UPFファイル（Quantum ESPRESSO）

注: 擬ポテンシャルの価電子配置は自由原子と異なる場合がある

**Step 2: LOBSTERの出力を確認**
- プログラム開始時に提案されるbasis関数を確認
- 自分の想定と比較

**Step 3: spillingが高い場合**
- basis setを実験的に調整
- 最小のspillingが得られるまで繰り返す

### 5.3 品質管理

**重要指標: Charge Spilling**
- できる限り小さい絶対値を目指す
- spillingが大きい場合は結果の信頼性が低下
- FAQも参照すること（マニュアル付属）

## 6. 実行方法

計算ディレクトリ（VASPの出力ファイルがある場所）で実行:

```bash
lobster
```

LOBSTERは自動的にVASPの出力ファイル（WAVECAR、POSCAR等）を読み込んで解析を開始する。

