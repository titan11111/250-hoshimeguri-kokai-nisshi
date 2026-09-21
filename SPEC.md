# 星めぐり航海日誌 — 仕様

## 0. ドキュメント情報
- 対象ゲーム: 250-hoshimeguri-kokai-nisshi
- 作成日: 2026-09-20
- 更新日: 2026-09-21
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
- 十字: 航行は機首/旋回。地表は前後移動＋左右で視点旋回（平行移動と重ねない）
- 航行中の画面ドラッグ: 旋回（チェイスカメラが視点を上書きするため）
- A: 記録 / 着陸
- B: 撮影
- 加速（旧SELECT）: 加速。地表ではジャンプ
- 停止（旧START）: ポーズ
- HUD・星図・ポーズのボタンは pointerdown + setPointerCapture
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

## 5-2. 天体の見え方（2026-09-21 改修）

- 惑星テクスチャは**球面座標で3Dノイズを引いて焼く**（UVで引くと極で模様が潰れ赤道で伸びる）。解像度 768×384、天体ごとに1回だけ生成してキャッシュ
- 種別ごとの作り分け
  - `rock`（月・水星・火星・フォボス）: 大地形＋尾根＋クレーター（椀・縁・中央丘・放出物）。月は海（マリア）、火星は明暗のアルベド模様と極冠、水星は高密度クレーター
  - `ice`（エウロパ等）: なめらかなノイズの**等高線**をリネア（氷殻の裂け目）として拾う
  - `earth`: ひねった大陸＋緯度と標高で植生・砂漠・雪、極冠、雲を焼き込む。海は約7割
  - `band`（木星〜海王星）: 緯度の帯をノイズで歪めたゾーナルな流れ＋渦（大赤斑・白斑・大暗斑）
  - `cloud`（金星・タイタン）: 地表を見せない霞。金星はダークY
  - `star`（太陽）: 粒状斑（粒と暗いレーン）・超粒状斑・黒点群（暗部＋半暗部）
- 凹凸は **bumpMap**（岩と氷のみ）。大地形ではなく細部だけを焼き、クレーターが光の当たり方で立つようにする
- 材質は **MeshPhongMaterial**（Standardの物理BRDFは1画素あたりが重い）。アルベドは `sRGBEncoding`
- 大気は**フレネルの縁光り**（`ShaderMaterial`）。大気のある星（地球・ガス惑星・金星/タイタン・火星）だけに付け、月や水星には付けない
- 太陽は**周縁減光**付きのシェーダー（中央が明るく縁が暗い）

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
- 9天体の周回（`enterOrbit`）と7天体への着陸（`startDescend`→`surface`）でコンソールエラー0件
- 出港 → 星図 → 出発で航行が始まる
- 操作盤 25%・ボタン 48px 以上
- harness PASS
- 合計サイズ 20MB 以下

## 11. 未確定事項
- なし（公開ブロッカーなし）
