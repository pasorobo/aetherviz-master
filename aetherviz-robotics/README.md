# AetherViz Robotics

> **Modern Robotics: Mechanics, Planning, and Control** (Lynch & Park, Cambridge University Press, 2017) の各章をブラウザ上の 3D インタラクティブ教材へ変換するロボティクス特化スキル。

AetherViz Master の派生スキルとして、ロボティクス教育に特化したキーワード辞書、数学ユーティリティ、可視化テンプレートを提供する。

---

## 構成

```
aetherviz-robotics/
├── SKILL.md                          # スキル定義（LLM 用プロンプト本体）
├── README.md                         # 本ファイル
└── prototypes/
    ├── ch3-rigid-body-motions.html   # Ch.3 SE(3), 回転、スクリュー、ツイスト
    ├── ch4-forward-kinematics.html   # Ch.4 PoE 順運動学
    ├── ch5-velocity-kinematics.html  # Ch.5 ヤコビアン、マニピュラビリティ
    └── ch6-inverse-kinematics.html   # Ch.6 数値 IK (Newton-Raphson)
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
| Ch.2 | C-space | 概念のみ（SKILL.md） |
| **Ch.3** | **剛体運動 SE(3)** | ✅ プロトタイプ実装済 |
| **Ch.4** | **PoE 順運動学** | ✅ プロトタイプ実装済 |
| **Ch.5** | **ヤコビアン** | ✅ プロトタイプ実装済 |
| **Ch.6** | **逆運動学** | ✅ プロトタイプ実装済 |
| Ch.7〜13 | 閉ループ／動力学／軌道／計画／制御／把持／移動 | 未実装（v0.2 以降予定） |

## 設計思想

- **プロトタイプ**: 各 HTML は最小構成・自己完結（CDN のみ）。教育的明瞭さを優先し、エンタープライズ品質ではない。
- **教育目的**: 産業利用には別途検証が必要。
- **教科書尊重**: 章番号・概念名のみ参照。図表の複製は行わない。

## 引用

教科書情報:
- Kevin M. Lynch and Frank C. Park, *Modern Robotics: Mechanics, Planning, and Control*, Cambridge University Press, 2017.
- 公式サイト: http://hades.mech.northwestern.edu/index.php/Modern_Robotics

## ライセンス

本スキル: MIT License（プロジェクトルートの LICENSE に従う）
