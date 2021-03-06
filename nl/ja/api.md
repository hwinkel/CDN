---

copyright:
  years: 2017
lastupdated: "2018-01-26"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:download: .download}



# CDN API リファレンス

IBM クラウドで提供される {{site.data.keyword.BluSoftlayer_notm}} アプリケーション・プログラミング・インターフェース (通常 SLAPI と呼ばれる) は、開発者やシステム管理者が {{site.data.keyword.BluSoftlayer_notm}} バックエンド・システムと直接対話することができる開発用インターフェースです。

SLAPI は、カスタマー・ポータルにある多くの機能を実装しています。カスタマー・ポータルで対話が可能であれば、SLAPI でも実現できます。 SLAPI 内で {{site.data.keyword.BluSoftlayer_notm}} 環境のすべての部分とプログラムで対話できるため、API を使用してタスクを自動化することができます。

SLAPI は、リモート・プロシージャー・コール (RPC) システムです。 各呼び出しでは、API エンドポイントにデータが送信され、戻りとして構造化データを受け取ります。 SLAPI でのデータの送受信に使用される形式は、選択した API の実装によって異なります。 SLAPI は現在、データ伝送に SOAP、XML-RPC、または REST を使用しています。

SLAPI、または IBM Cloud Content Delivery Network (CDN) サービス API について詳しくは、IBM Cloud Development Network にある以下のリソースを参照してください。

