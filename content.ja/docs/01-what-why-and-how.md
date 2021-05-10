---
title: 何を、なぜ、どのように
type: docs
weight: 2
---


# WebRTCとは？

WebRTC とは、Web Real-Time Communication の略で、API であると同時にプロトコルでもあります。WebRTC プロトコルは、2 つの WebRTC エージェントが双方向の安全なリアルタイム通信をネゴシエートするための一連のルールです。WebRTC API は、開発者が WebRTC プロトコルを使用するためのものです。WebRTC API は、JavaScript のみで規定されています。

似たような関係として、HTTP と fetch API があります。プロトコルの WebRTC が HTTP で、API の WebRTC が fetch API となります。

WebRTC プロトコルは、JavaScript 以外の API/言語でも利用可能です。また、WebRTC 用のサーバーやドメイン固有のツールもあります。これらの実装はすべて WebRTC プロトコルを使用しており、相互にやりとりできます。

WebRTC プロトコルは、IETF の[rtcweb](https://datatracker.ietf.org/wg/rtcweb/documents/)というワーキンググループで管理されています。WebRTC API は、W3C の[webrtc-pc](https://w3c.github.io/webrtc-pc/)で文書化されています。

## なぜWebRTCを学ぶ必要があるのか？

WebRTC を使うことで得られるものは以下の通りです。このリストはすべてを網羅しているわけではありませんが、あなたが旅をしている間に感謝することもあるでしょう。これらの用語のいくつかをまだ知らなくても、この本が教えてくれますのでご安心ください。

* オープンスタンダード
* 複数の実装
* ブラウザで利用可能
* 必須の暗号化
* NAT トラバーサル
* 既存技術の再利用
* 輻輳（ふくそう）制御
* 1秒未満のレイテンシー

## WebRTCプロトコルは他の技術の集合体である

これは、本 1 冊分の説明が必要なテーマです。しかし、ここでは 4 つのステップに分けて説明します。

* シグナリング
* 接続
* セキュリティの確保
* 通信

この 4 つのステップは順番に行われます。前のステップが 100％成功しなければ、次のステップは始まりません。

WebRTC の特徴の一つは、各ステップが実のところ他の多くのプロトコルによって構成されているということです。WebRTC を形作るために、多くの既存技術が組み合わされています。そういった意味で、WebRTC は 2000 年代初頭から存在する、よく理解された技術を組み合わせて構成したものと言えます。

これらのステップはそれぞれ専用の章を設けていますが、まずは大まかに理解しておくと便利です。これらの手順はお互いに依存しているため、それぞれの手順の目的をさらに説明する際に役立ちます。

### シグナリング: WebRTCでピアがお互いを見つける方法

WebRTC エージェントが起動したとき、WebRTC エージェントは誰と何の通信をするのかを知りません。この問題を解決するのがシグナリングです。シグナリングは、2 つの WebRTC エージェントが通信を開始できるようにするために使用されます。

シグナリングは、既存のプロトコルである SDP（Session Description Protocol）を使用します。SDP はプレーンテキストのプロトコルです。各 SDP メッセージは、キー／バリューペアで構成され、「メディアセクション」のリストを含んでいます。2 つの WebRTC エージェント間で交換する SDP には、以下のような詳細情報が含まれています。

* エージェントが到達可能な IP およびポート (候補)
* エージェントが送信しようとしているオーディオおよびビデオトラックの数
* 各エージェントがサポートするオーディオおよびビデオコーデック
* 接続時に使用される値 (uFrag/uPwd)
* セキュリティ確保時に使用される値 (証明書のフィンガープリント)

シグナリングは通常 WebRTC プロトコルの与り知らないところで行われることに注意してください。つまり、アプリケーションは通常、WebRTC 自体を使用してシグナリングメッセージを交換することはありません。接続しようとしているピア間ではメッセージの送信に適するような任意のアーキテクチャを使用して SDP を中継することができ、多くのアプリケーションは既存のインフラストラクチャ (REST エンドポイント、WebSocket 接続、または認証プロキシなど) を使用して、適切なクライアント間で SDP を円滑に交換できるようにしています。

### STUN/TURN による接続と NAT トラバーサル

これで、2 つの WebRTC Agent は、お互いに接続を試みるのに十分な詳細を知ることができました。WebRTC は ICE という別の確立された技術を使用します。

ICE（Interactive Connectivity Establishment）は、WebRTC よりも前のプロトコルです。ICE では、2 つの Agent 間で接続を確立できます。これらの Agent は、同じネットワーク上にある場合もあれば、地球の反対側にある場合もあります。ICE は、中央のサーバーを使わずに直接接続を確立するためのソリューションです。

それを可能にしているのは、「NAT トラバーサル」と「STUN/TURN サーバー」です。この 2 つの概念があれば、別のサブネットにいる ICE エージェントと通信できます。これらのトピックについては、後ほど詳しく説明します。

ICE が接続に成功すると、WebRTC は暗号化トランスポートの確立に進みます。このトランスポートは、音声、ビデオ、データに使用されます。


### DTLS と SRTP によるトランスポート層のセキュリティ確保

ICE による双方向通信が可能になったところで、安全な通信を確立する必要があります。これには、WebRTC よりも古い 2 つのプロトコルを使用します。1 つ目のプロトコルは DTLS（Datagram Transport Layer Security）で、これは UDP 上の TLS に過ぎません。TLS は、HTTPS での通信を保護するために使用される暗号化プロトコルです。2 つ目のプロトコルは SRTP（Secure Real-time Transport Protocol）です。

まず、WebRTC は ICE で確立された接続の上で DTLS ハンドシェイクを行って接続します。HTTPS とは異なり、WebRTC は証明書に中央機関を使用しません。代わりに、WebRTC は、DTLS を通じて交換された証明書が、シグナリングによって共有されたフィンガープリントと一致することを表明します。この DTLS 接続は、DataChannel メッセージに使用されます。

WebRTC は、RTP と呼ばれるオーディオ／ビデオ伝送用の別のプロトコルを使用します。RTP パケットのセキュリティには SRTP を使用します。SRTP セッションは、ネゴシエートされた DTLS セッションからキーを抽出して初期化します。後の章では、メディア伝送に独自のプロトコルが必要な理由について説明します。

これで完了です。双方向の安全な通信が可能になりました。WebRTC エージェント間の接続が安定していれば、これ以上の複雑な作業は必要ありません。しかしながら、現実の世界ではパケットロスや帯域幅の制限があるため、WebRTC エージェントがそれらに対処する方法を次のセクションで説明します。

### RTP および SCTP によるピアとの通信

これで 2 つの WebRTC Agent が安全な双方向通信を行うことができました。それでは早速、通信を開始しましょう。ここでも、既存の 2 つのプロトコルを使用します。RTP（Real-time Transport Protocol）と SCTP（Stream Control Transmission Protocol）です。SRTP で暗号化されたメディアのやり取りにはRTPを、DTLS で暗号化された DataChannel メッセージの送受信には SCTP を使用します。

RTP は最小限の機能しか備えていませんが、リアルタイムストリーミングを実現するために必要な機能を備えています。重要なのは、RTP が開発者に柔軟性を与えていることで、開発者はレイテンシー、ロス、輻輳を思い通りに処理できます。この点については、「メディア」の章で詳しく説明します。

スタックの最後のプロトコルは SCTP です。SCTP では、メッセージの配信オプションが多数用意されています。オプションで、信頼性のない、順番のない配信を選択できますので、リアルタイムシステムに必要なレイテンシーを得ることができます。

## プロトコルの集合体であるWebRTC

WebRTC は多くの問題を解決していて、一見過剰な技術とさえ思えるかもしれません。しかし、WebRTC のとてもすばらしいところは、実に謙虚だというところです。WebRTC は、自前ですべてをうまく解決できるとは考えませんでした。その代わり、多くの既存の単一目的の技術を採用し、それらを束ねることにしました。

これにより、私たちは圧倒されることなく、各部分を個別に検討し、学ぶことができるのです。「WebRTC エージェント」とは、実際には多くの異なるプロトコルを組み合わせたものに過ぎないということです。

![WebRTC Agent](/images/01-webrtc-agent.png "WebRTC Agent Diagram")

## WebRTC (API) はどのように動作するか

このセクションでは、JavaScript の API がプロトコルにどのように対応するかを示します。これは、WebRTC API の広範なデモを意味するものではなく、すべてがどのように結びついているかのメンタルモデルを作成するためのものです。
どちらにも慣れていない方でも問題ありません。このセクションは、より多くのことを学ぶために戻ってくる楽しみがあるかもしれません。

#### `new RTCPeerConnection`

`RTCPeerConnection` は、トップレベルの「WebRTC セッション」です。これには上述のすべてのプロトコルが含まれています。サブシステムはすべて割り当てられていますが、まだ何も起こりません。

#### `addTrack`

`addTrack` は新しい RTP ストリームを作成します。このストリームには、ランダムな同期ソース (SSRC) が生成されます。このストリームは、メディアセクション内の `createOffer` で生成されたセッション記述の中に入ります。 `addTrack` を呼び出すたびに、新しい SSRC とメディアセクションが作成されます。

SRTP セッションが確立されるとすぐに、これらのメディアパケットは SRTP で暗号化された後、ICE 経由で送信され始めます。

#### `createDataChannel`

`createDataChannel` は、SCTP アソシエーションが存在しない場合に、新しい SCTP ストリームを作成します。デフォルトでは、SCTP は有効ではなく、一方の側がデータチャネルを要求したときにのみ開始されます。

DTLS セッションが確立された直後に、SCTP アソシエーションは ICE を経由して DTLS で暗号化されたパケットの送信を開始します。

#### `createOffer`

`createOffer` は、リモートピアと共有するローカルステートの Session Description を生成します。

`createOffer` を呼び出しても、ローカルピアは何も変わりません。

#### `setLocalDescription`

`setLocalDescription` は要求されたすべての変更をコミットします。`addTrack`, `createDataChannel` などの呼び出しは、この呼び出しまではすべて一時的なものです。 `setLocalDescription` は `createOffer` で生成された値で呼び出されます。

通常、この呼び出しの後、リモートピアにオファーを送信し、リモートピアはそれを使って `setRemoteDescription` を呼び出します。

#### `setRemoteDescription`

`setRemoteDescription` は、リモート候補の状態をローカルエージェントに通知する方法です。これは、JavaScript の API で「シグナリング」という行為を行う方法です。

双方で `setRemoteDescription` が呼び出されると、WebRTC エージェントは P2P 通信を開始するのに十分な情報を得ることができます!

#### `addIceCandidate`

`addIceCandidate` を使うと、WebRTC エージェントはいつでも好きなときにリモートの ICE 候補を追加できます。この API は ICE サブシステムに ICE Candidate を直接送信し、大規模な WebRTC 接続には他の影響を与えません。

#### `ontrack`

`ontrack` は、リモートピアから RTP パケットを受信したときに起動されるコールバックです。受信パケットは、`setRemoteDescription` に渡されたセッション記述で宣言されているはずです。

WebRTC は SSRC を使用して、関連する `MediaStream` と `MediaStreamTrack` を検索し、これらの詳細が入力された状態でこのコールバックを起動します。

#### `oniceconnectionstatechange`

`oniceconnectionstatechange` は、ICE エージェントの状態を反映して起動されるコールバックです。ネットワークに接続されたときや、切断されたときに、このように通知されます。

#### `onstatechange`

`onstatechange` は、ICE エージェントと DTLS エージェントの状態を組み合わせたものです。これを見ることで、ICE と DTLS の両方が正常に完了したときに通知を受けることができます。