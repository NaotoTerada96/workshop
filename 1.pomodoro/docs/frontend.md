# フロントエンド ドキュメント

> **現在のフェーズ**: フェーズ1（UIモック — 静的画面）

## ファイル構成

```
templates/
└── index.html      # メイン画面 HTML テンプレート（Jinja2）

static/
└── css/
    └── style.css   # スタイルシート
```

---

## HTML テンプレート（`templates/index.html`）

Flask の Jinja2 テンプレートとして提供される単一ページのUIモックです。

### 画面構成

```
.window
├── .titlebar                    # タイトルバー（「ポモドーロタイマー」）
├── .timer-section               # タイマーセクション
│   ├── .mode-label              # モード表示（「作業中」）
│   ├── .progress-ring-wrapper   # 円形プログレスバー（SVG）
│   │   ├── circle.progress-ring__track   # 背景トラック
│   │   ├── circle.progress-ring__bar     # 進捗バー
│   │   └── .timer-display               # 残り時間表示（MM:SS）
│   └── .button-group            # ボタン群
│       ├── #btn-start           # 開始ボタン
│       └── #btn-reset           # リセットボタン
└── .stats-section               # 今日の進捗セクション
    ├── 完了数（静的: 4）
    └── 集中時間（静的: 1時間40分）
```

> **注意**: 現在の表示内容（残り時間 `25:00`、完了数 `4`、集中時間 `1時間40分`）はすべて静的なハードコード値です。JavaScript による動的更新はフェーズ2以降で実装予定です。

---

## スタイルシート（`static/css/style.css`）

### デザイントークン

| 用途 | 値 |
|-----|-----|
| 背景色（ページ） | `#7c6fcd`（紫） |
| アクセントカラー | `#6c63ff`（青紫） |
| ウィンドウ背景 | `#ffffff` |
| 進捗バーの色 | `#6c63ff` |
| トラック（背景リング）の色 | `#e8e8f0` |

### 主要コンポーネント

| クラス | 説明 |
|-------|------|
| `.window` | アプリウィンドウ枠（幅 320px、角丸 16px） |
| `.titlebar` | タイトルバー |
| `.timer-section` | タイマー表示エリア |
| `.progress-ring-wrapper` | 円形プログレス SVG のコンテナ（160×160px） |
| `.progress-ring__track` | プログレスリングの背景トラック |
| `.progress-ring__bar` | プログレスリングの進捗バー（`stroke-dasharray: 414.69`、半径 66px の周長） |
| `.timer-display` | MM:SS 形式の残り時間テキスト（中央オーバーレイ） |
| `.btn--primary` | 開始ボタン（紫背景） |
| `.btn--secondary` | リセットボタン（白背景・枠線） |
| `.stats-section` | 今日の進捗カード（薄紫背景） |

### 円形プログレスバーの仕様

SVG の `<circle>` 要素に `stroke-dasharray` / `stroke-dashoffset` を使用して円形の進捗を表現します。

- 半径 `r = 66px`
- 周長 = `2π × 66 ≈ 414.69`（`stroke-dasharray` の値）
- `stroke-dashoffset = 0` で 100% 表示、`414.69` で 0% 表示
- `transform="rotate(-90 80 80)"` により 12時方向から開始

---

## 予定されている JavaScript モジュール（`features.md` より、フェーズ2以降）

| ファイル | 役割 |
|--------|------|
| `static/js/timer_engine.js` | 状態遷移ロジック（start/pause/resume/reset/finish） |
| `static/js/store.js` | アプリ状態の一元管理 |
| `static/js/presenter.js` | DOM 更新（タイマー・プログレスリング・ボタン） |
| `static/js/api_client.js` | バックエンド API ラッパー |
| `static/js/app.js` | 初期化・イベント配線 |
