# データモデル仕様

> **現在のフェーズ**: フェーズ1（UIモック — 静的画面）

データモデルはまだ実装されていません（フェーズ3以降で実装予定）。

## 予定されているデータモデル（`features.md` より）

### Settings（タイマー設定）

| フィールド | 型 | 説明 |
|----------|-----|------|
| work_duration | int | 作業時間（分） |
| short_break_duration | int | 短い休憩時間（分） |
| long_break_duration | int | 長い休憩時間（分） |
| long_break_every | int | 何セッションごとに長い休憩を挟むか |
| auto_start_break | bool | 作業終了後に自動で休憩を開始するか |
| auto_start_work | bool | 休憩終了後に自動で作業を開始するか |

### Session（ポモドーロセッション記録）

| フィールド | 型 | 説明 |
|----------|-----|------|
| id | int | セッション ID（主キー） |
| started_at | datetime | 開始日時 |
| completed_at | datetime | 完了日時 |
| mode | str | セッション種別（`work` / `short_break` / `long_break`） |

---

*モデルの実装が完了次第、このドキュメントに詳細なスキーマ・バリデーションルールを追記します。*
