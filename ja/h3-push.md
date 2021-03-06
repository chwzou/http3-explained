# HTTP/3 サーバープッシュ

HTTP/3 サーバープッシュは HTTP/2 で説明されているもの ([RFC7540](https://httpwg.org/specs/rfc7540.html)) と似ていますが、異なるメカニズムを利用します。

サーバープッシュは、クライアントからのリクエストがなくても返すことができるレスポンスです。

サーバープッシュはクライアント側が合意した場合にのみ受け取ることが可能です。

加えて HTTP/3 では、クライアントがサーバーに対してプッシュストリームの最大 ID を通知することでプッシュをいくつまで受け入れ可能か制限を設定しています。この制限を超えると、コネクションエラーとなります。

サーバーが、クライアントからの要求はなかったが、いずれ必要になる追加のリソースが他にもあると判断する場合、レスポンスがプッシュであることを示す `PUSH_PROMISE` フレームをリクエストフレームを通して送ることができ、その後実際のレスポンスを新しいストリーム上で送信します。

予めクライアントによってプッシュの受け入れが可能であると通知されている場合であっても、それぞれのプッシュストリームはクライアント側の判断によって拒否することができます。

その際には `CANCEL_PUSH` フレームがサーバーへ送信されます。

## 問題点

サーバープッシュは HTTP/2 の開発時に当初議論され、プロトコルの策定の後インターネットへと展開されたにもかかわらず、その有用性について数えきれないほどの議論が行われ、叩かれ嫌われ続けてきました。

プッシュは決して "無料" ではありません。ラウンドトリップの半分を削減することができますが、それでも帯域を使うことに変わりないからです。サーバー側にとって、リソースが実際にプッシュされるべきかどうかをハイレベルで確実に判断することは非常に難しく、おおよそ不可能です。
