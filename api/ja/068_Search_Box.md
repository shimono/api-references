# Box一覧取得
### 概要
既存のBox情報の一覧を取得する

### 必要な権限
box-read

### 制限事項
* リクエストヘッダのAcceptは無視される
* リクエストヘッダのContent-Typeは全てapplication/jsonとして扱う
* リクエストボディはjson形式のみ受け付ける
* レスポンスヘッダのContent-Typeはapplication/jsonのみをサポートし、レスポンスボディはjson形式とする
* $formatクエリオプションは無視される

<br>
### リクエスト
#### リクエストURL
```
/{CellName}/__ctl/Box
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

[全文検索(q)クエリ](194_Full_Text_Search_Queryhtml)

#### リクエストヘッダ

|ヘッダ名<br>|概要<br>|有効値<br>|必須<br>|備考<br>|
|:--|:--|:--|:--|:--|
|X-HTTP-Method-Override<br>|メソッドオーバーライド機能<br>|任意<br>|×<br>|POSTメソッドでリクエスト時にこの値を指定すると、指定した値がメソッドとして使用されます。<br>|
|X-Override<br>|ヘッダオーバライド機能<br>|${上書きするヘッダ名}:${値}override} $: $ {value}<br>|×<br>|通常のHTTPヘッダの値を上書きします。複数のヘッダを上書きする場合はX-Overrideヘッダを複数指定します。<br>|
|X-Dc-RequestKey<br>|イベントログに出力するRequestKeyフィールドの値<br>|半角英数、-(半角ハイフン)と_(半角アンダーバー)<br>最大128文字<br>|×<br>|指定がない場合、PCS-${UNIX時間}を設定する<br>V1.1.7以降で対応<br>|
|Authorization<br>|OAuth2.0形式で、認証情報を指定する<br>|Bearer {UnitUserToken}<br>|×<br>|※認証トークンは認証トークン取得APIで取得したトークン<br>|
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

|項目名<br>|概要<br>|備考<br>|
|:--|:--|:--|
|d<br>|<br>|<br>|
|d / results<br>|<br>|<br>|
|d / results / __published<br>|作成日<br>|<br>|
|d / results / __updated<br>|更新日<br>|<br>|
|d / results / __metadata<br>|<br>|<br>|
|d / results / __metadata / etag<br>|ETag値<br>|<br>|
|d / results / __metadata / uri<br>|作成したリソースへのURL<br>|<br>|
|d / results / __metadata / type<br>|EntityType<br>|<br>|
|d / results / Name<br>|Box名<br>|<br>|
|d / results / Schema<br>|Schema名<br>|<br>|
#### エラーメッセージ一覧
[エラーメッセージ一覧](198_Error_Messages.html)を参照

#### レスポンスサンプル
```json
{
  "d": {
    "results": [
      {
        "__metadata": {
          "uri": "https://{UnitFQDN}/{CellName}/__ctl/Box('{BoxName}')",
          "etag": "1-1349430328880",
          "type": "CellCtl.Box"
        },
        "Name": "{BoxName}",
        "Schema": null,
        "__published": "/Date(1349430328880)/",
        "__updated": "/Date(1349430328880)/",
        "_Role": {
          "__deferred": {
            "uri": "https://{UnitFQDN}/{CellName}/__ctl/Box('{BoxName}')/_Role"
          }
        },
        "_Relation": {
          "__deferred": {
            "uri": "https://{UnitFQDN}/{CellName}/__ctl/Box('{BoxName}')/_Relation"
          }
        }
      },
      {
        "__metadata": {
          "uri": "https://{UnitFQDN}/{CellName}/__ctl/Box('{BoxName}2')",
          "etag": "1-1349430565146",
          "type": "CellCtl.Box"
        },
        "Name": "{BoxName}2",
        "Schema": null,
        "__published": "/Date(1349430565146)/",
        "__updated": "/Date(1349430565146)/",
        "_Role": {
          "__deferred": {
            "uri": "https://{UnitFQDN}/{CellName}/__ctl/Box('{BoxName}2')/_Role"
          }
        },
        "_Relation": {
          "__deferred": {
            "uri": "https://{UnitFQDN}/{CellName}_name/__ctl/Box('{BoxName}2')/_Relation"
          }
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
curl "https://{UnitFQDN}/{CellName}/__ctl/Box" -X GET -i -H 'Authorization: Bearer {UnitUserToken}' -H 'Accept: application/json'
```
<br>
<br>
<br>
###### Copyright 2017    FUJITSU LIMITED