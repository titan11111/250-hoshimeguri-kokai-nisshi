# 250-hoshimeguri-kokai-nisshi 学び

## 2026-09-20 フォルダに作品名を付けた

`250-day074` は日付の仮名で、中身が「星めぐり航海日誌」だとフォルダ一覧から読めない。番号250は維持し、`250-hoshimeguri-kokai-nisshi` にした。

- 本体は `index.html` のまま
- リポジトリ横断の参照は `250-day074` ゼロ件だったので、置換対象はフォルダ名のみ
- 親リポジトリでは未追跡（`??`）のまま

## 2026-09-20 25%ファミコン風操作盤

- `titans-ui` の FC iOS Controller をゲームフォルダへコピー（Pages単体公開のため）。`#game-shell` / `#game-stage` / `#screen-wrap` / `#control-deck` の 75/25。
- 十字＝移動、A＝記録/着陸、B＝撮影、SELECT＝加速、START＝ポーズ。ミュートはポーズ内と `m` キー。保存キーは `tg.250.mute`（航海記録 `hoshimeguri.v1` は維持）。
- SELECT/START を 48px 以上に拡げ、操作盤の `padding-bottom` を 8px 以上にした。公式CSSのままではハーネスのボタン下限と余白下限を割る。
- Pointer Events + `setPointerCapture`。描画先は `#screen-wrap`。
- harness PASS: `docs/harness-reports/250-hoshimeguri-kokai-nisshi-2026-09-20T04-26-39-652Z.md`（操作盤 25.0%、非重複、ボタン適合、RAF 36）。十字が画面端で欠けないよう操作盤内サイズを下げた。iPhone実機は未実施。

## 2026-09-20 星空を3枚重ねにした

- 点群（最大3000）をやめ、遠／中／近の星空キャンバス3枚を `#star-stack` に重ねた。WebGLは `alpha:true` でその上に惑星だけ描く。
- 視点の回転で3枚をずらす。手前の層だけ明るさを揺らす。大気のある地表では重ねを隠す。
- 3枚をWebGLのスフィアにすると全面加算が3回走り、ハーネスが 22 RAF/秒で落ちた。DOM重ねに変えて 33 RAF/秒で PASS。
- harness PASS: `docs/harness-reports/250-hoshimeguri-kokai-nisshi-2026-09-20T04-32-33-368Z.md`。iPhone実機は未実施。

## 2026-09-20 オフライン起動と公開

- Three.js を CDN から読んでいたため、通信が切れると「機関始動…」のまま止まる。`244` と同じ r128 を `three.min.js` として同梱し、起動はローカル読み込みだけにした。
- Google Fonts も外し、Hiragino 系のシステムフォントにした。外部URL参照はゼロ。
- タイトル／星図の主要ボタンは `pointerdown`。日誌写真は `data:image/` だけ表示し、名前はエスケープする。
- `#loading{display:flex}` が `hidden` 属性の `display:none` を上書きし、起動後も「機関始動…」が被さっていた。`#loading[hidden]` を `display:none!important` にした。
- ハーネスの描画ループが負荷で 28 RAF/秒になることがある。WebGL の antialias を切り、pixelRatio 上限を 1.5 にした（撮影用の preserveDrawingBuffer は維持）。
- harness PASS: `docs/harness-reports/250-hoshimeguri-kokai-nisshi-2026-09-20T05-38-08-446Z.md`（60 RAF/秒）。iPhone 16 シミュレータの Safari でタイトル画面（地球・出港する）を確認。実機は未実施。
- 公開ブロッカーになる未確定事項は SPEC に無し。

## 2026-09-20 操作性改修と iOS 公開

- 十字の左右が地表で「平行移動＋視点回転」を同時に踏んで滑っていた。地表は左右＝視点旋回のみにした。
- 航行・周回は画面ドラッグを旋回に使う。チェイスカメラが `IN.look` を捨てていたので、スマホで空をなぞっても曲がらなかった。
- 自動補正が `IN.side` だけを見ていて、旋回入力中も針路を戻していた。`IN.turn` とドラッグ量を見るようにした。
- HUD・星図・ポーズが `click` のままだったので `pointerdown` + `setPointerCapture` に揃えた。スマホ幅で消していた操作ヒントを復活。
- SELECT/START の 5px 表記をやめて「加速／停止」。A/B に「記録／撮影」。十字の連続バイブを方向変化時だけにした。
- ブラウザで出港→月／火星／地球を3回通した。ハーネス PASS 4回: `docs/harness-reports/250-hoshimeguri-kokai-nisshi-2026-09-20T10-37-41-717Z.md` / `...T10-37-50-375Z.md` / `...T10-37-53-208Z.md` / `...T10-41-17-088Z.md`（62 RAF/秒・操作盤25.0%・ボタン適合）。iPhone 16 シミュレータの Safari でタイトル＋操作盤（加速／停止／記録／撮影）を確認。実機は未実施。
