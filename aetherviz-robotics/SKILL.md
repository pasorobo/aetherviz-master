---
name: aetherviz-robotics
description: AetherViz Robotics - Modern Robotics (Lynch & Park) 教科書の各章を 3D インタラクティブ Web ページに変換するロボティクス教育可視化スキル
---

# AetherViz Robotics —— ロボティクス教育可視化建築家

**バージョン**: 0.1 (プロトタイプ)
**作成日**: 2026-05-06
**派生元**: AetherViz Master v5.0
**対象教材**: *Modern Robotics: Mechanics, Planning, and Control* by Kevin M. Lynch & Frank C. Park (Cambridge University Press, 2017)
**コア使命**: 教科書の各章の概念（C-space、SE(3)、PoE、ヤコビアン、IK、動力学、軌道、運動計画、制御、グラスピング、移動ロボット）をワンクリックで沈浸型 3D 教材へ変換

---

## 1. 配色テーマ — Industrial Robotics Palette

```css
/* メインテーマ：工業ロボット風（青鋼 × 警告オレンジ） */
--robotics-primary:    linear-gradient(135deg, #0EA5E9 0%, #6366F1 50%, #A855F7 100%);
--robotics-accent:     #FB923C;   /* セーフティオレンジ */
--robotics-grid:       #1E293B;   /* 工房グリッド色 */
--robotics-bg:         linear-gradient(180deg, #0B1120 0%, #1E293B 50%, #0F172A 100%);

/* 物理量別の色彩規約（Modern Robotics 慣例） */
--frame-x:             #EF4444;   /* x 軸（赤） */
--frame-y:             #22C55E;   /* y 軸（緑） */
--frame-z:             #3B82F6;   /* z 軸（青） */
--screw-axis:          #FB923C;   /* スクリュー軸 */
--angular-velocity:    #A855F7;   /* 角速度ω */
--linear-velocity:     #06B6D4;   /* 線速度v */
--joint-axis:          #FACC15;   /* ジョイント軸 */
--manipulability:      #14B8A6;   /* マニピュラビリティ楕円体 */
--obstacle:            #DC2626;   /* 障害物 */
--target:              #10B981;   /* 目標位置 */
--trajectory:          #FBBF24;   /* 軌跡 */

/* グラス材質（エンジニアリング HUD 風） */
--glass-bg:            rgba(15, 23, 42, 0.78);
--glass-border:        rgba(99, 102, 241, 0.35);
--glass-shadow:        0 8px 32px rgba(0, 0, 0, 0.5);
```

---

## 2. 教科書マッピング (Ch.1〜Ch.13)

このスキルは Lynch & Park の章構成に対応した **可視化テンプレ集** を持つ：

| 章 | 概念 | 主要可視化 | 推奨レンダリング |
|----|------|-----------|----------------|
| Ch.1 | プレビュー：ロボット工学の概観 | DOF、ロボット分類 | 3D 概念図 |
| Ch.2 | コンフィギュレーション空間 (C-space) | C-obstacle、トポロジー | 2D SVG + 3D |
| **Ch.3** | **剛体運動 SE(3)** | **回転行列、ねじ理論、ツイスト、随伴写像** | **Hybrid (Three.js + KaTeX)** |
| **Ch.4** | **順運動学 (PoE)** | **指数積公式、6R アーム、{s} と {b} 座標** | **Three.js 3D** |
| **Ch.5** | **速度運動学とヤコビアン** | **空間/ボディヤコビアン、マニピュラビリティ楕円体、特異姿勢** | **Hybrid** |
| **Ch.6** | **逆運動学** | **解析解（幾何）、Newton-Raphson 数値解** | **Hybrid** |
| Ch.7 | 閉ループ運動学 | スチュワートプラットフォーム | Three.js 3D |
| Ch.8 | 開ループ動力学 | Newton-Euler、Lagrangian | Hybrid |
| Ch.9 | 軌道生成 | 多項式、台形、S 曲線、screw path | Hybrid |
| Ch.10 | 運動計画 | グリッド、RRT、PRM、A* | SVG 優位 |
| Ch.11 | ロボット制御 | PID、計算トルク制御 | Hybrid |
| Ch.12 | グラスピング | 形状閉鎖、力閉鎖、摩擦円錐 | Three.js 3D |
| Ch.13 | 車輪型移動ロボット | ユニサイクル、差動駆動、オムニ、Ackermann | Hybrid |

**プロトタイプ実装範囲（v0.5）**: Ch.2〜Ch.13 全 12 章 + 拡張トピック合計 21 プロトタイプ実装済 ✅

---

## 3. ロボティクス専用キーワード辞書

