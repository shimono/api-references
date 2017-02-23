# Entity一括操作($batch)
### 概要
ODataEntityに対して、一覧取得や一件取得、登録、更新、削除といった一括操作を行う
### 必要な権限
* 一覧取得・一件取得  
	read
* 登録・更新・削除  
	write

### 制限事項
* $batch制限事項
	- 「GET」のリクエストクエリは未対応
	- 一覧取得を実行した場合、ランダムに10件返却する
	- NavigationProperty経由の「GET」は、未対応
	- $linksの「PUT」「GET」「DELETE」は、未対応(501を返却)

<br>
### リクエスト
#### リクエストURL
```
/{CellName}/{BoxName}/{ODataCollecitonName}/$batch
```
|パス<br>|概要<br>|
|:--|:--|
|{CellName}<br>|セル名<br>|
|{BoxName}<br>|ボックス名<br>|
|{ODataCollecitonName}<br>|コレクション名<br>|
#### メソッド
POST
#### リクエストクエリ
|クエリ名<br>|概要<br>|有効値<br>|必須<br>|備考<br>|
|:--|:--|:--|:--|:--|
|p_cookie_peer<br>|クッキー認証値<br>|認証時にサーバから返却されたクッキー認証値<br>|×<br>|Authorizationヘッダの指定が無い場合のみ有効<br>クッキーの認証情報を利用する場合に指定する<br>|
#### リクエストヘッダ
|ヘッダ名<br>|概要<br>|有効値<br>|必須<br>|備考<br>|
|:--|:--|:--|:--|:--|
|Authorization<br>|OAuth2.0形式で、認証情報を指定する<br>|Bearer {UnitUserToken}<br>|×<br>|※認証トークンは認証トークン取得APIで取得したトークン テスト未実施<br>|
|Content-Type<br>|リクエストボディの形式を指定する<br>|multipart / mixed; boundary = {Boundary}<br>|○<br>|省略時は[multipart/mixed]として扱う 　{Boundary}に使用可能な文字種：半角英数大小文字 '()+_,-./:=?<br>未対応<br>|
#### リクエストボディ
バッチ処理内容をMIMEのマルチパートデータ形式でリクエストボディに指定する
バッチ処理の最大処理件数は、1000件まで可能  
バッチ処理には、コンテキスト取得の「クエリーオペレーション」と、コンテキスト登録・更新・削除の「チェンジセット」がある  
以下にリクエストパラメータの記述方法を説明する
```
(1)		--{バウンダリー文字列}
(2)		クエリーオペレーション or チェンジセット
(3)		--{バウンダリー文字列}
(4)		クエリーオペレーション or チェンジセット
(5)		--{バウンダリー文字列}--

(1) 開始バウンダリー文字列「--{バウンダリー文字列}」を指定する
(2) クエリーオペレーション、またはチェンジセットを指定する
(3) (4)リクエストをバウンダリー文字列「--{バウンダリー文字列}」で区切ることで、複数指定が可能となる
(5) 終了バウンダリー文字列「--{バウンダリー文字列}--」を指定する
```
* クエリーオペレーション  
	指定したコンテキストを取得する  
	以下にクエリーオペレーションの記述方法を説明する  
	※「GET」のリクエストクエリは未対応

```
(1)		Content-Type: application/http
(2)		Content-Transfer-Encoding:binary
(3)		
(4)		GET :path
(5)		:request_header

(1) Content-Type を application/httpにて指定する
(2) Content-Transfer-Encoding を binaryにて指定する
(3) 空行を指定する
(4) リクエストメソッド「GET」とパス（:path）を指定する
	:pathにはエントリー情報取得対象のパスを指定する
	詳細は「エントリー取得」の「リクエストURI」を参照
(5) :request_headerにはリクエストヘッダを指定する
	リクエストヘッダは、指定なし、もしくは1つ以上指定が可能
```
* チェンジセット  
	指定したコンテキストの登録・更新・削除をする  
	登録・更新・削除の処理内容をMIMEのマルチパートデータで指定する  
	以下にチェンジセットの記述方法を説明する

