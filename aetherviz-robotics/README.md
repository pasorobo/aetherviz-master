# AetherViz Robotics

> **Modern Robotics: Mechanics, Planning, and Control** (Lynch & Park, Cambridge University Press, 2017) の各章をブラウザ上の 3D インタラクティブ教材へ変換するロボティクス特化スキル。

AetherViz Master の派生スキルとして、ロボティクス教育に特化したキーワード辞書、数学ユーティリティ、可視化テンプレートを提供する。

---

## 構成

```
aetherviz-robotics/
├── SKILL.md                              # スキル定義（LLM 用プロンプト本体）
├── README.md                             # 本ファイル
└── prototypes/
    ├── ch2-cspace.html                   # Ch.2 C-space ↔ Workspace 二重表示
    ├── ch3-rigid-body-motions.html       # Ch.3 SE(3), 回転、スクリュー、ツイスト
    ├── ch4-forward-kinematics.html       # Ch.4 PoE 順運動学
    ├── ch5-velocity-kinematics.html      # Ch.5 ヤコビアン、マニピュラビリティ
    ├── ch5-statics.html                  # Ch.5 静力学 τ = Jᵀ F
    ├── ch5-svd-singularity.html          # Ch.5 SVD 分解と特異姿勢
    ├── ch6-inverse-kinematics.html       # Ch.6 数値 IK (Newton-Raphson)
    ├── ch6-ur5-ik.html                   # Ch.6 6-DOF 解析的 IK（手首分離）
    ├── ch7-closed-chains.html            # Ch.7 4 節リンク、Grübler、Grashof
    ├── ch7-stewart-platform.html         # Ch.7 6-UPS Stewart Platform
    ├── ch8-dynamics.html                 # Ch.8 2R 振子の動力学 (M, C, G)
    ├── ch8-newton-euler.html             # Ch.8 Newton-Euler 再帰アルゴリズム
    ├── ch9-trajectory-generation.html    # Ch.9 3 次・5 次・台形・S 曲線
    ├── ch10-motion-planning.html         # Ch.10 RRT サンプリング計画
    ├── ch10-astar-potential.html         # Ch.10 A* + Potential Fields
    ├── ch10-rrt-star.html                # Ch.10 RRT* 漸近最適計画
    ├── ch11-control.html                 # Ch.11 PID/計算トルク制御
    ├── ch11-tracking-impedance.html      # Ch.11 軌道追従 + インピーダンス
    ├── ch12-grasping.html                # Ch.12 摩擦円錐・力閉鎖
    ├── ch13-mobile-robots.html           # Ch.13 差動駆動・ICR・Go-to-Goal
    └── ch13-ackermann.html               # Ch.13 Ackermann 車型・縦列駐車
```

## クイックスタート

```bash
# プロトタイプを直接ブラウザで開く
open aetherviz-robotics/prototypes/ch3-rigid-body-motions.html

# またはローカルサーバを立てる
cd aetherviz-robotics/prototypes
python -m http.server 8080
# → http://localhost:8080
```

## スキル使用方法（Claude Code 経由）

```bash
claude
> /aetherviz-robotics
> ヤコビアンを可視化して
# または
> PoE
> Newton-Raphson IK
```

## カバーする教科書範囲

| 章 | 概念 | プロトタイプ状態 |
|----|------|----------------|
| Ch.1 | プレビュー | 概念のみ（SKILL.md） |
| **Ch.2** | **C-space ↔ Workspace 二重表示** | ✅ プロトタイプ実装済 |
| **Ch.3** | **剛体運動 SE(3)** | ✅ |
| **Ch.4** | **PoE 順運動学** | ✅ |
| **Ch.5** | **ヤコビアン / 静力学 / SVD** | ✅ × 3 |
| **Ch.6** | **数値 IK / 6-DOF 解析的 IK** | ✅ × 2 |
| **Ch.7** | **4 節リンク / Stewart Platform** | ✅ × 2 |
| **Ch.8** | **動力学 (M, C, G) / Newton-Euler** | ✅ × 2 |
| **Ch.9** | **軌道生成（3 次・5 次・台形・S 曲線）** | ✅ |
| **Ch.10** | **RRT / A\*+Potential / RRT\*** | ✅ × 3 |
| **Ch.11** | **PID / 軌道追従 + インピーダンス** | ✅ × 2 |
| **Ch.12** | **把持（摩擦円錐・力閉鎖）** | ✅ |
| **Ch.13** | **差動駆動 / Ackermann 車型** | ✅ × 2 |

## 設計思想

- **プロトタイプ**: 各 HTML は最小構成・自己完結（CDN のみ）。教育的明瞭さを優先し、エンタープライズ品質ではない。
- **教育目的**: 産業利用には別途検証が必要。
- **教科書尊重**: 章番号・概念名のみ参照。図表の複製は行わない。

## スマホ対応 (v0.7+)

全プロトタイプに共通の**モバイルシム v2**を注入し、ポートレート/ランドスケープ両方で操作・閲覧可能：

### ポートレート（縦持ち、〜768px）
- sidebar / hud は上から、panel は下からスライドする**フル幅ドロワー**
- **下部水平タブバー**（h:48px）：「📖 学習 / ⚙️ 制御 / 📊 状態」でトグル
- 補助プロット (`#plots`, `#joint-space`) は非表示

### ランドスケープ（横持ち、height ≤ 500px、v0.8+）
- **キャンバス（左、約 60-65%）+ アクティブパネル（右、280px）の同時表示**
- 右レール上部の**ミニタブ**（h:32px）：「📖 学習 / ⚙️ 制御 / 📊 状態」で右側の中身を切替
- 進入時に「制御」タブを自動オープン
- `window.innerWidth` オーバーライドにより Three.js renderer / camera が右レール除外サイズで正しく描画（アスペクト保持）
- ナビは 1 行省略、フォント 9-10px に圧縮
- ch2-cspace は左右 2 領域を維持（右レール除外）

### 共通機能
- **タッチ → マウス変換**：1 本指 = ドラッグ、2 本指 = ピンチズーム（3D シーン）
- **画面回転対応**：開いているパネルを自動的に閉じてレイアウト破綻を防止
- **タップ可能なターゲット**：スライダー 14-18px、ボタン高 24-32px
- パネル外タップで自動クローズ

デスクトップ（≥769px ポートレート、≥501px 高さランドスケープ）には影響しない。

## 引用

教科書情報:
- Kevin M. Lynch and Frank C. Park, *Modern Robotics: Mechanics, Planning, and Control*, Cambridge University Press, 2017.
- 公式サイト: http://hades.mech.northwestern.edu/index.php/Modern_Robotics

## ライセンス

本スキル: MIT License（プロジェクトルートの LICENSE に従う）