```javascript
const ROBOTICS_KEYWORDS = {
  // 章レベル
  ch3: ['rigid body', 'SE(3)', 'SO(3)', 'rotation matrix', 'screw', 'twist', 'wrench', 'exponential coordinates', 'adjoint',
        '剛体', '回転行列', 'ねじ', 'ツイスト', 'レンチ', '随伴', '指数座標'],
  ch4: ['forward kinematics', 'PoE', 'product of exponentials', 'DH parameters', 'kinematic chain', 'end-effector',
        '順運動学', '指数積', 'DH パラメータ', '運動学チェーン', 'エンドエフェクタ'],
  ch5: ['Jacobian', 'manipulability', 'singularity', 'velocity kinematics', 'space Jacobian', 'body Jacobian',
        'ヤコビアン', 'マニピュラビリティ', '特異姿勢', '速度運動学'],
  ch6: ['inverse kinematics', 'IK', 'Newton-Raphson', 'numerical IK', 'analytical IK',
        '逆運動学', '逆運動学解', 'ニュートン法'],
  ch7: ['parallel manipulator', 'closed chain', 'Stewart platform', '閉ループ', 'パラレル機構'],
  ch8: ['dynamics', 'Newton-Euler', 'Lagrangian', 'mass matrix', '動力学', 'ラグランジュ'],
  ch9: ['trajectory', 'time scaling', 'trapezoidal', 'S-curve', '軌道', '軌道生成'],
  ch10: ['motion planning', 'RRT', 'PRM', 'A*', 'sampling', '運動計画', 'サンプリング'],
  ch11: ['control', 'PID', 'computed torque', '制御', 'PID 制御'],
  ch12: ['grasp', 'form closure', 'force closure', 'friction cone', 'グラスピング', '把持'],
  ch13: ['mobile robot', 'unicycle', 'differential drive', 'omnidirectional', 'Ackermann',
         '移動ロボット', 'ユニサイクル', '差動駆動']
};

function detectChapter(topic) {
  const t = topic.toLowerCase();
  for (const [ch, keywords] of Object.entries(ROBOTICS_KEYWORDS)) {
    if (keywords.some(k => t.includes(k.toLowerCase()))) return ch;
  }
  return 'ch1'; // デフォルト：プレビュー
}
```

---

## 4. ロボティクス共通実装規約

### 4.1 座標系描画ヘルパー（3D シーン共通）

```javascript
// 右手系の座標フレームを 3D に描画
function drawFrame(scene, T = identity4(), scale = 0.3, label = '') {
  const origin = new THREE.Vector3(T[0][3], T[1][3], T[2][3]);
  const xAxis  = new THREE.Vector3(T[0][0], T[1][0], T[2][0]);
  const yAxis  = new THREE.Vector3(T[0][1], T[1][1], T[2][1]);
  const zAxis  = new THREE.Vector3(T[0][2], T[1][2], T[2][2]);

  scene.add(new THREE.ArrowHelper(xAxis, origin, scale, 0xEF4444, scale*0.3, scale*0.15));
  scene.add(new THREE.ArrowHelper(yAxis, origin, scale, 0x22C55E, scale*0.3, scale*0.15));
  scene.add(new THREE.ArrowHelper(zAxis, origin, scale, 0x3B82F6, scale*0.3, scale*0.15));
  // ラベルは Sprite + CanvasTexture で
}
```

### 4.2 必須数学ユーティリティ

すべてのプロトタイプには、最低限以下のロボティクス数学関数を **インライン** で含める：

```javascript
// SO(3) ロドリゲス公式: R = e^[ω̂]θ
function so3Exp(omega, theta) { /* I + sin(θ)[ω] + (1-cos(θ))[ω]² */ }

// SE(3) 指数写像: T = e^[S]θ
function se3Exp(S, theta) {
  // S = [ω; v]: 6次元ねじ
  // 出力: 4×4 同次変換行列
}

// 同次変換の逆元
function trInv(T) { /* [Rᵀ, -Rᵀp; 0, 1] */ }

// 随伴表現 [Ad_T] (6×6)
function adjoint(T) {
  // [R, 0; [p]R, R]
}

// ねじ axis × angle → ScrewAxis 表記
function screwToAxis(S) { /* {q, ŝ, h} */ }
```

### 4.3 KaTeX 数式表示規約

ロボティクス数式は KaTeX レンダリングを **必ず** 使用：

```html
<!-- 例：指数積公式 -->
<div>$$ T_{sb}(\theta) = e^{[\mathcal{S}_1]\theta_1} e^{[\mathcal{S}_2]\theta_2} \cdots e^{[\mathcal{S}_n]\theta_n} M $$</div>

<!-- 例：随伴 -->
<div>$$ [\text{Ad}_T] = \begin{bmatrix} R & 0 \\ [p]R & R \end{bmatrix} $$</div>

<!-- 例：ヤコビアン -->
<div>$$ \mathcal{V}_s = J_s(\theta)\dot{\theta} $$</div>
```

### 4.4 標準 UI レイアウト

| ゾーン | 内容 |
|--------|------|
| 上部ナビ | 章番号 + 章タイトル + 「重置」「全屏」「关于」 |
| 左サイドバー (28%) | 学習目標 / 主要数式 (KaTeX) / 章本文要約 |
| 中央キャンバス (72%) | Three.js 3D シーン + SVG 指示オーバーレイ |
| 右下コントロール | 関節角スライダー / アニメーションボタン / 表示切替トグル |
| 右上 HUD | リアルタイム T 行列 / 現在の関節値 |

---

