# 参考資料要約 (reference_summary.md)

## 参照したPDFファイル

1. `利用報告書 _ User's Reports _ ARIM Japan公式ホームページ_マテリアル先端リサーチインフラ.pdf`
2. `molecules-28-01256-v2.pdf`
3. `synthesis-crystal-structure-and-hydrogen-storage-properties-of-an-ab3-based-alloy-synthesized-by-disproportionation.pdf`

---

## 1. 利用報告書（ARIM Japan 課題番号: 22QS0007）

### 実験目的
水素社会の構築に向けた高密度水素貯蔵材料の開発。先行研究でLaNi5が室温・6 GPaで2.1 mass%水素吸蔵後、NiHxと**新規La-Ni水素化物**に分解することが判明。本研究では新規La-Ni水素化物の組成に近い**La2Ni3**を対象に、その合成機構と形成条件を解明する。

### 試料
- **組成**: La2Ni3（La:Ni = 2:3 モル比）
- **合成**: La片（99.9%）とNi片（99.999%）を真空アーク溶解（東北大学金属材料研究所）
- **前処理**: 粉末化→真空中350℃3時間熱処理→PDindexerで評価

### 装置・測定条件
- **装置**: QS-141 高温高圧プレス装置（量子科学技術研究開発機構 / QST）
- **放射光源**: SPring-8 BL14B1
- **手法**: エネルギー分散型X線回折（2θ = 6.0° 固定）
- **圧力範囲**: 0 GPa → 9 GPa（プレス加重で制御）
- **温度範囲**: 室温 → 1173 K（ヒーター通電、電力法で温度推算）
- **測定**: 約60秒/パターン、時系列で316回連続測定

### 主な結果
- **9 GPa・873 K**（約600℃）で新規La-Ni水素化物の形成を確認
- 新規水素化物相は**LaNi2Hx**であることが示唆

### 対応するデータセット変数
- metadata.jsonのindex 3-318（seq_0001〜seq_0316）が高温高圧その場観察の時系列
- index 0: A2308001（1 GPa, 310 K = 常温加圧ベースライン）
- index 1: A2308002（1 GPa, 310 K）
- index 2: A2308003（0 GPa, 300 K = 大気圧参照）

---

## 2. molecules-28-01256-v2.pdf（Sato et al., Molecules 2023）

**タイトル**: "Hydrogen Absorption Reactions of Hydrogen Storage Alloy LaNi5 under High Pressure"

### 研究背景
- LaNi5は実用的な水素貯蔵材料（AB5型）だが重量水素密度1.38 mass%が課題
- 高圧下での水素吸蔵でさらに高密度化できる可能性

### 主な知見（先行研究として本データと直接関連）
- LaNi5は室温・6 GPaで2.1 mass%水素吸蔵後、**NiHx + 新規La-Ni水素化物**に相分解
- 新規La-Ni水素化物は高い体積水素密度を持つと予測
- エネルギー分散型放射光XRD（同BL14B1）で直接観測

### 解析手法
- エネルギー分散型XRD: 固定角2θ=6°、エネルギー軸でd面間距離を計算
- 回折ピーク位置からd値を算出 → 格子定数・相同定（PDindexer使用）

---

## 3. synthesis-crystal-structure...pdf（Sato et al., J. Phys. Chem. C 2025）

**タイトル**: "Synthesis, Crystal Structure, and Hydrogen Storage Properties of an AB3-Based Alloy Synthesized by Disproportionation Reactions of AB2-Based Alloys"

### 研究内容
- AB3型合金**Y0.68Mg0.32Co3.00**の水素貯蔵特性を報告
- 1.68 mass%可逆水素吸蔵、100サイクル後も劣化最小
- 10 GPaで2.88 mass%まで増加（高密度水素貯蔵の可能性）

### 本研究との関連
- 同グループ（佐藤豊人ら）による類似の高圧XRD実験
- AB3型構造のLa2Ni3は本データの試料と同じ構造型
- **PDindexer**ソフトウェアによるXRDデータ解析を採用
- 高圧下での不均化（disproportionation）反応機構が参考になる

### 解析手法
- エネルギー分散型XRD + PDindexerによるピークインデックス
- 中性子回折（Denis Sheptyakov）による結晶構造精密化（Rietveld）
- 高圧セル用の圧力マーカー使用

---

## 解析上の重要な示唆

### データ解釈の鍵
1. **エネルギー分散型XRD**: ch → energy → d値（d = hc/(2sinθ·E)）への変換が必要
   - 2θ = 6.0° 固定、エネルギー軸(keV)からd値を計算
   - d(Å) = 12.398 / (2 × sin(3°) × E(keV)) ≈ 118.66 / E(keV)

2. **時系列の実験フロー**: 
   - A2308001-002: 初期状態（1 GPa、室温）参照パターン
   - A2308003: 減圧後（0 GPa）参照パターン
   - seq_0001〜seq_0316: 昇圧（1→9 GPa）→昇温（室温→873 K以上）→水素化反応

3. **観測すべき相変化**:
   - La2Ni3（出発物質）→ LaNi2Hx（目的の新規水素化物）
   - 過程でNiHxが生成する可能性
   - 873 K付近（seq約170番前後）が相転移の鍵

4. **圧力・温度の推算**:
   - 圧力: P = L × 0.1 + 0（プレス加重(ton)→GPa換算）
   - 温度: T = p × 5.32158 + 27（電力(W)→温度(℃)推算）

### 解析に推奨されるアプローチ
- XRDパターンのd値変換後、各時点でのピーク位置・強度の追跡
- 圧力・温度をx軸とした各d値の時系列変化プロット
- ピーク強度の主成分分析（PCA）による相変化の自動検出
- La2Ni3の既知回折線（JCPDS）との照合による相同定
