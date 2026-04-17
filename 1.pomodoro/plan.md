# ポモドーロタイマー 実装計画

## 全体の流れ

```
ステップ1  →  ステップ2  →  ステップ3  →  ステップ4  →  ステップ5  →  ステップ6  →  ステップ7
静的UI       エンジン単体   UIと結合      DB基盤        API結合        UX改善        テスト整備
（見た目）    （テスト済み）  （動くタイマー）（保存できる）  （永続化完成）
```

> ステップ3完了時点で「動くポモドーロタイマー」として最低限使える状態になる。

---

## ステップ1: 静的UI（見た目の確認）

**目標**: ブラウザでUIモックと同じ画面を表示できる状態

- [ ] `templates/index.html` — 基本構造（タイトル、ラベル、ボタン、進捗セクション）
- [ ] `static/css/style.css` — レイアウト・配色・フォント（モック再現）
- [ ] `app.py` — Flaskで `/` を返すだけの最小構成
- [ ] 円形プログレスバー — SVGで静的描画（固定値で表示確認）

**完了基準**: `flask run` でUIモックと同じ見た目が表示される

---

## ステップ2: タイマーエンジン（ロジック単体）

**目標**: UIと切り離した状態遷移ロジックを実装・テスト

- [ ] `static/js/timer_engine.js` — `reduce(state, action, now)` の純粋関数実装
  - `start / pause / resume / reset / finish` の各アクション
  - `targetAt - now` による残り秒数計算
- [ ] `tests/test_timer_engine.js` — 状態遷移・境界値テスト

**完了基準**: テストがすべてパスする

---

## ステップ3: タイマーUI結合

**目標**: ブラウザ上でタイマーが動作する（APIなし）

- [ ] `static/js/store.js` — アプリ状態の一元管理
- [ ] `static/js/presenter.js` — 残り時間テキスト・円形プログレス・ボタン状態のDOM更新
- [ ] `static/js/app.js` — 初期化・ボタンイベント配線・1秒インターバル処理
- [ ] `work → short_break → long_break` サイクルの画面反映

**完了基準**: 開始・一時停止・リセット・モード切替がブラウザで動作する

---

## ステップ4: バックエンド基盤

**目標**: SQLiteにデータを保存・取得できる

- [ ] `domain/models.py` — `Settings`, `Session` データクラス定義
- [ ] `repositories/settings_repository.py` — 設定のCRUD（SQLite）
- [ ] `repositories/sessions_repository.py` — セッション記録のCRUD（SQLite）
- [ ] `app.py` — DB初期化・マイグレーション処理追加

**完了基準**: DBが自動作成され、デフォルト設定が保存される

---

## ステップ5: APIと画面の結合

**目標**: フロントとバックが連携し、進捗が永続化される

- [ ] `domain/services.py` — バリデーション・日次集計ロジック
- [ ] Flask APIエンドポイント実装（4本）
  - `GET /api/settings`
  - `PUT /api/settings`
  - `GET /api/stats/today`
  - `POST /api/sessions/complete`
- [ ] `static/js/api_client.js` — fetchラッパー実装
- [ ] `app.js` — 起動時に設定取得、完了時にセッション記録、進捗表示を更新

**完了基準**: リロード後も今日の完了数・集中時間が正しく表示される

---

## ステップ6: UX改善

**目標**: 実用的な使い心地に仕上げる

- [ ] セッション完了時のブラウザ通知（Notification API）
- [ ] 設定ダイアログ（作業時間・休憩時間の変更UI）
- [ ] 自動スタートオプションの動作実装

**完了基準**: 通知が届き、設定変更がタイマーに反映される

---

## ステップ7: テスト拡充

**目標**: 主要ロジックの自動テストを整備

- [ ] `tests/test_domain_services.py` — バリデーション・集計ロジック
- [ ] `tests/test_api.py` — 正常系・異常系のAPIテスト

**完了基準**: 主要パスの自動テストがすべてパスする

---

## 参考: ディレクトリ構成

```text
1.pomodoro/
  app.py
  features.md
  plan.md
  templates/
    index.html
  static/
    css/
      style.css
    js/
      app.js
      timer_engine.js
      presenter.js
      api_client.js
      store.js
  domain/
    models.py
    services.py
  repositories/
    settings_repository.py
    sessions_repository.py
  tests/
    test_timer_engine.js
    test_domain_services.py
    test_api.py
```