```
(1)		Content-Type: multipart/mixed; boundary={チェンジセットバウンダリー文字列}
(2)		Content-Length: XXX
(3)		
(4)		--{チェンジセットバウンダリー文字列}
(5)		Content-Type: application/http
(6)		Content-Transfer-Encoding: binary
(7)		
(8)		:method :path HTTP/1.1
(9)		:request_header
(10)
(11)	:data
(12)
(13)	--{チェンジセットバウンダリー文字列}--

(1) Content-Type を multipart/mixedにて指定する
	{チェンジセットバウンダリー文字列}に使用可能な文字種は「リクエストヘッダ」を参照
(2) Content-Length を指定するXXXにはチェンジセットのContent-Lengthを指定する
(3) 空行を指定する
(4) 開始チェンジセットバウンダリー文字列「--{チェンジセットバウンダリー文字列}」を指定する
(5) Content-Type を application/httpにて指定する
(6) Content-Transfer-Encoding を binaryにて指定する
(7) 空行を指定する
(8) リクエストメソッド（:method）とパス（:path）とHTTPのVersion「HTTP/1.1」を指定する
	:methodにはリクエストメソッド「POST」「PUT」「DELETE」のいずれかを指定する
	:pathにはエントリー登録・更新・削除の処理対象のパスを指定する
(9) :request_headerにはリクエストヘッダを指定する
	リクエストヘッダは、指定なし、もしくは1つ以上指定が可能
(10) 空行を指定する
(11) リクエストメソッド「POST」「PUT」の場合には:dataに登録・更新対象のデータをJSON形式にて指定する
(12) リクエストメソッド「POST」「PUT」の場合には空行を指定する
(13) 終了チェンジセットバウンダリー文字列「--{チェンジセットバウンダリー文字列}--」を指定する
	チェンジセットバウンダリー文字列からチェンジセットバウンダリー文字列までが１つのリクエスト情報となり、
	リクエストをチェンジセットバウンダリー文字列で区切ることで、複数指定が可能となる定が可能
```
#### Request Path
* {EntitySet名}
* {EntitySet名}('{\__id}')
* {EntitySet名}('{\__id}')/{NavigationProperty名}
* {EntitySet名}('{\__id}')/$links/{NavigationProperty名}

#### メソッド
POST, GET, PUT, DELETE
#### Request Queries
未対応
#### リクエストヘッダ
ユーザデータの仕様に従う
#### リクエストボディ
ユーザデータの仕様に従う
#### リクエストサンプル
次に示すのは、以下の流れでコンテキストを取得・登録・更新・削除する場合のリクエストの例を示す
* userの「ID:0000」を取得
* userを「ID:0000、Name:富士通 太郎」で登録
* userを「ID:0000、familyName:富士通、givenName:太郎」で更新
* userの「ID:0000」を取得
* NavigationProperty経由でlogを「ID:0001、Name:太郎ログ」で登録
* logの「ID:0001」を削除
* userの「ID:0000」を削除

```
--batch_31e84e14-28b9-4741-903f-b955f2a1b853
Content-Type: application/http
Content-Transfer-Encoding:binary
GET user('0000') HTTP/1.1
Host:
Accept-Encoding: gzip
Accept: application/json
Content-Length: 0
--batch_31e84e14-28b9-4741-903f-b955f2a1b853
Content-Type: multipart/mixed; boundary=changeset_76c10b01-3eaf-49c2-bdd7-9fe90df24159
Content-Length: 608
--changeset_76c10b01-3eaf-49c2-bdd7-9fe90df24159
Content-Type: application/http
Content-Transfer-Encoding: binary
POST user HTTP/1.1
Host:
Content-Type: application/json
Content-Length: 41
{"__id":"0000","Name":"田中 太郎"}
--changeset_76c10b01-3eaf-49c2-bdd7-9fe90df24159
Content-Type: application/http
Content-Transfer-Encoding: binary
PUT user('0000') HTTP/1.1
Host:
Content-Type: application/json
Content-Length: 87
If-Match: *
{"__id":"0000","Name":"田中 太郎","familyName":"田中","givenName":"太郎"}
--changeset_76c10b01-3eaf-49c2-bdd7-9fe90df24159--
--batch_31e84e14-28b9-4741-903f-b955f2a1b853
Content-Type: application/http
Content-Transfer-Encoding:binary
GET user('0000') HTTP/1.1
Host:
Accept-Encoding: gzip
Accept: application/json
Content-Length: 0
--batch_31e84e14-28b9-4741-903f-b955f2a1b853
Content-Type: multipart/mixed; boundary=changeset_d4883767-a06e-4632-9608-ae952b443dfc
Content-Length: 686
--changeset_d4883767-a06e-4632-9608-ae952b443dfc
Content-Type: application/http
Content-Transfer-Encoding: binary
POST user('0000')/_log HTTP/1.1
Host:
Content-Type: application/json
Content-Length: 37
{"__id":"0001","Name":"太郎ログ"}
--changeset_d4883767-a06e-4632-9608-ae952b443dfc
Content-Type: application/http
Content-Transfer-Encoding: binary
DELETE log('0001') HTTP/1.1
Host:
Content-Length: 0
If-Match: *
--changeset_d4883767-a06e-4632-9608-ae952b443dfc
Content-Type: application/http
Content-Transfer-Encoding: binary
DELETE user('0000') HTTP/1.1
Host:
Content-Length: 0
If-Match: *
--changeset_d4883767-a06e-4632-9608-ae952b443dfc--
--batch_31e84e14-28b9-4741-903f-b955f2a1b853--
```

