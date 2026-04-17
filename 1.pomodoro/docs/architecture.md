# アーキテクチャ概要

> **現在のフェーズ**: フェーズ1（UIモック — 静的画面）

## 現在の構成

```
1.pomodoro/
├── app.py              # Flask アプリエントリポイント（未実装）
├── features.md         # 機能仕様・ロードマップ
├── templates/
│   └── index.html      # メイン画面 HTML テンプレート
└── static/
    └── css/
        └── style.css   # スタイルシート
```

## 技術スタック

| レイヤー | 技術 | 状態 |
|--------|------|------|
| バックエンド | Flask (Python) | 未実装 |
| フロントエンド | HTML / CSS | フェーズ1完了（静的モック） |
| JavaScript | - | 未実装（フェーズ2以降） |
| データベース | SQLite | 未実装（フェーズ3以降） |

## 今後の予定アーキテクチャ（`features.md` より）

フェーズ2以降で以下の層が追加される予定：

```
routes/api.py           # REST API エンドポイント
domain/models.py        # Settings, Session データモデル
domain/services.py      # バリデーション・集計ロジック
repositories/           # データアクセス層（SQLite CRUD）
static/js/
  ├── timer_engine.js   # 状態遷移ロジック
  ├── store.js          # アプリ状態管理
  ├── presenter.js      # DOM 更新
  ├── api_client.js     # API ラッパー
  └── app.js            # 初期化・イベント配線
```
