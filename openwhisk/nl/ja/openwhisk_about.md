---

 

copyright:

  years: 2016

 

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# {{site.data.keyword.openwhisk}} の概要

*最終更新日: 2016 年 2 月 22 日*

以下のセクションでは、{{site.data.keyword.openwhisk}} について詳しく説明します。
{: shortdesc}

## {{site.data.keyword.openwhisk_short}} の動作
{: #openwhisk_how}

{{site.data.keyword.openwhisk_short}} は、イベントまたは直接起動に応えてコードを実行する、イベント・ドリブンの計算プラットフォームです。

次の図は、{{site.data.keyword.openwhisk_short}} アーキテクチャーの概要を示したものです。

![{{site.data.keyword.openwhisk_short}} アーキテクチャー](OpenWhisk.png)

イベントの例には、データベース・レコードへの変更、IoT センサーによる一定の気温を超えたことの感知、GitHub リポジトリーへの新規コードのコミット、Web アプリまたはモバイル・アプリからの単純な HTTP 要求などがあります。外部および内部のイベント・ソースからのイベントは、トリガーを通じてチャネル設定され、ルールによって許可されたアクションがこれらのイベントに対応します。

アクションは、Javascript または Swift の小さなコード断片であるか、Docker コンテナーに組み込まれたカスタム・バイナリーであることが可能です。{{site.data.keyword.openwhisk_short}} のアクションは、トリガーが発生するとすぐにデプロイされて実行されます。発生するトリガーが多いほど、起動されるアクションは多くなります。トリガーがまったく発生しない場合、実行されるアクション・コードはなく、したがってコストはかかりません。

アクションをトリガーと関連付けることに加えて、{{site.data.keyword.openwhisk_short}} API、CLI、または iOS SDK を使用することによってアクションを直接起動することも可能です。一連のアクションを、コードを何も書く必要なくチェーニングすることもできます。チェーン内の各アクションは順に起動され、あるアクションの出力は次のアクションへの入力として順に渡されていきます。

従来型の長時間稼働する仮想マシンまたはコンテナーを使用する場合、複数の VM またはコンテナーをデプロイして、1 つのインスタンスで障害が起こっても回復できるようにすることが一般的です。しかし、{{site.data.keyword.openwhisk_short}} はそれに代わるモデルとして、回復力に関連する余分なコストのないモデルを提供します。アクションがオンデマンドで実行されることにより、実行アクション数は常にトリガー率と一致するため、特有の拡張容易性および最良の使用効率がもたらされます。さらに、開発者は自分のコードにのみ集中でき、モニターやパッチを気にしたり、基盤サーバー、ストレージ、ネットワーク、およびオペレーティング・システム・インフラストラクチャーの保護について考えたりする必要はありません。

さらに多くのサービスおよびイベント・プロバイダーとの統合をパッケージで追加することができます。パッケージとは、フィードおよびアクションを束ねたものです。フィードとは、外部イベント・ソースを構成してトリガー・イベントを発生させるコード断片です。例えば、Cloudant 変更フィードで作成されるトリガーは、文書が変更されるか Cloudant データベースに追加されるたびにそのトリガーが発生するようにサービスを構成します。パッケージ内のアクションは、再使用可能なロジックを表します。サービス・プロバイダーがアクションを利用可能にすることによって、開発者はそのサービスをイベント・ソースとして使用できるだけでなく、そのサービスの API を起動することもできます。

既存のパッケージ・カタログを利用すると、素早く簡単に、有用な機能でアプリケーションを強化したり、エコシステム内の外部サービスにアクセスしたりできます。{{site.data.keyword.openwhisk_short}} 対応の外部サービスの例として、Cloudant、The Weather Company、Slack、GitHub などがあります。


## 一般的なユース・ケース
{: #openwhisk_use_cases}

{{site.data.keyword.openwhisk_short}} が提供する実行モデルは、さまざまなユース・ケースをサポートします。以下のセクションで、典型的な例を示します。

### アプリケーションをマイクロサービスに分解する
{{site.data.keyword.openwhisk_short}} のモジュラー性と本質的な拡張容易性は、小さなロジック断片をアクションに実装するのに向いています。例えば、{{site.data.keyword.openwhisk_short}} は、負荷が大きくて潜在的な難しさのある (バックグラウンド) タスクをフロントエンド・コードから除去して、それらのタスクをアクションとして実装するのに役立ちます。

### モバイル・バックエンド
多くのモバイル・アプリケーションでは、サーバー・サイドのロジックが必要です。モバイル開発者のほとんどがサーバー・サイド・ロジックの管理経験に乏しく、デバイス上で実行されるアプリに専念できるようにしたい場合は、{{site.data.keyword.openwhisk_short}} をサーバー・サイドのバックエンドとして使用することが良い解決策になります。さらに、Swift のサポートが組み込まれていることにより、開発者は既に持っている iOS プログラミング・スキルを再利用できます。

### データ処理
現在使用可能なデータ量に加えて、アプリケーション開発では、新規データを処理する能力と、将来的にそれに対応できる能力を必要とします。この要件には、構造化されたデータベース・レコードの処理と、構造化されていない文書、イメージ、またはビデオの処理の両方が含まれます。

### IoT
モノのインターネットは、その性質上、センサーで駆動されることがよくあります。例えば、一定の気温を超えたセンサーに反応することが必要な場合に、{{site.data.keyword.openwhisk_short}} のアクションが起動されるといったことが考えられます。



