# 星めぐり航海日誌 — 仕様

## 0. ドキュメント情報
- 対象ゲーム: 250-hoshimeguri-kokai-nisshi
- 作成日: 2026-09-20
- 更新日: 2026-09-20
- ステータス: 公開準備
- 参照ファイル: index.html / fc-ios-controller.js / fc-ios-controller.css / three.min.js

## 1. ゲーム概要
- ジャンル: 3D航行・探索ログ
- 一言説明: 地球の宇宙港から光の順に太陽系を巡り、着陸して見て、日誌に書きとめる。
- 想定プレイ時間: 15〜40分
- 対象端末: iPhone Safari / GitHub Pages

## 2. 対象環境

### 必須
- 配信先: GitHub Pages
- 最優先端末: iPhone Safari
- 対応画面幅: 320px〜430px
- 実装方式: 静的 HTML / CSS / JS。Three.js r128 はローカル同梱

### 任意
- PC: キーボード（WASD・矢印・E記録・P撮影・L着陸・Shift加速・Enterポーズ）

## 3. 操作 / 設定UI

- 画面構成: 上75% `#game-stage` / 下25% `#control-deck`（ハーネス契約）
- 入力: Pointer Events + `setPointerCapture`
- 十字: 移動
- A: 記録 / 着陸
- B: 撮影
- SELECT: 加速
- START: ポーズ
- ミュート: ポーズ内「音の切り替え」と `m` キー。キー `tg.250.mute`
- 航海記録: `hoshimeguri.v1`（既存キー維持）
- 開始: タイトル「出港する」の pointerdown で AudioContext unlock

## 4. コアループ
- タイトル → 星図で行き先を選ぶ → 航行 → 周回観測 → 着陸探索 → 発見を日誌へ
- 達成率は到達・観測・着陸・発見の記録数
- 勝敗はなく、日誌が埋まることがクリア体験

## 5. 画面 / 状態遷移
- `boot` → `attract`（タイトル）→ `map` → `cruise` → `orbit` → `descend` → `surface`
- ポーズは航行中・地表で明示停止
- 日誌はどの画面からも開閉できる

## 6. 音声
- WebAudio。開始タップおよび最初の pointerdown で unlock
- 環境音ループ。ミュート時は出力オフ。状態は `tg.250.mute`

## 7. 保存
- `hoshimeguri.v1`: 記録と写真（data URL）
- `tg.250.mute`: ミュート

## 8. ファイル構成
```text
250-hoshimeguri-kokai-nisshi/
  index.html
  fc-ios-controller.js
  fc-ios-controller.css
  three.min.js
  SPEC.md
  LEARNINGS.md
```

## 9. 実装制約
- 公開実体 20MB 以下
- 外部 CDN 禁止（フォントもシステムフォント）
- iOS: viewport / safe-area / ダブルタップ防止 / 明示ポーズ

## 10. テスト項目
- タイトルが出る
- 出港 → 星図 → 出発で航行が始まる
- 操作盤 25%・ボタン 48px 以上
- harness PASS
- 合計サイズ 20MB 以下

## 11. 未確定事項
- なし（公開ブロッカーなし）
