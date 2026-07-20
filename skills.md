# テトリスゲーム - プロジェクト技術書類

## 概要
ブラウザで動作するテトリスゲーム。HTML5 Canvas + JavaScriptで実装。

## 使用技術

| 技術 | 用途 |
|------|------|
| HTML5 | 構造・Canvas要素 |
| CSS3 | スタイリング・レスポンシブデザイン |
| JavaScript (ES6+) | ゲームロジック・イベント処理 |
| Canvas API | 2D描画 |
| Web Audio API | サウンドエフェクト生成 |
| localStorage | ハイスコア永続化 |

## ファイル構成

```
seminar_test/
├── index.html    # 全コード（HTML/CSS/JS統合）
└── skills.md     # この技術書類
```

## ゲーム機能

### 基本操作
| キー | 操作 |
|------|------|
| `←` `→` | 左右移動 |
| `↑` | 回転 |
| `↓` | ソフトドロップ（1行下げ+1pt） |
| `Space` | ハードドロップ（着地位置に落下+2pt/行） |
| `Shift` | ホールド（ピース保存・1回のみ） |
| `P` | 一時停止 |
| `Enter` | ゲーム開始 / リトライ |

### 7種類のテトリミノ
| ピース | 色 | 形状 |
|--------|------|------|
| I | シアン | 直線（4ブロック） |
| O | 黄色 | 正方形（2×2） |
| T | 紫 | T字 |
| S | 緑 | S字 |
| Z | 赤 | Z字 |
| J | 青 | J字 |
| L | オレンジ | L字 |

### スコアリング
| アクション | ポイント |
|-----------|---------|
| 1ライン消去 | 100 × レベル |
| 2ライン消去 | 300 × レベル |
| 3ライン消去 | 500 × レベル |
| 4ライン消去（テトリス） | 800 × レベル |
| T-spin（1ライン） | 800 × レベル |
| T-spin（2ライン） | 1200 × レベル |
| T-spin（4ライン） | 2000 × レベル |
| コンボボーナス | 50 × コンボ数 × レベル |
| ソフトドロップ | 1pt / 行 |
| ハードドロップ | 2pt / 行 |

### レベルシステム
- 10ライン消去ごとにレベルアップ
- レベルが上がるほどドロップ速度が速くなる
- 速度: `1000ms - (level-1) × 80ms`（最小50ms）

## 主要な実装

### ピース生成（7-bag方式）
```javascript
function createBag() {
  const bag = [...PIECE_NAMES];
  // Fisher-Yatesシャッフル
  for (let i = bag.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [bag[i], bag[j]] = [bag[j], bag[i]];
  }
  return bag;
}
```
7種類を1セットでシャッフルし、均等な出現率を実現。

### 衝突判定
```javascript
function collides(piece, dx, dy) {
  return piece.blocks.some(([bx, by]) => {
    const nx = piece.x + bx + dx;
    const ny = piece.y + by + dy;
    return nx < 0 || nx >= COLS || ny >= ROWS || (ny >= 0 && grid[ny][nx]);
  });
}
```

### 回転（ウォールキック付き）
```javascript
function rotate() {
  // 90度回転: (x,y) → (-y, x)
  const rotated = currentPiece.blocks.map(([x, y]) => [-y, x]);
  // 正規化して左上に合わせる
  // ウォールキック: [0, -1, 1, -2, 2] の順でx方向にずらして試行
}
```

### T-spin検出
回転直後にT字の4コーナーうち3つ以上がブロックで占有されている場合にT-spinと判定。

### サウンド（Web Audio API）
```javascript
function playSound(freq, type, dur, vol) {
  const osc = audioCtx.createOscillator();
  const gain = audioCtx.createGain();
  osc.type = type;
  osc.frequency.value = freq;
  // ゲインの減衰で音の衰えを表現
}
```
外部ファイル不要でサウンドを生成。

## レスポンシブ対応
- デスクトップ: キーボード操作
- モバイル（600px以下）: タッチボタン表示
- キャンバスサイズ自動調整

## ブラウザ対応
- Chrome / Edge / Firefox / Safari（最新版）
- Web Audio API対応ブラウザ必須

## GitHub Pages
- リポジトリ: `https://github.com/morikawa001/seminar_test`
- Pages URL: `https://morikawa001.github.io/seminar_test/`