<br>
### レスポンス
#### ステータスコード
|コード<br>|概要<br>|備考<br>|
|:--|:--|:--|
|202<br>|成功<br>| <br>
|400<br>|リクエストクエリの指定誤り<br>リクエストヘッダの指定誤り<br>|未対応<br>|
|401<br>|認証トークンが無効の場合<br>|テスト未実施<br>|
|403<br>|アクセス権限が不足している場合<br>| <br>|
|404<br>|存在しないCellを指定した場合<br>存在しないBoxを指定した場合<br>存在しないODataCollectionを指定した場合<br>存在しないEntitySetを指定した場合<br>|テスト未実施<br>|
|405<br>|許可していないリクエストメソッドを指定 した場合<br>|テスト未実施<br>|
#### レスポンスヘッダ
|ヘッダ名<br>|概要<br>|備考<br>|
|:--|:--|:--|
|Content-Type<br>|返却されるデータの形式<br>|正常に$batch処理ができた場合：multipart/mixed; boundary={Boundary}<br>|
|DataServiceVersion<br>|ODataのバージョン情報<br>|正常にEntityが作成できた場合のみ返却する<br>|
#### レスポンスボディ
サンプル  
次に示すのは、上記のリクエストパラメータの例を実行した場合のレスポンスの例を示す
* userの「ID:0000」を取得
* userを「ID:0000、Name:富士通 太郎」で登録
* userを「ID:0000、familyName:富士通、givenName:太郎」で更新
* userの「ID:0000」を取得
* NavigationProperty経由でlogを「ID:0001、Name:太郎ログ」で登録
* logの「ID:0001」を削除
* userの「ID:0000」を削除

