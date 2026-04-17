# ポモドーロタイマー 機能一覧

## フェーズ1: UIモック再現（静的画面）

- [ ] メイン画面レイアウト（HTML/CSS）
  - タイトル「ポモドーロタイマー」
  - モード表示ラベル（作業中 / 休憩中）
  - 円形プログレスバー
  - 残り時間表示（MM:SS形式）
  - 開始・リセットボタン
  - 今日の進捗セクション（完了数・集中時間）

---

## フェーズ2: タイマーエンジン（フロントエンド）

- [ ] **timer_engine.js** — 状態遷移ロジック
  - `start / pause / resume / reset / finish` アクション処理
  - `targetAt - now` による残り時間計算（タブ復帰対応）
  - `work → short_break → long_break` サイクル管理
- [ ] **store.js** — アプリ状態の一元管理
  - `mode`, `status`, `startedAt`, `targetAt`, `remainingSec`, `completedCountToday`
- [ ] **presenter.js** — DOM更新
  - 残り時間テキスト更新
  - 円形プログレスバー描画（SVGまたはCanvas）
  - ボタン状態切り替え（開始/一時停止）
  - モードラベル更新
- [ ] **api_client.js** — APIラッパー
- [ ] **app.js** — 初期化・イベント配線

---

## フェーズ3: バックエンドAPI（Flask）

- [ ] **GET /api/settings** — 設定取得
- [ ] **PUT /api/settings** — 設定更新
- [ ] **GET /api/stats/today** — 当日進捗取得（完了数・集中時間）
- [ ] **POST /api/sessions/complete** — セッション完了記録
- [ ] **domain/models.py** — `Settings`, `Session` データモデル
- [ ] **domain/services.py** — バリデーション・集計ロジック
- [ ] **repositories/settings_repository.py** — 設定のCRUD
- [ ] **repositories/sessions_repository.py** — セッション記録のCRUD
- [ ] **SQLite初期化・マイグレーション** — DBスキーマ作成

---

## フェーズ4: UX・設定機能

- [ ] 設定ダイアログ（作業時間・休憩時間の変更）
- [ ] セッション完了時の通知（ブラウザ通知 or 音声）
- [ ] 自動スタートオプション（`auto_start_break`, `auto_start_work`）
- [ ] ロングブレーク間隔設定（`long_break_every`）

---

## フェーズ5: テスト

- [ ] **test_timer_engine.js** — 状態遷移・境界値テスト
- [ ] **test_domain_services.py** — バリデーション・集計テスト
- [ ] **test_api.py** — 正常系・異常系APIテスト

---

## 横断的な考慮事項

| 項目 | 内容 |
|------|------|
| Clock注入 | `Date.now` / `datetime.now` を抽象化してテスト容易化 |
| 純粋関数設計 | `reduce(state, action, now) → new_state` パターン |
| APIスキーマ固定 | フロント/バック間の契約テスト |
| セキュリティ | 入力バリデーション（範囲外値・型チェック） |