## 5. 章別ビジュアライゼーション仕様

### Ch.3：剛体運動 (Rigid-Body Motions)

**学習目標**:
- SO(3) と SE(3) の幾何的意味を理解する
- ロドリゲス公式と指数写像を適用できる
- ねじ／ツイスト／レンチを区別できる

**可視化要素**:
- 基準フレーム {s} と移動フレーム {b} を 3D 表示
- スライダー：(ω̂ʸᶻˣ, θ) — ロドリゲス回転をリアルタイム適用
- スライダー：(v) — 並進ベクトル
- スクリュー軸 S = (ω, v) を 3D ライン + アローで表示
- ピッチ h = ω·v/||ω||² を数値表示
- 行列 R, T を画面横に常時表示（KaTeX）

### Ch.4：順運動学 (PoE)

**学習目標**:
- 空間 PoE 公式を多関節アームに適用できる
- スクリュー軸 Sᵢ を読み取れる
- 関節空間 → 作業空間の写像を理解する

**可視化要素**:
- 6R 空間アーム（簡略化：UR5 風の DH パラメータ）
- 各関節にスライダー（θ₁〜θ₆）
- 各リンクを `THREE.CylinderGeometry` で生成
- 各 Sᵢ をフレーム原点から **半透明アロー** で表示
- T_sb をリアルタイムに 4×4 行列で右上 HUD に表示
- 「ホームポジション (M)」「ランダム姿勢」ボタン

### Ch.5：速度運動学 (Jacobians)

**学習目標**:
- 空間ヤコビアン Jₛ とボディヤコビアン Jᵦ を区別できる
- マニピュラビリティ楕円体の意味を理解する
- 特異姿勢を視覚的に識別できる

**可視化要素**:
- 2R 平面アーム（簡略化のため 2 リンク）
- スライダー：θ₁, θ₂
- 各列 Jₛᵢ をエンドエフェクタから矢印で表示
- マニピュラビリティ楕円体を **SVG オーバーレイ** で描画
- 行列式 det(JJᵀ) を数値表示（特異値 → 0 で警告色）
- 関節速度 → エンドエフェクタ速度マッピングをスライダーで操作

### Ch.6：逆運動学 (IK)

**学習目標**:
- 解析的 IK と数値的 IK の違いを理解する
- Newton-Raphson 反復を観察できる
- 解の冗長性／不存在を視覚的に把握する

**可視化要素**:
- 2R / 3R 平面アーム
- マウスクリックで目標位置を設定
- 反復ステップを 1 ステップずつ「次へ」で進行可能
- 各ステップで Jᵦ⁺ × エラーベクトルを矢印表示
- 収束判定（残差ノルム）をプログレスバーで表示
- 解析解（cos⁻¹ ベース）と数値解の比較トグル

---

## 6. 出力規則

AetherViz Master と同じく **100% 厳守ルール** を継承：

1. 出力は単一 HTML（`<!DOCTYPE html>` 〜 `</html>`）
2. 説明文・マークダウン・コードブロックの混入禁止
3. 外部ファイル依存ゼロ（CDN は許可、ロボティクス向けに以下を追加）：
   - **mathjs** (数値線形代数のフォールバック用、オプション):
     `https://cdnjs.cloudflare.com/ajax/libs/mathjs/12.4.2/math.min.js`
4. すべての行列計算は **インライン JavaScript** で実装（外部依存最小化）
5. 教科書 PDF の図面・本文を直接コピーしない（章番号と概念名の参照のみ可）

---

## 7. 実行フロー

```
[ユーザー入力: "PoE を可視化して" / "ヤコビアン" / "IK" 等]
        │
        ▼
[detectChapter(topic) → 該当章を特定]
        │
        ▼
[該当章の可視化テンプレを選択]
        │
        ▼
[HTML を生成（Three.js シーン + SVG オーバーレイ + KaTeX）]
        │
        ▼
[出力: lesson.html]
```

---

## 8. 安全性と倫理

- **教科書著作権の尊重**: Lynch & Park 教科書は CC BY-NC-SA 4.0 で配布されているが、図表の直接複製は避け、概念の独自再表現に留める
- **数値計算の信頼性**: ロボティクス計算は安全性に直結する分野のため、生成 HTML には常に「教育目的、産業利用には別途検証必要」の注記を表示
- **数式の検証**: 生成された KaTeX 数式は、ブラウザで開いた際に視覚的に検証されるべき

---

**スキル状態**: ✅ 全章プロトタイプ + 拡張トピック完備 + スマホ対応 (ポートレート/ランドスケープ両対応)
**バージョン**: 0.7
**プロトタイプ**: 21 ファイル（Ch.2 ～ Ch.13、コア 11 章 + 拡張 10 件）
**スマホ対応**:
  - ポートレート: 上下ドロワー + 下部水平タブバー
  - ランドスケープ (height ≤ 500px): 右からスライドドロワー + 右端縦タブストリップ
  - タッチ操作 / ピンチズーム / 画面回転対応
**残作業**: 多言語キーワード辞書拡張、ユニットテスト整備、視覚回帰テスト整備、目次ページ追加
