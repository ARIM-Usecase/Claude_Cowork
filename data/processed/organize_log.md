# データ整理ログ (organize_log.md)

## 参照した取扱説明書・メタデータファイル
- `dataset_32d1cc75-4152-48a1-b404-8ee51e308f05/catalog.json` : データセット目録（内容: null）
- `dataset_32d1cc75-4152-48a1-b404-8ee51e308f05/invoice.schema.json` : テンプレート定義「XRD_BL14B1テンプレート」
- `dataset_32d1cc75-4152-48a1-b404-8ee51e308f05/metadata-def.json` : 測定パラメータ定義
- `dataset_32d1cc75-4152-48a1-b404-8ee51e308f05/data_0001/invoice.json` : データセット基本情報
- `dataset_32d1cc75-4152-48a1-b404-8ee51e308f05/data_0001/data.json` : データセット詳細情報
- `dataset_32d1cc75-4152-48a1-b404-8ee51e308f05/data_0001/meta/metadata.json` : 全測定条件（319件）

## 実験概要（invoice.json より）
- **試料**: La2Ni3合金（LaとNiを2:3比率でアーク溶解→乳鉢粉砕→真空中350℃3時間熱処理）
- **実験名**: 高圧下での新規La-Ni水素化物の形成過程の観測
- **装置**: SPring-8 BL14B1（放射光XRD）
- **実験ID**: A2308
- **測定日**: 2022-07-31〜
- **助成**: JPMXP1222QS0007（マテリアル先端リサーチインフラ事業）
- **代表者**: SATO, Toyoto（東北大学）
- **説明**: 9 GPa, 800℃の高温高圧水素とLa2Ni3合金の反応を放射光その場観察

## データ構造の解釈（metadata-def.jsonより）
| フィールド | 日本語名 | 単位 |
|---|---|---|
| Sample name | ファイル名 | - |
| Profile number | サンプル名 | - |
| Time | 測定開始日時 | - |
| 2Theta(deg) | 回折角 | deg |
| Temperature(K) | 温度 | K |
| Pressure(GPa) | 圧力 | GPa |
| Press load(ton) | プレス加重 | ton |
| Live time(sec) | ライブタイム | sec |
| Dead time(%) | デッドタイム | % |

## 採用した整理ルール

### ファイル種別と配置先
| 種別 | ZIPパス | 整理後パス | 件数 |
|---|---|---|---|
| XRD参照パターン (CSV) | structured/A2308*.csv | xrd/reference/*.csv | 3 |
| XRD時系列パターン (CSV) | structured/seq_*.csv | xrd/sequential/*.csv | 316 |
| XRD参照画像 (PNG) | main_image/A2308*.png | images/reference/*.png | 3 |
| XRD時系列画像 (PNG) | main_image/seq_*.png | images/sequential/*.png | 316 |
| メタデータ (JSON) | meta/metadata.json | metadata/metadata.json | 1 |
| 補助データ (XLSX) | nonshared_raw/*.xlsx | supplementary/auxdata_high_pressure_device.xlsx | 1 |
| メタデータ表 | （新規生成） | metadata/metadata_table.csv | 319行 |

### 命名規則
- 元ファイル名をそのまま踏襲（スペース・特殊文字なし、Python安全）
- xlsxのみ英語ファイル名に変換: `高温高圧装置_補助ファイル_出力.xlsx` → `auxdata_high_pressure_device.xlsx`
- NPDファイル（319件）は生データのためコピー対象外（解析にはCSVを使用）
- PNG画像（320件）はOneDriveのクラウド同期状態の都合上、コピーをスキップ（images/ディレクトリ構造は確保済み）

## 測定条件の範囲（metadata.jsonより抽出）
- **測定数**: 319件（参照3件 + 時系列316件）
- **温度範囲**: 300.1 K〜1073.6 K（約27℃〜800℃）
- **圧力範囲**: 0.00 GPa〜9.00 GPa
- **測定時間**: 2022-07-31 10:26〜（1回/約60秒）
- **2θ角**: 固定 6.0°（エネルギー分散型XRD）
- **チャンネル数**: 4096チャンネル / パターン
- **エネルギー範囲**: 0.046〜176.36 keV

## 整理前後のディレクトリ比較

### 整理前（ZIP内構造）
```
dataset_32d1cc75-4152-48a1-b404-8ee51e308f05/
├── catalog.json
├── catalog.schema.json
├── invoice.schema.json
├── metadata-def.json
└── data_0001/
    ├── invoice.json
    ├── data.json
    ├── filemeta.json
    ├── meta/
    │   └── metadata.json
    ├── main_image/         # 320 PNG
    │   ├── A2308001-003.png
    │   └── seq_0001-0316.png
    ├── structured/         # 319 CSV
    │   ├── A2308001-003.csv
    │   └── seq_0001-0316.csv
    └── nonshared_raw/
        ├── *.npd (319件)
        └── 高温高圧装置_補助ファイル_出力.xlsx
```

### 整理後（data/processed/構造）
```
data/processed/
├── file_mapping.csv           # 元ファイル↔整理後パス対応表（641件）
├── organize_log.md            # 本ファイル
├── xrd/
│   ├── reference/             # A2308001-003.csv（参照・ベースライン）
│   └── sequential/            # seq_0001-0316.csv（時系列XRDパターン）
├── images/
│   ├── reference/             # A2308001-003.png（※ZIPから直接参照）
│   └── sequential/            # seq_0001-0316.png（※ZIPから直接参照）
├── metadata/
│   ├── metadata.json          # 全測定条件（オリジナル）
│   ├── metadata_table.csv     # 表形式に変換（319行×12列）
│   └── file_mapping.csv       # ファイル対応表バックアップ
└── supplementary/
    └── auxdata_high_pressure_device.xlsx
```

## 注意点・特殊ケースの扱い
1. **NPDファイル（生データ）**: ZIPに319件含まれるが、解析にはCSVで十分なためコピー対象外。
2. **metadata_table.csv追記**: オリジナルには存在しない。metadata-def.jsonの定義に従い、
   metadata.jsonの319エントリを表形式（index, filename, time, temperature_K, pressure_GPa等）に変換して追加。
3. **PNGファイル**: OneDriveのクラウド同期の制約でコピーをスキップ。必要に応じてZIPから直接読み込み可能。
4. **時系列インデックス**: metadata.jsonの変数リストはindex 0-2がA2308001-003、index 3-318がseq_0001-0316に対応。

## 生成ファイル
- `data/processed/xrd/reference/` : 3 CSVファイル（参照XRDパターン）
- `data/processed/xrd/sequential/` : 316 CSVファイル（時系列XRDパターン）
- `data/processed/metadata/metadata.json` : オリジナルメタデータ
- `data/processed/metadata/metadata_table.csv` : 表形式メタデータ（319行）
- `data/processed/supplementary/auxdata_high_pressure_device.xlsx` : 補助データ
- `data/processed/file_mapping.csv` : ファイル対応表（641件）
- `data/processed/organize_log.md` : 本ファイル
