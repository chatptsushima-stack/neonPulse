# NEON PULSE — 配信用一式

ビルド不要の静的サイトです。このフォルダをそのまま公開すれば動きます。

```
index.html                 ゲーム本体（これ1枚で完結）
manifest.webmanifest       ホーム画面追加用
icon-192.png / icon-512.png / icon-maskable-512.png
vercel.json                キャッシュ設定
```

## Vercel に出す

**CLI（最短）**

```bash
cd neon-pulse-web
npx vercel        # 初回はログイン → 質問はすべてEnterでOK
npx vercel --prod # 本番URLに反映
```

フレームワークを聞かれたら **Other**、ビルドコマンドは空のままにします。

**GitHub 経由**

1. このフォルダの中身をリポジトリのルートに置いて push
2. Vercel で New Project → リポジトリを Import
3. Framework Preset は **Other**、Build Command は空、Output Directory は `.`

どちらでも `https://<プロジェクト名>.vercel.app` が発行され、そのURLを送れば他の端末から遊べます。HTTPS で配信されるので、後述の Wake Lock などもそのまま有効です。

## スマホで遊んでもらうときのポイント

- **ホーム画面に追加してもらうと一番快適です。** アドレスバーが消えて画面が広くなり、誤スワイプでページが戻る事故もなくなります（iOS: 共有 → ホーム画面に追加 / Android: メニュー → アプリをインストール）
- **iPhone のマナーモード対策済み。** Web Audio はサイレントスイッチONだと鳴らないので、無音ループを同時再生して再生セッションを起こしています
- **プレイ中は画面が消えません**（Screen Wake Lock）。非対応ブラウザでは自動的に無視されます
- **バックグラウンドに回ると自動で中断します。** 音声だけ進んで譜面とズレるのを防ぐためです
- **設定とハイスコアは端末ごとに保存されます**（localStorage、難易度別のベストスコア）
- 初回は必ず START のタップから始まります。iOS/Android とも、ユーザー操作なしには音を鳴らせないためです

## 遊ぶ人に伝えるといいこと

判定がズレて感じたら、タイトルの「オフセットを合わせる」を1回やってもらってください。端末とイヤホンの組み合わせで実際に数十msの差が出ます（特に Bluetooth イヤホンは 100〜200ms 遅れることがあるので、有線かスピーカー推奨）。

## 中身をいじる

`index.html` 冒頭の JS 定数まわりが調整ポイントです。

| 場所 | 何が変わるか |
|---|---|
| `BPM` | 曲と譜面の速さ（全体が連動します） |
| `SECTIONS` | 曲の構成とコード進行 |
| `PATTERNS` | メロディ＝譜面のリズム型。難易度ごとの密度もここ |
| `WIN` | 判定幅（秒） |
| `buildSong()` の `rnd` シード | 数値を変えると別の曲・別の譜面になります |

曲を差し替えたい場合は `buildSong()` が `{events, notes, duration}` を返す形さえ守れば、mp3 + 手打ち譜面にも置き換えられます。

## 他のホスティング

Netlify、Cloudflare Pages、GitHub Pages でも同じフォルダをそのまま置けば動きます（`vercel.json` は Vercel 以外では無視されます）。
