[Google Cloud Next Tokyo ’23](https://cloudonair.withgoogle.com/events/next-tokyo) の2日目に参加しました。忘れないうちに感じたことなどをレポートします。


## ロケーション

### 会場: 東京ビッグサイト

https://www.bigsight.jp/

人生ではじめて東京ビッグサイトに来ました。お祭り感があり来場しただけでワクワクしました（準備がとても大変そう）。空調に期待しすぎないよう、外気温に合わせて厚着していったのがよかったです。それでもちょっと寒かった。

Google Cloud Next Tokyo ’23 は主に西棟で開催されていたのですが、東棟・南棟・西棟の位置関係は事前によく確認してから参加するのが良いです。

![](https://res.cloudinary.com/zenn/image/fetch/s--Fyyg99Ma--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/4b8d01afedae6589c3a7a5dc.png%3Fsha%3D46dbe3645c9a234c7b9162e1c9725b1dd6d68012)
*西棟で開催された Google Cloud Next Tokyo ’23*

### ノベルティなど

飲み物が無料でいただけるのがありがたかったです。ホットコーヒーがあるとなお嬉しかったかも。

### 認定者ラウンジ

![](https://res.cloudinary.com/zenn/image/fetch/s--Sc0D0eYw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/c9c12075ed29c9e25afc9dd5.png%3Fsha%3D3204c7812cded136d98e9e864ccf17a1fc8b0908)

ハンズオン（後述）で電源が確保できずギリギリだったため認定者ラウンジに駆け込んで充電できたのは助かりました。飲み物も完備。


## セッション

### ハンズオン: Google Cloud ではじめる生成 AI を活用したアプリケーション開発入門

[セッションリンク](https://cloudonair.withgoogle.com/events/next-tokyo?talk=d2-space1-1)（概要情報のみが残っています）

`Knowledgeドライブ` という、架空のサンプルアプリケーションをつくります。

- ログインできる
- ファイルをアップロードできる
- PDFファイルをアップロードすると、Cloud Functionsにより非同期でEmbeddingする
- 検索すると、登録したPDFをベースに回答を生成する
- どのファイルの何ページが回答のもとになっているかも表示する

このような機能をもつアプリをつくりました。Qwiklabs で進めていきます。

![](https://res.cloudinary.com/zenn/image/fetch/s--9vJOS9S4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/883916a0247572b559e96092.png%3Fsha%3De328e80c715d6a6c14896d2ccd32154c10b6ab2c)
*Google Cloud で始める生成 AI を活用したアプリケーション開発入門 - アプリ画面*

ハンズオンの資料が充実しており、迷うことなく進められました。1点だけ、コマンドベースで進めるとFirebaseとCloud Storageの連携がうまくできないケースがあり、それにハマりました。アプリからみるとファイルアップロード時にCORSエラーで失敗するような見た目になるため解決に時間がかかりました（サポートの方に質問したのですが私の聞き方がわるく解決には至らず）。

Cloud Storageの実体が作成されているにもかかわらず、Firebaseの管理画面をみたときにストレージがなかったため、GUIからストレージを設定し直したところ正常にアップロードできるようになりました。

アプリは、PDFをアップロードすると数秒で検索結果に生成AIの説明を表示できました。アプリと生成AIの連携を体験するという点で、有意義でした。

#### 感想

- ハンズオン会場は電源あります。しかし後方座席の一部は電源がないエリアもあるようでした。私はギリギリでの到着だったため電源なしの座席に座りました。致し方なし。
- 仕方ない事情もあるかもしれませんが、受講者の人数に対してサポート役の方の人数が少ないように思えました。
- PaLM API による Embeddings と、その結果から得られる生成テキストの変化を実際のアプリケーションで観察できるのが面白かったです。


### セッション: What's Next for Security?｜最前線のインテリジェンスと AI によるセキュリティの変革

[セッションリンク](https://cloudonair.withgoogle.com/events/next-tokyo?talk=d2-sec-03)

英語のセッションだったので不安でしたが、内容は生成AIでセキュリティを強化する [Google Cloud Security AI Workbench](https://cloud.google.com/blog/ja/products/identity-security/rsa-google-cloud-security-ai-workbench-generative-ai) の紹介だったと理解しています。

セキュリティ分野は生成AIの力が発揮できる分野のひとつであり、Google Cloud もそのために Sec-PaLM（セキュリティに特化した大規模言語モデル）を用意したとのこと。大きく3つの観点で役に立つとのことでした：

1. 最初の感染を抑え込み脅威の拡大を抑制
2. インテリジェンスの付加によるトイル（手作業）の削減
3. セキュリティ人材の育成

私はセキュリティに関して苦手意識があります。たとえば脅威監視のアラートで「攻撃を受けています」ということがわかったとして、**じゃあどこをどうすればいいのか**という具体的なアクションを起こすためにかなり理解と準備が必要、という点です。正直、CVEをみてもよくわからないことも多いので、すぐTwitterを見てます。みなさんはどういう解釈・アクションをしているんだろう…という具合で。Google Cloud では、このような状況に対し[Chronicle AI](https://cloud.google.com/blog/ja/products/identity-security/rsa-google-cloud-security-ai-workbench-generative-ai)を使い、セキュリティイベントを会話形式で把握できるらしいです。私にとってはありがたい機能ですね。
#### 感想

- 把握からアクションまでの時間を短くできるという点で、セキュリティ分野への生成AIの応用は期待できる
- 「この脆弱性、実際どうなの？」がわかるとありがたい

セッションは画像のようにオープンスペースで行われました。

![](https://res.cloudinary.com/zenn/image/fetch/s--R6MWwVMq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/29aab9fe6d9d2c513a78b0ef.png%3Fsha%3D40a9a7bc47afba42c0d46df7b31f62e161bdbd63)


最初、これ喋ってる内容聞こえるんかな？と思いましたが視聴者全員にレシーバーが配布され、声はレシーバーにより聞きます。オープンスペースでお祭り感をだしつつ、音声は集中できるようにする上手な運用だなと思いました。

### セッション: BigQuery Analytics Hub で最先端の HR DaaS を実現した方法

[セッションリンク](https://cloudonair.withgoogle.com/events/next-tokyo?talk=d2-da-08)

[株式会社 HR Force](https://www.hr-force.co.jp/)さまによる [BigQuery Analytics Hub](https://cloud.google.com/analytics-hub?hl=ja) の事例です。

- HR（人材・採用）データは特殊な事情がある
  - 技術的停滞、オープンにしづらい
  - セキュリティが求められる
  - 社会的影響

とくに3点目の社会的影響については共感するところがあり、限られた人材を業界内で奪い合うのは不毛という主張、同意します。人材に頼り切るのではなくいろいろな手段を使って企業のパワーを上げていく方策が必要になるだろう、とのことです。

- BigQuery Analytics Hub がHRデータの提供で貢献できる点
  - IAMとは異なり組織に対して BigQuery のデータをシェアできる、publicにすることもできる
  - データを使う側もインタフェースはBigQueryを使えば良いので、提供する側はデータの収集と加工に集中できる
  - 提供データの複製可否も提供側が最初に設定できる

データを共有・提供するときは、要件によってどのくらい厳密に、またはゆるく共有するかさまざまな条件があるが、Analytics Hubでは柔軟に対応できるとのことでした。

#### 感想

- データを共有するときにBigQueryという共通のインタフェースでやりとりできるのは魅力だとおもった
- もう少し具体的な例（どのようなHRデータをどう加工して、どう提供してどう利用したか）があると嬉しかった


## ふりかえり

2日目だけでしたが楽しめました。今回のGoogle Cloud Next Tokyo ’23は、ハンズオンおよびセッションの内容ももちろんですが運営の観点で良いと思いました。セッションに会議室は使わずすべてオープンスペースで実施していました。それにより以下のメリットがありそうです：

- お祭り間が演出できる
- セッション移動の間にブースを通る導線設計になっており、各ブースも盛り上がっていた
  - ブースを通りたくない人のためのスルーパスも用意されている
- オープンスペースによる騒々しさが心配になるところだが、メインセッションではレシーバーが配られそれを通してリアルタイムに音声が聞こえるためまったく問題ない
- 飲み物に困らない
- 認定者ラウンジのブースにふらっと寄ることができ、でちょっとした作業ができる、ということがブースの外から見えるため、おそらく見た人は認定取得をモチベートされる

セッションは、やはり生成AIが盛り上がっているという印象で、具体的にどう活かすか、どう連携するかという話が増えてきています。アプリケーションの文脈で見ても実践的に取り入れられそうな要素もあり（PaLM、セキュリティなど）、プロダクションでの活用も視野に入ります。引き続きキャッチアップしていきたいと思います。