#### エラーメッセージ一覧
[エラーメッセージ一覧](200_Error_Messages.html)を参照
#### レスポンスサンプル
```
--batch_AI6AVj7wPaHhKrBkYqBmA78weSj3u848CuX
Content-Type: application/http
HTTP/1.1 404 Not Found
Content-Type: application/json
Content-Length: 48
DataServiceVersion: 2.0
{"error":{"code":"404","message":"Not Found"}}
--batch_AI6AVj7wPaHhKrBkYqBmA78weSj3u848CuX
Content-Type: multipart/mixed; boundary=changeset_qPJh1ATATNykjJT8ocPE5MJE3QzMBS19ljS
--changeset_qPJh1ATATNykjJT8ocPE5MJE3QzMBS19ljS
Content-Type: application/http
Content-Transfer-Encoding: binary
HTTP/1.1 201 Created
Content-Type: application/json
Content-Length: 425
DataServiceVersion: 2.0
Location: http://{UnitFQDN}:50280/api/context/xxx-ah,http%253A%252F%252FUnitFQDN%252Fds%252Fabc-web/odata/user('0000')
{"d":{"Results":{"Name":"\u5bcc\u58eb\u901a \u592a\u90ce","__metadata":{"Type":"user","ETag":"1-9fcc9e8203dc56dc7fe74a7b39e30e88","uri":"http://{UnitFQDN}/api/context/fujitsu-ah,http%253A%252F%252FUnitFQDN%252Fds%252Fvet-web/odata/user('0000')"},"__published":"2010-12-24T11:31:50+09:00","__id":"0000","::meta":{"Type":"user","links":[],"published":"2010-12-24T11:31:50+09:00","id":"0000"}}}}
--changeset_qPJh1ATATNykjJT8ocPE5MJE3QzMBS19ljS--
--batch_AI6AVj7wPaHhKrBkYqBmA78weSj3u848CuX
Content-Type: multipart/mixed; boundary=changeset_mry0iPNyKqKONtK237cBbGHUQhNgwIxlg70
--changeset_mry0iPNyKqKONtK237cBbGHUQhNgwIxlg70
Content-Type: application/http
Content-Transfer-Encoding: binary
HTTP/1.1 201 Created
Content-Type: application/json
Content-Length: 0
DataServiceVersion: 2.0
--changeset_mry0iPNyKqKONtK237cBbGHUQhNgwIxlg70--
--batch_AI6AVj7wPaHhKrBkYqBmA78weSj3u848CuX
Content-Type: application/http
HTTP/1.1 200 OK
Content-Type: application/atom+xml
Content-Length: 457
ETag: 2-3230d783295299adb1ca59a71768d585
DataServiceVersion: 2.0
{"d":{"results":{"__published":"\/Date(1370248522812)\/","__updated":"\/Date(1370248522812)\/","__id":"0000","name":"田中 太郎","__metadata":{"etag":"W\/\"1-1370248522812\"","type":"UserData.user","uri":"http:\/\/localhost:8080\/dc1-core\/daotest1370248515828\/boxname1370248515828\/odatabatch\/user('0000')"},"_log":{"__deferred":{"uri":"http:\/\/localhost:8080\/dc1-core\/daotest1370248515828\/boxname1370248515828\/odatabatch\/user('0000')\/_log"}}}}}
--batch_AI6AVj7wPaHhKrBkYqBmA78weSj3u848CuX
Content-Type: multipart/mixed; boundary=changeset_wtsoeMGaDT6vDVM8QooqoLGEH8s6nHcrh6C
--changeset_mry0iPNyKqKONtK237cBbGHUQhNgwIxlg70
Content-Type: application/http
Content-Transfer-Encoding: binary
HTTP/1.1 201 Created
Content-Type: application/json
Content-Length: 0
DataServiceVersion: 2.0
--changeset_d4883767-a06e-4632-9608-ae952b443dfc
Content-Type: application/http
Content-Transfer-Encoding: binary
HTTP/1.1 204 No Content
Content-Type: application/json
Content-Length: 0
DataServiceVersion: 2.0
--changeset_wtsoeMGaDT6vDVM8QooqoLGEH8s6nHcrh6C
Content-Type: application/http
Content-Transfer-Encoding: binary
HTTP/1.1 204 No Content
Content-Type: application/json
Content-Length: 0
DataServiceVersion: 2.0
--changeset_wtsoeMGaDT6vDVM8QooqoLGEH8s6nHcrh6C--
--batch_AI6AVj7wPaHhKrBkYqBmA78weSj3u848CuX--
```

<br>
### CURLサンプル

```sh
curl "https://{UnitFQDN}/{CellName}/{BoxName}/{ODataCollecitonName}/$batch" -X POST -i -H 'Content-Type:multipart/mixed; boundary= batch_XAmu9BiJJLBa20sRWIq74jp2UlNAVueztqu' -H 'Authorization: Bearer {UnitUserToken}' -H 'Accept: application/json' -d  '--batch_XAmu9BiJJLBa20sRWIq74jp2UlNAVueztqu Content-Type: application/http Content-Transfer-Encoding:binary  GET user('0000') Host: host
  --batch_XAmu9BiJJLBa20sRWIq74jp2UlNAVueztqu Content-Type: multipart/mixed; boundary=changeset_cLzcDEEVPwvvoxS3yJTFTpRauSK_FAQ6mQtyo0aby93-SDP3lAs2A19a2uBb
 Content-Length: 995  --changeset_cLzcDEEVPwvvoxS3yJTFTpRauSK_FAQ6mQtyo0aby93-SDP3lAs2A19a2uBb Content-Type: application/http Content-Transfer-Encoding: binary
  POST user HTTP/1.1 Host:  Connection: close Accept: application/json Content-Type: application/json Content-Length: 38  {"__id":"0000","Name":"田中 太郎"}
  --changeset_cLzcDEEVPwvvoxS3yJTFTpRauSK_FAQ6mQtyo0aby93-SDP3lAs2A19a2uBb Content-Type: application/http Content-Transfer-Encoding: binary  PUT user('0000')
 HTTP/1.1 Host:  Connection: close Accept: application/json Content-Type: application/json If-Match: * Content-Length: 38  {"__id":"0000","familyName":"田中",
"givenName":"太郎"}  --changeset_cLzcDEEVPwvvoxS3yJTFTpRauSK_FAQ6mQtyo0aby93-SDP3lAs2A19a2uBb--  --batch_XAmu9BiJJLBa20sRWIq74jp2UlNAVueztqu--'
```
<br>
<br>
<br>
###### Copyright 2017    FUJITSU LIMITED