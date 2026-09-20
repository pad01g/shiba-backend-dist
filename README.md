# shiba-backend-dist

「しばいぬのおつかい」の**接続先ポインタ**の配信先。GitHub Pages で配ります。

アプリは接続先をハードコードせず、まず `rendezvous.json` を読み、
**`endpoints` を上から順に試します**。置き場所を変えても
アプリを配り直さずに切り替えられます。

## endpoints の順番が意味を持ちます

| # | 何か | いつ使うか |
|---|---|---|
| 1 | `home` — Tailscale Funnel 経由の自宅サーバ | **本線。** 常にこれを先に試す |
| 2 | `worker` — Cloudflare Worker + Durable Object | **代役。** 自宅 PC が止まっているときだけ |

代役はまだ用意していません。用意したら 2 番目に足します。
本線を先に置くことで、Durable Objects の無料枠を消費するのは
自宅 PC が止まっている間だけになります。

## 部屋コードはパスに載ります

`ws` の値に部屋コードを足して繋ぎます。

```
wss://gateway.tail2668e8.ts.net/shiba/ws/ABC234
```

**クエリ文字列では渡しません。** Tailscale の serve/funnel が
WebSocket のアップグレード要求からクエリを落とすためです
([tailscale#18651](https://github.com/tailscale/tailscale/issues/18651))。

## iceServers

WebRTC の直結に使う STUN サーバ。**TURN は使いません。**
クレジットカード登録が必要になるためで、直結できない場合は
サーバ中継へ落とします (親リポジトリの `PLAN.md` §12)。
