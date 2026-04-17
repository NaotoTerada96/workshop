# Pomodoro Timer Webアプリ アーキテクチャ案

## 1. 目的
本ドキュメントは、Flask + HTML/CSS/JavaScriptで実装するポモドーロタイマーWebアプリのアーキテクチャ方針を定義する。
主な目的は次の3点。

- UIモックに沿った体験を最短で実現する
- 将来の機能追加に耐える構造にする
- ユニットテストしやすい構造を最初から採用する

## 2. 設計方針

- 単一画面で完結するシンプル構成から開始
- 責務分離を徹底し、UI/業務ロジック/永続化を分ける
- タイマー進行はクライアント主導、サーバーは設定と記録の管理に集中
- 時刻依存処理を抽象化し、時間起因のテストを容易にする

## 3. 全体構成（4層）

1. プレゼンテーション層（Browser）
- HTML/CSS: 画面構造と見た目
- JavaScript: 画面制御、タイマー状態管理、API呼び出し

2. API層（Flask）
- REST APIエンドポイント
- 入出力バリデーション
- ドメインサービス呼び出し

3. ドメイン層（Python/JavaScript）
- タイマー状態遷移
- セッション完了判定
- 日次集計ロジック

4. 永続化層（SQLite）
- 設定・セッション記録の保存
- Repository経由でアクセス

## 4. ディレクトリ案

```text
/workspaces/workshop/
  architecture.md
  1.pomodoro/
    app.py
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
      services.py
      models.py
    repositories/
      settings_repository.py
      sessions_repository.py
    tests/
      test_timer_engine.py
      test_domain_services.py
      test_api.py
```

補足:
- 初期は app.py に集約してよいが、ロジック増加時に domain と repositories を優先的に分離する。

## 5. フロントエンド設計

### 5.1 モジュール分割

- timer_engine.js
  - タイマーの状態遷移と残り時間計算を担当
  - 可能な限り純粋関数で実装
- store.js
  - 画面状態を1箇所で管理（単一ストア）
- presenter.js
  - DOM更新専任（表示文字列、円形プログレス、ボタン状態）
- api_client.js
  - API呼び出しを集約（fetchの直接呼び出しを他モジュールから排除）
- app.js
  - 初期化、イベント配線、モジュール間の調停

### 5.2 タイマー状態

- mode: work / short_break / long_break
- status: stopped / running / paused / finished
- startedAt: 実行開始時刻
- targetAt: 終了予定時刻
- remainingSec: 表示用導出値
- completedCountToday: 当日完了数

### 5.3 時間計算ルール

- running中は remainingSec を減算で保持しない
- targetAt - now で毎回再計算する
- タブ非アクティブ復帰時も正しい値を表示できる

## 6. バックエンド設計（Flask）

### 6.1 API責務

- 設定提供/更新
- 当日進捗取得
- セッション完了記録

### 6.2 API案

- GET /api/settings
- PUT /api/settings
- GET /api/stats/today
- POST /api/sessions/complete

### 6.3 サーバー側の原則

- 秒単位のリアルタイムカウントは持たない
- 完了イベントの記録と集計に責務を限定
- ルート関数にロジックを寄せず、サービス層へ委譲

## 7. データモデル案

### 7.1 settings

- id
- work_minutes
- short_break_minutes
- long_break_minutes
- long_break_every
- auto_start_break
- auto_start_work
- updated_at

### 7.2 sessions

- id
- mode（work/short_break/long_break）
- started_at
- ended_at
- planned_seconds
- actual_seconds
- completed（bool）

### 7.3 集計値（必要に応じて）

- daily_stats（ビューまたは計算）
  - date
  - completed_pomodoros
  - focus_seconds

## 8. ユニットテスト容易性を高める追加設計

### 8.1 Time abstraction（Clock注入）

- Date.now や datetime.now の直接利用を避ける
- Clockインターフェース経由で現在時刻を取得
- テストで固定時刻/任意時刻を注入可能にする

### 8.2 純粋関数ベースの状態遷移

- reduce(state, action, now) -> new_state を基本形にする
- start/pause/resume/reset/tick/finish をActionとして統一
- 期待値比較だけで分岐網羅しやすくなる

### 8.3 Port/Adapter（境界分離）

- フロント: TimerEngine -> StatsRepository（抽象）
- バック: Service -> SessionRepository（抽象）
- 実装差し替えでテストダブルを容易にする

### 8.4 Presenter分離

- Engineは状態を返すだけ
- PresenterがDOM反映を担当
- ロジックテストとUIテストを分離

### 8.5 契約テスト

- APIレスポンスのJSONスキーマを固定
- フロントとバックのインターフェース破壊を早期検知

## 9. テスト戦略

### 9.1 フロントユニット

- 状態遷移（start/pause/resume/reset/finish）
- 境界時刻（0秒、負値補正、切替直前）
- タブ復帰時の再計算

### 9.2 バックユニット

- 設定バリデーション
- セッション完了登録
- 日次集計ロジック

### 9.3 APIテスト

- 正常系: settings取得/更新、stats取得、session完了
- 異常系: 入力不正、欠損、範囲外値

## 10. 実装フェーズ

1. フェーズ1: UIモック再現（静的画面）
2. フェーズ2: TimerEngine実装（状態遷移 + 時間計算）
3. フェーズ3: Flask API接続（進捗表示と記録）
4. フェーズ4: 設定機能、通知、UX調整
5. フェーズ5: テスト拡充とリファクタリング

## 11. 今回の推奨初期スコープ

- 単一ユーザー、ローカル実行
- SQLite永続化
- 当日進捗（完了数、集中時間）
- 作業/休憩の基本サイクル

このスコープで最短リリースし、その後に認証、複数端末同期、統計拡張を段階追加する。