# Property一覧取得
### 概要
既存のProperty情報の一覧を取得する
### 必要な権限
read
### 制限事項
* リクエストヘッダのContent-Typeは全てapplication/jsonとして扱う
* リクエストボディはjson形式のみ受け付ける
* レスポンスヘッダのContent-Typeはapplication/jsonのみをサポートし、レスポンスボディはjson形式とする
* $formatクエリオプションにatom または xmlを指定した場合、エラーとはならないが、レスポンスボディのデータの保証はない

<br>
### リクエスト
#### リクエストURL
```
/{CellName}/{BoxName}/{OdataCollecitonPath}/$metadata/Property
```
#### メソッド
GET
#### リクエストクエリ
以下のクエリパラメタが利用可能です。

|クエリ名<br>|概要<br>|有効値<br>|必須<br>|備考<br>|
|:--|:--|:--|:--|:--|
|dc_cookie_peer<br>|クッキー認証値<br>|認証時にサーバから返却されたクッキー認証値<br>|×<br>|Authorizationヘッダの指定が無い場合のみ有効<br>クッキーの認証情報を利用する場合に指定する<br>|

[$select クエリ](192_$select_Query.html)

[$expand クエリ](191_$expand_Query.html)

[$format クエリ](190_$Format_Query.html)

[$filter クエリ](189_$filter_Query.html)

[$inlinecount クエリ](193_$inlinecount_Query.html)

[$orderby クエリ](186_$orderby_Query.html)

[$top クエリ](187_$top_Query.html)

[$skip クエリ](188_$Skip_Query.html)

[全文検索(q)クエリ](194_Full_Text_Search_Query.html)

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
##### OData一覧取得リクエストヘッダ
|ヘッダ名<br>|概要<br>|有効値<br>|必須<br>|備考<br>|
|:--|:--|:--|:--|:--|
|Accept<br>|レスポンスボディの形式を指定する<br>|application / json<br>|×<br>|省略時は[application/json]として扱う<br>|
#### リクエストボディ
なし
#### リクエストサンプル
なし

<br>
### レスポンス
#### ステータスコード
200
#### レスポンスヘッダ
なし
#### レスポンスボディ
##### 共通レスポンスボディ
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
##### Property固有レスポンスボディ
|オブジェクト<br>|名前（キー）<br>|型<br>|値<br>|
|:--|:--|:--|:--|
|{3}<br>|type<br>|string<br>|ODataSvcSchema.AssociationEnd<br>|
|{2}<br>|Name<br>|string<br>|Property名<br>|
|{2}<br>|_EntityType.Name<br>|string<br>|紐付くEntityType名<br>|
|{2}<br>|Type<br>|string<br>|型定義<br>|
|{2}<br>|Nullable<br>|boolean<br>|Null値許可<br>|
|{2}<br>|IsDeclared<br>|boolean<br>|Declaredかの真偽<br>|
|{2}<br>|DefaultValue<br>|string<br>|デフォルト値<br>|
|{2}<br>|CollectionKind<br>|string<br>|配列種別<br>|
|{2}<br>|IsKey<br>|boolean<br>|主キー設定<br>|
|{2}<br>|UniqueKey<br>|string<br>|ユニークキー設定<br>|
#### エラーメッセージ一覧
[エラーメッセージ一覧](198_Error_Messages.html)を参照
```json
{
  "d": {
    "results": [
      {
        "_EntityType": {
          "__deferred": {
            "uri": "https://{UnitFQDN}/{CellName}/{BoxName}/{OdataCollecitonPath}/$metadata/Property(Name='PetName',_EntityType.Name='Profile')/_EntityType"
          }
        },
        "Name": "PetName",
        "_EntityType.Name": "Profile",
        "Type": "Edm.String",
        "Nullable": true,
        "DefaultValue": null,
        "CollectionKind": "None",
        "IsKey": true,
        "UniqueKey": null,
        "__published": "/Date(1349434504818)/",
        "__updated": "/Date(1349434504818)/",
        "__metadata": {
          "etag": "1-1349434504818",
          "type": "ODataSvcSchema.Property",
          "uri": "https://{UnitFQDN}/{CellName}/{BoxName}/{OdataCollecitonPath}/$metadata/Property(Name='PetName',_EntityType.Name='Profile')"
        }
      },
      {
        "_EntityType": {
          "__deferred": {
            "uri": "https://{UnitFQDN}/{CellName}/{BoxName}/{OdataCollecitonPath}/$metadata/Property(Name='Age',_EntityType.Name='Profile')/_EntityType"
          }
        },
        "Name": "Age",
        "_EntityType.Name": "Profile",
        "Type": "Edm.Int32",
        "Nullable": false,
        "DefaultValue": null,
        "CollectionKind": "None",
        "IsKey": false,
        "UniqueKey": null,
        "__published": "/Date(1349434944818)/",
        "__updated": "/Date(1349434944818)/",
        "__metadata": {
          "etag": "1-1349434944818",
          "type": "ODataSvcSchema.Property",
          "uri": "https://{UnitFQDN}/{CellName}/{BoxName}/{OdataCollecitonPath}/$metadata/Property(Name='Age',_EntityType.Name='Profile')"
        }
      }
    ]
  }
}
```

<br>
### CURLサンプル
#### CURLコマンド(UNIX)
```sh
curl "https://{UnitFQDN}/{CellName}/{BoxName}/{OdataCollecitonPath}/$metadata/Property" -X GET -i -H 'Authorization: Bearer {UnitUserToken}' -H 'Accept: application/json'
```
<br>
<br>
<br>
###### Copyright 2017    FUJITSU LIMITED