* [SLAPI Overview](https://sldn.softlayer.com/article/softlayer-api-overview )
* [Getting Started with SLAPI](http://sldn.softlayer.com/article/getting-started )
* [SoftLayer_Product_Package API](http://sldn.softlayer.com/reference/services/SoftLayer_Product_Package )
* [PHP Soap API Guide](https://sldn.softlayer.com/article/PHP )

----

始めに、API 呼び出しの推奨手順を以下に示します。
* `listVendors` - サポートされているベンダーのリストを提供します。
* `verifyOrder` - 発注できるかどうかを確認します。
* `placeOrder`  - 指定のベンダーを使用して CDN アカウントを作成します。 placeOrder 呼び出しが正常に実行されると、最大 10 CDN マッピングが作成できます。
* `createDomainMapping` - CDN マッピングを作成します。
* `verifyDomainMapping` - CDN の状況を _RUNNING_ に変更します。

前述の手順に従った後、その他の API を使用できます。

[この呼び出し手順の各ステップについて、コード例が用意されています。](cdn-example-code.html#code-examples-using-the-cdn-api)

**注**: この文書にあるほとんどの API 呼び出しで、`CDN_ACCOUNT_MANAGE` アクセス権を備えたユーザーの API ユーザー名と API キーを使用する**必要があります**。このアクセス権を自分用に有効にする必要がある場合は、ご使用のアカウントのマスター・ユーザーにお問い合わせください。(各 IBM Cloud カスタマー・アカウントに 1 つのマスター・ユーザーが指定されます)。

----
## ベンダー用の API
### listVendors
この API で、ユーザーはサポートされている CDN ベンダーをリストできます。 CDN アカウントを作成し、CDN の注文を開始するには、`vendorName` が必要です。

* **必須パラメーター**: なし
* **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Vendor` のコレクション

  ベンダー・コンテナーと使用例は、[ベンダー・コンテナー](vendor-container.html)で表示されます。

----
## アカウント用の API
### verifyCdnAccountExists
指定された `vendorName` について、API を呼び出しているユーザーの CDN アカウントが存在するかどうかを確認します。

* **必須パラメーター**: `vendorName`: 有効な CDN プロバイダーの名前を指定します。
* **戻り**: アカウントが存在する場合は `true`、存在しない場合は `false`。

----
## ドメイン・マッピング用の API
### createDomainMapping
この関数は、提供された入力を使用して、指定のベンダー用のドメイン・マッピングを作成し、ユーザーの {{site.data.keyword.BluSoftlayer_notm}} アカウント ID と関連付けます。 CDN アカウントは、この API が動作するように、`placeOrder` を使用して最初に作成される必要があります ([コード例](cdn-example-code.html)の `placeOrder` API 呼び出しの例を参照してください)。CDN が正常に作成されると、`defaultTTL` が 3600 秒の値で作成されます。

* **パラメーター**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Configuration_Input` のコレクション。
  入力コンテナーのすべての属性を、
  [入力コンテナーの表示 (View the Input Container)](input-container.html) で表示することができます。

  次の属性は入力コンテナーの一部で、ドメイン・マッピングの作成時に指定できます (特に記載がない限り属性はオプションです)。
    * `vendorName`: **必須** 有効な IBM Cloud CDN プロバイダーの名前を指定します。
    * `origin`: **必須** オリジン・サーバー・アドレスを文字列として指定します。
    * `originType`: **必須** オリジン・タイプは `HOST_SERVER` または `OBJECT_STORAGE` の可能性があります。
    * `domain`: **必須** ホスト名を文字列として指定します。
    * `protocol`: **必須** サポート対象のプロトコルは、`HTTP`、`HTTPS`、または`HTTP_AND_HTTPS` です。
    * `path`: キャッシュに入れられたコンテンツの配信元のパス。デフォルトのパスは /\* です。
    * `httpPort` および/または `httpsPort`: (ホスト・サーバーでは**必須**) これらの 2 つのオプションは、要求されたプロトコルに対応している必要があります。プロトコルが `HTTP` の場合は、`httpPort` が設定される必要があり、`httpsPort` は設定されては_なりません_。同様に、プロトコルが `HTTPS` の場合は、`httpsPort` が設定される必要があり、`httpPort` は設定されては_なりません_。プロトコルが `HTTP_AND_HTTPS` の場合は、`httpPort` と `httpsPort` の_両方_を設定する_必要があります_。Akamai はポート番号に制限があります。許可ポート番号については、[FAQ](faqs.html#are-there-any-restrictions-on-what-http-and-https-port-numbers-are-allowed-for-akamai-) を参照してください。
    * `header`: オリジン・サーバーによって使用されるホスト・ヘッダー情報を指定します。
    * `respectHeader`: `true` に設定された場合は、オリジンの TTL 設定が CDN TTL 設定をオーバーライドするブール値。
    * `cname`: ホスト名に別名を指定します。指定しなかった場合は、生成されます。
    * `bucketName`: (オブジェクト・ストレージのみ**必須**) S3 オブジェクト・ストレージのバケット名。
    * `fileExtension`: (オブジェクト・ストレージのオプション) キャッシュに入れることができるファイル拡張子。
    * `cacheKeyQueryRule`: キャッシュ・キーの動作を構成するために以下のオプションを使用できます。`cacheKeyQueryRule` 引数が指定されていない場合は、「include-all」がデフォルトになります。
      * `include-all` - すべての照会引数を含みます。**デフォルト**
      * `ignore-all` - すべての照会引数を無視します。
      * `ignore: space separated query-args` - スペースで区切られた特定の照会引数を無視します。例えば、`ignore: query1 query2` です
      * `include: space separated query-args`: スペースで区切られた特定の照会引数を含めます。例えば、`include: query1 query2` です

* **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Configuration_Mapping` のコレクション。

  **注**: このコレクションは `uniqueId` 値を提供します。この値を、マッピングとオリジン・パスに関連した後続の API 呼び出しの入力として送信する必要があります。

  [マッピング・コンテナーの表示 (View the Mapping Container)](mapping-container.html)

----
### deleteDomainMapping
`uniqueId` に基づいてドメイン・マッピングを削除します。 ドメイン・マッピングの状態は、_RUNNING_、_STOPPED_、_DELETED_、_ERROR_、_CNAME_CONFIGURATION_、または _SSL_CONFIGURATION_ のいずれかでなければなりません。

* **必須パラメーター**: `uniqueId`: 削除するマッピングの固有 ID
* **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Configuration_Mapping` のコレクション
  [マッピング・コンテナーの表示 (View the Mapping Container)](mapping-container.html)

----
### verifyDomainMapping
CDN の状況を確認し、CDN マッピングの `status` が変更されていたら更新します。CDN マッピングが最初に作成された時点では、状況は _CNAME_CONFIGURATION_ と表示されます。この時点で、DNS レコードを更新して、CDN マッピングが CNAME のホスト名を指すようにする必要があります。更新の方法および変更がインターネットに伝搬するのに要する時間については、DNS プロバイダーに確認してください。 通常は、15 分から 30 分かかります。 その時間が経過した後で、この `verifyDomainMapping` API を呼び出して、CNAME チェーンが完了したかどうかを確認する必要があります。CNAME チェーンが完了すると、CDN マッピングの状況は _RUNNING_ に変更されます。

この API は、最新の CDN マッピング状況を取得するために、いつでも呼び出すことができます。ドメイン・マッピングの状態は、_RUNNING_ または _CNAME_CONFIGURATION_ のいずれかでなければなりません。

* **必須パラメーター**: `uniqueId`: 検証するマッピングの固有 ID
* **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Configuration_Mapping` のコレクション

  [マッピング・コンテナーの表示 (View the Mapping Container)](mapping-container.html)

----
### startDomainMapping
`uniqueId` に基づいて CDN ドメイン・マッピングを開始します。 開始するには、ドメイン・マッピングが _STOPPED_ 状態でなければなりません。

* **必須パラメーター**: `uniqueId`: 開始するマッピングの固有 ID
* **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Configuration_Mapping` のコレクション

  [マッピング・コンテナーの表示 (View the Mapping Container)](mapping-container.html)

----
### stopDomainMapping
`uniqueId` に基づいて CDN ドメイン・マッピングを停止します。 停止を開始するには、ドメイン・マッピングが _RUNNING_ 状態でなければなりません。

* **必須パラメーター**: `uniqueId`: 停止するマッピングの固有 ID
* **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Configuration_Mapping` のコレクション

  [マッピング・コンテナーの表示 (View the Mapping Container)](mapping-container.html)

----
### updateDomainMapping
ユーザーが `uniqueId` で示されたマッピングのプロパティーを更新できるようにします。 変更できるフィールドは、`originHost`、`httpPort`、`httpsPort`、`respectHeader`、`header`、`cacheKeyQueryRule` 引数です。オリジン・タイプがオブジェクト・ストレージの場合は、`bucketName` と `fileExtension` も変更することができます。更新を実行するには、ドメイン・マッピングが _RUNNING_ 状態でなければなりません。

* **パラメーター**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Configuration_Input` のコレクション。
  入力コンテナーのすべての属性を、
  [入力コンテナーの表示 (View the Input Container)](input-container.html) で表示することができます。

  次の属性は入力コンテナーの一部で、ドメイン・マッピングの更新時に提供される**必要があります**。
    * `vendorName`: このマッピング用の CDN プロバイダーの名前を指定します。
    * `path`: このマッピングの現行パスを指定します。
    * `origin`: オリジン・サーバー・アドレスを文字列として指定します。
    * `originType`: オリジン・タイプは `HOST_SERVER` または `OBJECT_STORAGE` の可能性があります。
    * `domain`: ホスト名を指定します。
    * `protocol`: サポート対象のプロトコルは、`HTTP`、`HTTPS`、または `HTTP_AND_HTTPS` です。
    * `httpPort` および/または `httpsPort`: これらの 2 つのオプションは、要求されたプロトコルに対応する必要があります。プロトコルが `HTTP` の場合は、`httpPort` が設定される必要があり、`httpsPort` は設定されては_なりません_。同様に、プロトコルが `HTTPS` の場合は、`httpsPort` が設定される必要があり、`httpPort` は設定されては_なりません_。プロトコルが `HTTP_AND_HTTPS` の場合は、`httpPort` と `httpsPort` の_両方_を設定する_必要があります_。Akamai はポート番号に制限があります。許可ポート番号については、[FAQ](faqs.html#are-there-any-restrictions-on-what-http-and-https-port-numbers-are-allowed-for-akamai-) を参照してください。
    * `header`: オリジン・サーバーによって使用されるホスト・ヘッダー情報を指定します。
    * `respectHeader`: `true` に設定された場合は、オリジンの TTL 設定が CDN TTL 設定をオーバーライドするブール値。
    * `uniqueId`: マッピングの作成後に生成されます。
    * `cname`: cname を指定します。指定しなかった場合は、マッピングの作成時に作成されています。
    * `bucketName`: (オブジェクト・ストレージのみ**必須**) S3 オブジェクト・ストレージのバケット名。
    * `fileExtension`: (オブジェクト・ストレージのみ**必須**) キャッシュに入れることができるファイル拡張子。
    * `cacheKeyQueryRule`: キャッシュ・キーの動作ルールは、2017 年 11 月 16 日_以降に_作成された CDN マッピングに対してのみ更新することができます。キャッシュ・キーの動作を構成するために以下のオプションを使用できます。
      * `include-all` - すべての照会引数を含みます。**デフォルト**
      * `ignore-all` - すべての照会引数を無視します。
      * `ignore: space separated query-args` - スペースで区切られた特定の照会引数を無視します。例えば、`ignore: query1 query2` です
      * `include: space separated query-args`: スペースで区切られた特定の照会引数を含めます。例えば、`include: query1 query2` です
* **戻り** タイプ `SoftLayer_Container_Network_CdnMarketplace_Configuration_Mapping` のコレクション
  [マッピング・コンテナーの表示 (View the Mapping Container)](mapping-container.html)

----
### listDomainMappings
現行のカスタマーのすべてのドメイン・マッピングのコレクションを返します。

* **必須パラメーター**: なし
* **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Configuration_Mapping` のコレクション
  [マッピング・コンテナーの表示 (View the Mapping Container)](mapping-container.html)

----
### listDomainMappingByUniqueId
CDN の `uniqueId` に基づいて、単一のドメイン・オブジェクトを含むコレクションを返します。

* **必須パラメーター**: `uniqueId`: 返されるマッピングの固有 ID
* **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Configuration_Mapping` の単一オブジェクトのコレクション
  [マッピング・コンテナーの表示 (View the Mapping Container)](mapping-container.html)

----
## オリジン用の API
### createOriginPath
既存の CDN および特定のお客様用のオリジン・パスを作成します。オリジン・パスは、ホスト・サーバーまたはオブジェクト・ストレージに基づくことができます。オリジン・パスを作成するには、ドメイン・マッピングの状態が _RUNNING_ または _CNAME_CONFIGURATION_ のいずれかでなければなりません。

* **パラメーター**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Configuration_Input` のコレクション。
  入力コンテナーのすべての属性を、
  [入力コンテナーの表示 (View the Input Container)](input-container.html) で表示することができます。

  次の属性は入力コンテナーの一部で、オリジン・パスの作成時に提供される場合があります (特に記載がない限り属性はオプションです)。
    * `vendorName`: **必須** 有効な IBM Cloud CDN プロバイダーの名前を指定します。
    * `origin`: **必須** オリジン・サーバー・アドレスを文字列として指定します。
    * `originType`: **必須** オリジン・タイプは `HOST_SERVER` または `OBJECT_STORAGE` の可能性があります。
    * `domain`: **必須** ホスト名を文字列として指定します。
    * `protocol`: **必須** サポート対象のプロトコルは、`HTTP`、`HTTPS`、または`HTTP_AND_HTTPS` です。
    * `path`: キャッシュに入れられたコンテンツの配信元のパス。マッピング・パスで始める必要があります。例えば、マッピング・パスが `/test` の場合、オリジン・パスは `/test/media` になります。
    * `httpPort` および/または `httpsPort`: **必須** これらの 2 つのオプションは、要求されたプロトコルに対応する必要があります。プロトコルが `HTTP` の場合は、`httpPort` が設定される必要があり、`httpsPort` は設定されては_なりません_。同様に、プロトコルが `HTTPS` の場合は、`httpsPort` が設定される必要があり、`httpPort` は設定されては_なりません_。プロトコルが `HTTP_AND_HTTPS` の場合は、`httpPort` と `httpsPort` の_両方_を設定する_必要があります_。Akamai はポート番号に制限があります。許可ポート番号については、[FAQ](faqs.html#are-there-any-restrictions-on-what-http-and-https-port-numbers-are-allowed-for-akamai-) を参照してください。
    * `header`: オリジン・サーバーによって使用されるホスト・ヘッダー情報を指定します。
    * `uniqueId`: **必須** マッピングの作成後に生成されます。
    * `cname`: ホスト名に別名を指定します。固有の cname を指定しなかった場合は、マッピングの作成時に生成されています。
    * `bucketName`: (オブジェクト・ストレージは**必須**) S3 オブジェクト・ストレージのバケット名。
    * `fileExtension`: (オブジェクト・ストレージのオプション) キャッシュに入れることができるファイル拡張子。
    * `cacheKeyQueryRule`: キャッシュ・キーの動作を構成するために以下のオプションを使用できます。
      * `include-all` - すべての照会引数を含みます。**デフォルト**
      * `ignore-all` - すべての照会引数を無視します。
      * `ignore: space separated query-args` - スペースで区切られた特定の照会引数を無視します。例えば、`ignore: query1 query2` です
      * `include: space separated query-args`: スペースで区切られた特定の照会引数を含めます。例えば、`include: query1 query2` です

* **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Configuration_Mapping_Path` のコレクション

  [オリジン・パス・コンテナーの表示 (View the Origin Path Container)](path-container.html)

----
### updateOriginPath
既存のマッピングおよび特定のお客様用の既存のオリジン・パスを更新します。 オリジン・タイプはこの API では変更できません。変更できるプロパティーは `path`、`origin`、`httpPort` と `httpsPort`、`header`、および `cacheKeyQueryRule` 引数です。更新するには、ドメイン・マッピングの状態が _RUNNING_ または _CNAME_CONFIGURATION_ のいずれかでなければなりません。

* **パラメーター**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Configuration_Input` のコレクション。
  入力コンテナーのすべての属性を、
  [入力コンテナーの表示 (View the Input Container)](input-container.html) で表示することができます。

  次の属性は入力コンテナーの一部で、オリジン・パスの更新時に提供される場合があります (特に記載がない限り属性はオプションです)。
    * `oldPath`: **必須** 変更する現行パス。
    * `origin`: (更新中の場合は**必須**) オリジン・サーバー・アドレスを文字列として指定します。
    * `originType`: **必須** オリジン・タイプは `HOST_SERVER` または `OBJECT_STORAGE` の可能性があります。
    * `path`: **必須** 追加する新規のパス。マッピング・パスを基準とした相対パス。
    * `httpPort` および/または `httpsPort`: (更新中の場合はホスト・サーバーでは**必須**) これらの 2 つのオプションは、要求されたプロトコルに対応している必要があります。プロトコルが `HTTP` の場合は、`httpPort` が設定される必要があり、`httpsPort` は設定されては_なりません_。同様に、プロトコルが `HTTPS` の場合は、`httpsPort` が設定される必要があり、`httpPort` は設定されては_なりません_。プロトコルが `HTTP_AND_HTTPS` の場合は、`httpPort` と `httpsPort` の_両方_を設定する_必要があります_。Akamai はポート番号に制限があります。許可ポート番号については、[FAQ](faqs.html#are-there-any-restrictions-on-what-http-and-https-port-numbers-are-allowed-for-akamai-) を参照してください。
    * `uniqueId`: **必須**: このオリジンが属するマッピングの固有 ID
    * `bucketName`: (オブジェクト・ストレージのみ**必須**) S3 オブジェクト・ストレージのバケット名。
    * `fileExtension`: (オブジェクト・ストレージのみ**必須**) キャッシュに入れることができるファイル拡張子。
    * `cacheKeyQueryRule`: (更新中の場合は**必須**) キャッシュ・キーの動作ルールは、2017 年 11 月 16 日_以降に_作成されたオリジン・パスに対してのみ更新することができます。キャッシュ・キーの動作を構成するために以下のオプションを使用できます。
      * `include-all` - すべての照会引数を含みます。**デフォルト**
      * `ignore-all` - すべての照会引数を無視します。
      * `ignore: space separated query-args` - スペースで区切られた特定の照会引数を無視します。例えば、`ignore: query1 query2` です
      * `include: space separated query-args`: スペースで区切られた特定の照会引数を含めます。例えば、`include: query1 query2` です

* **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Configuration_Mapping_Path` のコレクション

  [オリジン・パス・コンテナーの表示 (View the Origin Path Container)](path-container.html)

----
### deleteOriginPath
既存の CDN および特定のお客様用の既存のオリジン・パスを削除します。削除するには、ドメイン・マッピングの状態が _RUNNING_ または _CNAME_CONFIGURATION_ のいずれかでなければなりません。

* **必須パラメーター**:
  * `uniqueId`: このオリジン・パスが属するマッピングの固有 ID
  * `path`: 削除するパス

* **戻り**: 削除に成功した場合は状況メッセージ、それ以外の場合は、例外がスローされます。

----
### listOriginPath
`uniqueId` に基づいて既存のマッピングに対するオリジン・パスをリストします。

* **必須パラメーター**:
  * `uniqueId`: オリジン・パスをリスト表示するマッピングの固有 ID。
* **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Configuration_Mapping_Path` のオブジェクトのコレクション

  [オリジン・パス・コンテナーの表示 (View the Origin Path Container)](path-container.html)

----
## パージ用の API
### パージするコンテナーのクラス:
```
class SoftLayer_Container_Network_CdnMarketplace_Configuration_Cache_Purge
{
    /**
     * @var string
     */
    public $path;

    /**
     * @var string
     */
    public $status;

    /**
     * @var string
     */
    public $saved;

    /**
     * @var string
     */
    public $date;
}  
```

### createPurge
パージ・レコードを作成して、データベースに挿入します。

* **パラメーター**:
  * `uniqueId`: パージが作成されるマッピングの固有 ID
  * `path`: 作成されるパージのパス

* **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Configuration_Cache_Purge` のコレクション

----
### getPurgeHistoryPerMapping
`uniqueId` と `saved` 状況に基づいて、CDN のパージ履歴を返します (デフォルトでは、`saved` の値は _UNSAVED_ に設定されています。)

* **パラメーター**:
  * `uniqueId`: パージ履歴を取り出すマッピングの固有 ID
  * `saved`: _SAVED_ または _UNSAVED_ されたパージを返します。

* **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Configuration_Cache_Purge` のコレクション

----
### saveOrUnsavePurgePath
パージ・パス項目の状況を更新して、そのパージ・パスを保存する必要があるかどうかを示します。パージ・パスが保存されている場合、新規 `saved` パージを作成します。パスが `unsaved` の場合、保存されたパージ・レコードを削除します。

* **パラメーター**:
  * `uniqueId`: パージが属するマッピングの固有 ID
  * `path`: 保存されるまたは保存されないパージのパス
  * `saved`: _SAVED_ または _UNSAVED_

* **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Configuration_Cache_Purge` のコレクション

----
## 存続時間用の API  
### TimeToLive クラス変数:  
```  
class SoftLayer_Network_CdnMarketplace_Configuration_Cache_TimeToLive  
{
    /**
     * @var string
     */
    public $path;

    /**
     * @var int
     */
    public $timeToLive;

    /**
     * @var timestamp
     */
    public $createDate;
}
```  
### createTimeToLive
新規 `TimeToLive` オブジェクトを作成して、データベースに挿入します。

 * **パラメーター**: `string` `uniqueId`、`string` `path`、`int` `ttl`
 * **戻り**: タイプ `SoftLayer_Network_CdnMarketplace_Configuration_Cache_TimeToLive` のオブジェクト
___
### updateTtl
既存の `TimeToLive` オブジェクトを更新します。 _oldTtl_ と _newTtl_ の入力が等しい場合は、早く終了します。

 * **パラメーター**: `string` `uniqueId`、`string` `oldPath`、`string` `newPath`、`int` `oldTtl`、`int` `newTtl`
 * **戻り**: タイプ `SoftLayer_Network_CdnMarketplace_Configuration_Cache_TimeToLive` のオブジェクト
___
### deleteTtl
既存の `TimeToLive` オブジェクトをデータベースから削除します。

 * **パラメーター**: `string` `uniqueId`、`string` `pathName`
 * **戻り**: 削除の状況を含むストリング
___
### listTtl
CDN の `uniqueId` に基づいて、既存の `TimeToLive` オブジェクトをリストします。

 * **パラメーター**: `string` `uniqueId`
 * **戻り**: タイプ `SoftLayer_Network_CdnMarketplace_Configuration_Cache_TimeToLive` のオブジェクトの配列

 ----
## メトリック用の API  
### メトリック用のコンテナー・クラス:  
```  
class SoftLayer_Container_Network_CdnMarketplace_Metrics  
{  
    /**
     * @var string
     */
    public $type;

    /**
     * @var string[]
     */
    public $names;

    /**
     * @var string[]
     */   
     public $totals;

    /**
     * @var string[]
     */
    public $percentage;

    /**
     * @var string[]
     */
    public $time;

    /**
     * @var string[]
     */
    public $xaxis;

    /**
     * @var string[]
     */
    public $yaxis1;

    /**
     * @var string[]
     */
    public $yaxis2;

    /**
     * @var string[]
     */
    public $yaxis3;

    /**
     * @var string[]
     */
    public $yaxis4;

    /**
     * @var string[]
     */
    public $yaxis5;

    /**
     * @var string[]
     */
    public $yaxis6;

    /**
     * @var string[]
     */
    public $yaxis7;

    /**
     * @var string[]
     */
    public $yaxis8;

    /**
     * @var string[]
     */
    public $yaxis9;

    /**
     * @var string[]
     */
    public $yaxis10;

    /**
     * @var string[]
     */
    public $yaxis11;

    /**
     * @var string[]
     */
    public $yaxis12;

    /**
     * @var string[]
     */
    public $yaxis13;

    /**
     * @var string[]
     */
    public $yaxis14;

    /**
     * @var string[]
     */
    public $yaxis15;

    /**
     * @var string[]
     */
    public $yaxis16;

    /**
     * @var string[]
     */
    public $yaxis17;

    /**
     * @var string[]
     */
    public $yaxis18;

    /**
     * @var string[]
     */
    public $yaxis19;

    /**
     * @var string[]
     */
    public $yaxis20;
}  
```  
### getCustomerUsageMetrics
指定された期間について、お客様のアカウントに関して直接表示 (グラフなし) するための事前定義された統計の総数を返します。

 * **パラメーター**: `string` `vendorName`、`int` `startDate`、`int` `endDate`、`string` `frequency`

 * **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Metrics` のオブジェクトのコレクション
___
### getMappingUsageMetrics
指定されたマッピングについて直接表示するための事前定義された統計の総数を返します。 `frequency` の値は、デフォルトでは「aggregate」です。

 * **パラメーター**: `string` `mappingUniqueId`、`int` `startDate`、`int` `endDate`、`string` `frequency`
 * **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Metrics` のオブジェクトのコレクション
___
### getMappingHitsMetrics
ドメイン・マッピングごとに、指定された時間範囲について特定の頻度でヒットの総数を返します。 頻度には「day」、「week」、および「month」を指定でき、各間隔がグラフの 1 つのプロット・ポイントになります。 戻りデータは、`startDate`、`endDate`、および `frequency` に基づいて配列されます。 `frequency` の値は、デフォルトでは「aggregate」です。

 * **パラメーター**: `string` `mappingUniqueId`、`int` `startDate`、`int` `endDate`、`string` `frequency`
 * **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Metrics` のオブジェクトのコレクション
___
### getMappingHitsByTypeMetrics
指定された時間範囲について特定の頻度でヒットの総数を返します。 頻度には「day」、「week」、および「month」を指定でき、各間隔がグラフの 1 つのプロット・ポイントになります。 戻りデータは、`startDate`、`endDate`、および `frequency` に基づいて配列される必要があります。 `frequency` の値は、デフォルトでは「aggregate」です。

 * **パラメーター**: `string` `mappingUniqueId`、`int` `startDate`、`int` `endDate`、`string` `frequency`
 * **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Metrics` のオブジェクトのコレクション
___
### getMappingBandwidthMetrics
個々の CDN のエッジ・ヒットの数を返します。 各マッピングで、地域はベンダーごとに異なっても構いません。

 * **パラメーター**: `string` `mappingUniqueId`、`int` `startDate`、`int` `endDate`、`string` `frequency`
 * **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Metrics` のオブジェクトのコレクション
___
### getMappingBandwidthByRegionMetrics
指定された期間について、指定のマッピングに関する直接表示 (グラフなし) 用の事前定義された統計の総数を返します。 `frequency` の値は、デフォルトでは「aggregate」です。

 * **パラメーター**: `string` `mappingUniqueId`、`int` `startDate`、`int` `endDate`、`string` `frequency`
 * **戻り**: タイプ `SoftLayer_Container_Network_CdnMarketplace_Metrics` のオブジェクトのコレクション
