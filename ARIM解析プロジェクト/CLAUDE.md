# CLAUDE.md

## 概要
ARIMデータポータルからダウンロードしたzipを整理し、参考資料（利用報告書・論文PDF）を踏まえた
EDA・機械学習・信号処理・データ分析コードを作成するプロジェクト。

## ディレクトリ構成

```
project_root/
├── CLAUDE.md
├── data/
│   ├── raw/         # ダウンロードしたzip・展開直後のファイル（読み取り専用扱い）
│   └── processed/   # Python解析用に整理されたデータ・メタデータ
├── 参考/             # 利用報告書・論文PDF
├── output/          # notebook・図表・解析結果
└── .claude/
    └── skills/
        ├── arim-zip-organize/
        │   └── SKILL.md
        └── arim-analysis-codegen/
            └── SKILL.md
```

## 起動条件（重要）

以下のような**簡潔な依頼**が来た場合、ユーザーが個別ステップを指示していなくても、
下記「作業フロー」の **ステップ1〜3を順番にすべて実行する**こと。
途中で停止してユーザーの追加指示を待たない。

トリガー例:
- 「ファイルを data に置いたので作業を行ってください」
- 「zipを置いたので進めてください」
- 「データが揃ったので解析してください」
- 「@data に置きました」

判断基準: ユーザーが「整理して」「要約して」「解析コードを作って」のいずれか1つしか
明示していない場合でも、`data/raw/` にzipが存在し、かつ未整理（`data/processed/` が空
または未作成）であれば、ステップ1から開始し、3まで連続実行する。
すでにステップ1・2が完了済み（`data/processed/`・`reference_summary.md`が存在）であれば、
ステップ3から開始する。

## 作業フロー（パイプライン）

このプロジェクトの本質的なゴールは「ステップ1〜3を最後まで実行し、4種類の解析Notebookを
`output/` に作成すること」である。各ステップ完了時、次のステップに進む前にユーザーへ
完了報告を行うが、確認待ちで停止せず、問題がなければ自動的に次へ進む。

### ステップ1: データ整理（arim-zip-organizeスキル使用）
- `data/raw/` のzipを展開し、同梱の取扱説明書の階層構造定義に従って `data/processed/` に再配置
- 整理ログ・ファイル対応表を `data/processed/` 内に出力
- 完了後、ステップ2へ進む

### ステップ2: 参考資料の確認
- `参考/` 内にPDFがあれば読み込み、実験目的・測定条件・解析手法を抽出し
  `data/processed/reference_summary.md` にまとめる
- `参考/` が空、またはPDFが存在しない場合は、その旨を `reference_summary.md` に
  「参考資料なし」と記録し、参考資料なしでステップ3に進む（停止しない）
- 完了後、ステップ3へ進む

### ステップ3: 解析コード作成（arim-analysis-codegenスキル使用）
- `data/processed/` のデータと `reference_summary.md` を踏まえ、`output/` にNotebookを作成
- **EDA・信号処理・機械学習・データ分析の4種類すべてを作成する**（1種類だけで終了しない）
- 詳細は `.claude/skills/arim-analysis-codegen/SKILL.md` を参照
- 完了後、`output/analysis_log.md` を含めた全体の完了報告を行う

## 完了報告フォーマット
全ステップ完了後、以下を報告する:
- ステップ1: 整理されたファイル数・主な分類
- ステップ2: 参考資料の有無・要約の概要（または「参考資料なし」）
- ステップ3: 作成した4つのNotebookのファイル名と概要

## コーディング規約
- pandas, numpy, matplotlib/seaborn を基本とする
- NumPy 2.0対応（`np.trapz` ではなく `np.trapezoid`）
- 日本語フォント表示が必要な場合はMeiryo等を明示指定
- パスはプロジェクトルートからの相対パスで記述

## 操作の境界（Always / Ask First / Never）
- **Always**: `data/processed/` への書き込み、`output/` へのNotebook・図表作成
- **Ask First**: `参考/` 内ファイルの移動・リネーム
- **Never**: `data/raw/` 内ファイルの編集・削除
