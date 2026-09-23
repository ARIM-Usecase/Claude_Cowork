# La2Ni3 高圧水素化その場観察XRD データ解析教材

放射光施設SPring-8 BL14B1で取得したLa2Ni3合金の高温高圧その場観察エネルギー分散型XRDデータを用いた、Pythonによるデータ解析の実践教材です。

**データセット出典:**  
ARIMデータポータル公開データセット  
課題番号：JPMXP1222QS0007  
データセット名：高圧下での新規La-Ni水素化物の形成過程の観測  
代表者：佐藤豊人 芝浦工業大学  
DOI：https://doi.org/10.71947/arim.jpmxp1222qs0007

---

## ノートブック一覧

| No. | ファイル | 内容 | Colabで開く | 主要手法 |
| --- | --- | --- | --- | --- |
| 1 | [`01_eda.ipynb`](https://github.com/ARIM-Usecase/Claude_Cowork/blob/main/01_eda.ipynb) | 探索的データ解析（EDA）: 316パターンの時系列XRDデータの全体像を把握 | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ARIM-Usecase/Claude_Cowork/blob/main/01_eda.ipynb) | pandas, matplotlib, seaborn |
| 2 | [`02_signal_processing.ipynb`](https://github.com/ARIM-Usecase/Claude_Cowork/blob/main/02_signal_processing.ipynb) | 信号処理: スムージング・ベースライン補正・ピーク検出 | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ARIM-Usecase/Claude_Cowork/blob/main/02_signal_processing.ipynb) | scipy (Savitzky-Golay, find_peaks) |
| 3 | [`03_machine_learning.ipynb`](https://github.com/ARIM-Usecase/Claude_Cowork/blob/main/03_machine_learning.ipynb) | 機械学習: PCA・K-meansクラスタリング・RandomForest回帰 | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ARIM-Usecase/Claude_Cowork/blob/main/03_machine_learning.ipynb) | scikit-learn, numpy |
| 4 | [`04_data_analysis_summary.ipynb`](https://github.com/ARIM-Usecase/Claude_Cowork/blob/main/04_data_analysis_summary.ipynb) | 結果統合と文献との比較考察 | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ARIM-Usecase/Claude_Cowork/blob/main/04_data_analysis_summary.ipynb) | 統合分析・文献比較 |

---

## 対象読者・前提知識

- Pythonの基礎文法（変数、リスト、for文、pandas/numpyの基本操作）を理解している方
- 放射光X線回折(XRD)・高圧実験・機械学習の専門知識は前提としません
- 各ノートブックの冒頭に「対象読者・前提知識・動作環境」ブロックを記載しています

## 動作環境

**Python:** 3.10 以降

**主要パッケージ:**
- `pandas`, `numpy`: データ操作・配列処理
- `matplotlib`, `seaborn`, `matplotlib_fontja`: 可視化（日本語フォント対応）
- `scipy`: 信号処理（Savitzky-Golayスムージング、ピーク検出）
- `scikit-learn`: 機械学習（PCA、K-means、RandomForest）

**実行環境:**
- Jupyter Notebook / JupyterLab、または Google Colab

## 実行方法

### ローカル環境の場合

1. このリポジトリをクローンしてください：
```bash
git clone https://github.com/ARIM-Usecase/Claude_Cowork.git
cd Claude_Cowork
```

2. 必要なパッケージをインストールしてください：
```bash
pip install pandas numpy matplotlib seaborn scipy scikit-learn matplotlib_fontja
```

3. Jupyter環境を起動し、各ノートブックを先頭セルから順に実行してください：
```bash
jupyter lab
```

`data/`・`output/` フォルダはこのプロジェクトフォルダ内に同梱済みのため、追加のダウンロードは不要です。

### Google Colab の場合

上表の「Colabで開く」バッジから直接開けます。バッジ経由で開いた場合、各ノートブック冒頭の「教材への接続」セルを実行すると、このリポジトリを自動的にクローンし、必要なデータ一式を含めます。

```python
! pip install matplotlib_fontja
!git clone https://github.com/ARIM-Usecase/Claude_Cowork.git
%cd Claude_Cowork
```

ローカル実行時はこのセルの実行は不要です。

---

## ノートブックの詳細説明

### 1️⃣ 01_eda.ipynb - 探索的データ解析（EDA）

**目的:** 316パターンの時系列XRDデータに対し、データの全体像を把握する

**主な内容:**
- メタデータの読み込みと参照パターン・時系列パターンの分離
- 温度・圧力・測定条件の時系列プロファイル表示
- XRDパターンのエネルギー軸・d値軸での可視化
- P-Tトラジェクトリ（実験経路）のプロット
- 基本統計量の計算と分布確認

**成果物:**
- `fig01_conditions_timeseries.png`: 温度・圧力・デッドタイムの推移
- `fig02_PT_trajectory.png`: P-T相図上の実験経路
- `fig03_reference_xrd.png`, `fig04_xrd_waterfall_sample.png`: XRDパターン可視化

**前提知識:** Pythonの基本文法、pandasの基礎操作

---

### 2️⃣ 02_signal_processing.ipynb - 信号処理

**目的:** 316パターンのXRDデータに対し、ノイズ除去・ピーク検出を実施し、相変化の追跡を準備する

**主な内容:**
- 全316パターンを2D配列に読み込み（測定番号 × エネルギーチャンネル）
- 2D カラーマップによる時系列全体の可視化
- Savitzky-Golayスムージング（window=15, polyorder=3）
- 移動最小値法によるベースライン補正
- ピーク検出（`scipy.signal.find_peaks`）と参照パターンの解析
- 監視d値の時系列追跡
- 反応前後（T<400K vs T>850K）の差分スペクトル

**成果物:**
- `fig01_xrd_2d_colormap.png`: 時系列カラーマップ
- `fig02_smoothing_example.png`: スムージング・残差比較
- `fig03_baseline_correction.png`: ベースライン補正の効果
- `fig04_peak_tracking.png`: 主要ピークの時系列追跡
- `fig05_diff_spectrum.png`: 反応前後の差分スペクトル

**前提知識:** scipy による信号処理の基礎概念

---

### 3️⃣ 03_machine_learning.ipynb - 機械学習

**目的:** スペクトル形状から物質の相（フェーズ）を教師なしで検出し、温度予測の可能性を検証する

**主な内容:**
- **PCA（主成分分析）:** 2090チャンネル → 10次元への次元削減
  - 寄与率（explained variance ratio）の計算
  - PC1 vs PC2 の可視化
  - PCAローディングの解釈

- **K-meansクラスタリング:** 相の自動同定
  - シルエット係数（silhouette score）による最適クラスタ数決定
  - P-T空間・PC空間での相分布

- **ランダムフォレスト回帰:** スペクトル → 温度予測
  - **重要な学習:** 評価方法の違いによる性能格差
  - 訓練当てはめ vs シャッフルCV vs 測定順ブロックCV
  - 時系列・非可逆過程の回帰の限界

**成果物:**
- `fig01_pca_scores.png`: PCAスコアの可視化（3つの視点）
- `fig02_pca_loadings.png`: PC1・PC2のローディング
- `fig03_kmeans_clusters.png`: K-meansクラスタリング結果（4つの視点）
- `fig04_ml_regression.png`: 回帰予測結果と特徴量重要度

**前提知識:** scikit-learnの基礎、交差検証の概念

---

### 4️⃣ 04_data_analysis_summary.ipynb - データ分析まとめ

**目的:** 01～03の結果を統合し、文献（Sato et al. 2023/2025）との比較考察を行う

**主な内容:**
- 実験タイムライン総合サマリー図（6パネル統合表示）
- PC1・PC2と温度・圧力の相関係数の実算値
- 文献との比較
  - 利用報告書（22QS0007）での初期報告
  - 査読済論文（Sato et al., Inorg. Chem. 2025）による精緻化
  - La2Ni3の結晶構造（斜方晶, Cmca）の確認
- 測定条件・クラスタ統計の表示
- 解析結果CSVの出力

**成果物:**
- `fig01_summary_overview.png`: 総合サマリー図
- `analysis_results.csv`: クラスタラベル・PC値の出力

**重要なポイント:**
- 「事前の想定と実測が食い違う」ことはよくあり、必ず実際に計算して確認することが重要
- PC1は温度より圧力との相関がやや強いという実測結果

---

## フォルダ構成

```
.
├── 01_eda.ipynb                    # パート1: 探索的データ解析
├── 02_signal_processing.ipynb      # パート2: 信号処理
├── 03_machine_learning.ipynb       # パート3: 機械学習
├── 04_data_analysis_summary.ipynb  # パート4: 結果統合・文献比較
├── data/
│   └── processed/
│       ├── xrd/
│       │   ├── reference/          # 参照パターン(A2308001-003.csv)
│       │   └── sequential/         # 時系列パターン(seq_0001-0316.csv)
│       └── metadata/
│           └── metadata_table.csv  # 測定条件メタデータ
├── output/                         # 実行時に自動生成される図版・結果出力
└── README.md
```

## データセット情報

| 項目 | 内容 |
| --- | --- |
| **試料** | La2Ni3合金（斜方晶, 空間群 Cmca No.64） |
| **測定施設** | SPring-8 BL14B1（高圧ビームライン） |
| **手法** | エネルギー分散型X線回折（ED-XRD） |
| **測定条件** | T: 300-1074 K, P: 0-9 GPa（約315分間、約60秒間隔） |
| **パターン数** | 参照3件 + 時系列316件 = 計319件 |
| **反応対象** | 高圧水素ガス |
| **ライセンス・利用条件** | ARIMデータポータル利用規約に従う |

## 参考文献

- **初期報告:** 利用報告書 JPMXP1222QS0007（2022年利用）
- **査読済論文:**
  - Sato et al., "Crystal Structure Investigations of LaNi2Hx (x = 0 and 3.08 with Metal Atoms in AlB2 Units) and LaNiH1.01", *Inorg. Chem.* (2025), DOI: 10.1021/acs.inorgchem.5c03517
  - Sato et al., *Molecules* (2023), *J. Phys. Chem. C* (2025)
- **結晶構造:** Van Vucht & Buschow, "Preparation and Structure of Some New Ternary Rare Earth Intermetallic Compounds", *J. Less-Common Met.* **46**, 133-138 (1976)

## 各ノートブックの構成

各ノートブックには、以下の標準的な構成が含まれています：

1. **冒頭ブロック**
   - 対象読者・前提知識
   - シリーズ構成（全4部作の中での位置付け）
   - 動作環境・版・ライセンス情報

2. **教材への接続セル**
   - Google Colab用のセットアップコード（git clone, パッケージインストール）
   - ローカル環境では不要

3. **本体セクション**
   - 段階的な説明と実装
   - 質問・コメント欄（専門家との協力によるパネル形式コードウォークスルーを想定）
   - 図版や統計情報の出力

4. **章末**
   - まとめ
   - 本ノートブックで扱っていないこと（今後の課題）
   - 演習問題

## ライセンス

各ノートブックのコード部分はMITライセンスで提供します。  
データセットのライセンス・利用条件はARIMデータポータルおよび上記の出典元に従ってください。

## 更新履歴

- 各ノートブックの詳細な変更点は、本リポジトリのコミット履歴を参照してください

---

**作成者:** Claude (AI Assistant)  
**対応時期:** 2026年9月  
**所属:** ARIM（マテリアル先端リサーチインフラ事業）
