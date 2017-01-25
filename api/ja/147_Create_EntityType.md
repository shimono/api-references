# EntityType登録
### 概要
ユーザーデータのEntityTypeを登録する

### 制限事項
* リクエストヘッダのContent-Typeは全てapplication/jsonとして扱う
* リクエストボディはjson形式のみ受け付ける
* レスポンスヘッダのContent-Typeはapplication/jsonのみをサポートし、レスポンスボディはjson形式とする
* $formatクエリオプションにatom または xmlを指定した場合、エラーとはならないが、レスポンスボディのデータの保証はない

### 個別の制限
* 1つのCollectionに対して作成できるEntityTypeは100個まで

### 必要な権限
alter-schema

<br>
### リクエスト
#### リクエストURL
```
/{CellName}/{BoxName}/{OdataCollecitonPath}/$metadata/EntityType
```

#### メソッド
POST

#### リクエストクエリ
共通リクエストクエリ

|クエリ名<br>|概要<br>|有効値<br>|必須<br>|備考<br>|
|:--|:--|:--|:--|:--|
|dc_cookie_peer<br>|クッキー認証値<br>|認証時にサーバから返却されたクッキー認証値<br>|×<br>|Authorizationヘッダの指定が無い場合のみ有効<br>クッキーの認証情報を利用する場合に指定する<br>|

##### OData 共通リクエストクエリ
なし

#### リクエストヘッダ
##### 共通リクエストヘッダ

|ヘッダ名<br>|概要<br>|有効値<br>|必須<br>|備考<br>|
|:--|:--|:--|:--|:--|
|X-HTTP-Method-Override<br>|メソッドオーバーライド機能<br>|任意<br>|×<br>|POSTメソッドでリクエスト時にこの値を指定すると、指定した値がメソッドとして使用される<br>|
|X-Override<br>|ヘッダオーバライド機能<br>|${上書きするヘッダ名}:${値}<br>|×<br>|通常のHTTPヘッダの値を上書きします。複数のヘッダを上書きする場合はX-Overrideヘッダを複数指定する<br>|
|X-Dc-RequestKey<br>|イベントログに出力するRequestKeyフィールドの値<br>|半角英数、-(半角ハイフン)と_(半角アンダーバー)<br>最大128文字<br>|×<br>|指定がない場合、PCS-${UNIX時間}を設定する<br>V1.1.7以降で対応<br>|

##### OData共通リクエストヘッダ

|ヘッダ名<br>|概要<br>|有効値<br>|必須<br>|備考<br>|
|:--|:--|:--|:--|:--|
|Authorization<br>|OAuth2.0形式で、認証情報を指定する<br>|Bearer {UnitUserToken}<br>|×<br>|※認証トークンは認証トークン取得APIで取得したトークン<br>|

##### OData登録リクエストヘッダ

|ヘッダ名<br>|概要<br>|有効値<br>|必須<br>|備考<br>|
|:--|:--|:--|:--|:--|
|Content-Type<br>|リクエストボディの形式を指定する<br>|application / json<br>|×<br>|省略時は[application/json]として扱う<br>|
|Accept<br>|レスポンスボディの形式を指定する<br>|application / json<br>|×<br>|省略時は[application/json]として扱う<br>|

#### リクエストボディ
##### Format
JSON

|項目名<br>|概要<br>|有効値<br>|必須<br>|備考<br>|
|:--|:--|:--|:--|:--|
|Name<br>|EntityType名<br>|桁数：1&#65374;128<br>文字種:半角英数字と-(半角ハイフン)と_(半角アンダーバー)<br>ただし、先頭文字に-(半角ハイフン)と_(半角アンダーバー)は指定不可<br>null<br>|○<br>| <br>|

#### リクエストサンプル
```json
{"Name":"animal"}
```

<br>
### レスポンス
#### ステータスコード
201

#### レスポンスヘッダ

|ヘッダ名<br>|概要<br>|備考<br>|
|:--|:--|:--|
|X-Dc-Version<br>|APIの実行バージョン<br>|リクエストが処理されたAPIバージョン<br>|
|Access-Control-Allow-Origin<br>|クロスドメイン通信許可ヘッダ<br>|返却値は"*"固定<br>|
|Content-Type<br>|返却されるデータの形式<br>| <br>|
|Location<br>|作成したリソースへのURL<br>| <br>|
|ETag<br>|リソースのバージョン情報<br>| <br>|
|DataServiceVersion<br>|ODataのバージョン<br>| <br>|

#### レスポンスボディ
#####共通レスポンスボディ
レスポンスはJSONオブジェクトで、オブジェクト（サブオブジェクト）に定義されるキー(名前)と型、並びに値の対応は以下のとおりです。

|オブジェクト<br>|名前（キー）<br>|型<br>|値<br>|
|:--|:--|:--|:--|
|ルート<br>|d<br>|object<br>|オブジェクト{1}<br>|
|{1}<br>|__count<br>|string<br>|$inlinecountクエリでの取得結果件数<br>|
|{1}<br>|results<br>|array<br>|オブジェクト{2}の配列<br>|
|{2}<br>|__published<br>|string<br>|作成日(UNIX時間)<br>|
|{2}<br>|__updated<br>|string<br>|更新日(UNIX時間)<br>|
|{2}<br>|__metadata<br>|object<br>|オブジェクト{3}<br>|
|{3}<br>|etag<br>|string<br>|Etag値<br>|
|{3}<br>|uri<br>|string<br>|作成したリソースへのURL<br>|

##### EntityType固有レスポンスボディ

|オブジェクト<br>|名前（キー）<br>|型<br>|値<br>|
|:--|:--|:--|:--|
|{3}<br>|type<br>|string<br>|ODataSvcSchema.EntityType<br>|
|{2}<br>|Name<br>|string<br>|EntityType名<br>|

#### エラーメッセージ一覧
[エラーメッセージ一覧](198_Error_Messages.html)を参照

#### レスポンスサンプル
```json
{
  "d": {
    "results": {
      "_AssociationEnd": {
        "__deferred": {
          "uri": "https://{UnitFQDN}/{CellName}/{BoxName}/{CollectionName}/$metadata/EntityType('{EntitytypeName}')/_AssociationEnd"
        }
      },
      "Name": "entityType",
      "__published": "/Date(1349434504818)/",
      "__updated": "/Date(1349434504818)/",
      "_Property": {
        "__deferred": {
          "uri": "https://{UnitFQDN}/{CellName}/{BoxName}/{CollectionName}/$metadata/EntityType('{EntitytypeName}')/_Property"
        }
      },
      "__metadata": {
        "etag": "1-1349434504818",
        "type": "ODataSvcSchema.EntityType",
        "uri": "https://{UnitFQDN}/{CellName}/{BoxName}/{CollectionName}/$metadata/EntityType('{EntitytypeName}')"
      }
    }
  }
}
```

<br>
### CURLサンプル
#### CURLコマンド(UNIX)
```sh
curl "https://{UnitFQDN}/{CellName}/{BoxName}/{OdataCollecitonPath}/$metadata/EntityType" -X POST -i -H 'Authorization: Bearer {UnitUserToken}' -H 'Accept: application/json' -d '{ "Name": "animal" }' 
```
<br>
<br>
<br>
###### Copyright 2017    FUJITSU LIMITED