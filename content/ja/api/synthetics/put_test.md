---
title: テストの編集
type: apicontent
order: 31.3
external_redirect: '/api/#テストの編集'
---
## テストの編集

このメソッドを使用して、既存の Synthetics テストを更新します。テストを更新するには、テストの作成時と同じペイロードを送信する必要があります。

必要なパラメーターは API テストとブラウザテストで異なり、それに応じてマークされています。required とマークされたパラメーターは、両方のテストに必要です。

ブラウザテストは GET API テストと同様に処理されます。この方法でブラウザテストを更新できますが、[テストを記録する][1]には UI を使用する必要があります。

リクエストを更新するには、完全なオブジェクトを送信する必要があります。ただし、編集できるのは、`name`、`tags`、`config` (`assertions` および `request` で定義されるすべて)、`message`、`options`、`locations`、`status` の各パラメーターだけです。

**引数**:

テストには主に以下の引数を使用します。

* **`name`** - 必須 - テストの一意の名前。
* **`type`** - 必須 - テストの種類。有効な値は `api` と `browser` です。
* **`subtype`** - _SSL のテストで必須_ - SSL API のテストでは、`ssl` を値に指定するか、あるいはこの引数を省略する必要があります。
* **`request`** - _必須_ - API と SSL のテストに関連付けられたリクエスト。詳しくは、以降にある「リクエスト」のセクションを参照してください。
* **`options`** - _必須_ - テストをカスタマイズするオプションのリスト。詳しくは、以降にある「オプション」のセクションを参照してください。
* **`message`** - 必須 - テストの説明。
* **`assertions`** - _API と SSL のテストで必須_ - テストに関連付けられたアサーション。詳しくは、以降にある「アサーション」のセクションを参照してください。
* **`locations`** - _必須_ - テストの送信元になる場所のリスト。少なくとも 1 つの値が必要で、すべての場所を使用できます。有効な場所のリストについては、`GET available locations` メソッドを使用してください。
* **`tags`** - _オプション_ - Datadog でテストを表示する際に、テストを絞り込むために使用するタグのリスト。カスタムタグの詳細については、[タグの使用方法][2]を参照してください。

以降では、作成するテストに応じて指定できる構成オプションについて説明します。

**リクエスト**:

**`request`**は、ブラウザ、API、および SSL のテストで必須となる引数です。エンドポイントに対するリクエストの実行に必要な情報をすべて含むオブジェクトです。JSON オブジェクトであり、次の属性を持ちます。

* **`method`** - _API とブラウザのテストで必須_ - テストする API のメソッド。有効な値は、`GET`、`POST`、`PUT`、`PATCH`、および `DELETE` です。ブラウザテストの場合は `GET` を使用します。
* **`url`** - _API とブラウザのテストで必須_ - Datadog がテストする API のエンドポイント。ブラウザテストの場合は、Datadog がテストする Web サイトの URL。
* **`host`** - _SSL のテストで必須_ - SSL のテストを作成する場合は、接続先のホストを指定します。
* **`port`** - _SSL のテストで必須_ - SSL のテストを作成する場合は、接続先のポートを指定します。
* **`basicAuth`** - _オプション_ - エンドポイントが Basic 認証よりも後方にある場合は、このパラメータを使用し、次の値でユーザー名とパスワードを定義します。`{"username": "<ユーザー名>", "password": "<パスワード>"}`。
* **`timeout`** - _オプション_ - リクエストがいつタイムアウトするか。
* **`headers`** - _テストでオプション_ - API リクエストのヘッダー。
* **`body`** - _API テストでオプション_ - API リクエストの本文。テキスト文字列を受け取ります (JSON テキスト文字列など)。`headers` パラメーターで、`Content-Type` `property` パラメーターとタイプ (例: `application/json`、`text/plain`) を使用してタイプを指定します。
* **`cookies`** - _API テストでオプション_ - API テストのリクエストと一緒に送信する cookie。

**オプション**:

**`options`** はブラウザ、API、SSL のテストで必須の引数です。カスタムリクエストヘッダー、認証資格情報、本文コンテンツ、cookie を指定したり、リダイレクトされるままテストを行ったりするために使用します。値が指定されないオプションパラメーターは、すべてデフォルト値を取ります。JSON オブジェクトであり、以下の属性を使用できます。

* **`tick_every`:** - _必須_ - どのくらいの頻度でテストを実行するか (秒単位。現在使用できる値は 60、300、900、1800、3600、21600、43200、86400、604800)。
* **`min_failure_duration`** - オプション - どのくらいの時間テストが失敗するとアラートになるか (整数の秒数。最大値は 7200)。デフォルトは 0 です。
* **`min_location_failed`** - _オプション_ -`min_failure_duration` 期間の少なくともある瞬間に少なくとも何個の場所が同時に失敗するか (min_location_failed および min_failure_duration は、高度なアラート規則の一部  - 1 以上の整数)。デフォルトは 1 です。
* **`follow_redirects`** - _オプション_ - ブール値 - リダイレクトに従うかどうか (最大 10 回のリダイレクトに従い、それを超えると「Too many redirects」エラーをトリガーします)。有効な値は `true` または `false` です。デフォルト値は `false` です。
* **`device_ids`** - _ブラウザテストで必須_ - テストに使用するデバイスの種類。使用できるデバイスのリストを取得するには、`GET devices for browser checks` メソッドの応答に含まれる `id` を使用します。少なくとも 1 つのデバイスが必要です。

**アサーション**:

**`assertions`** は、API と SSL のテストで必須となる引数です。テストを合格と見なすために起こるべきことを正確に定義できます。**JSON オブジェクトの配列**であり、次の属性を持ちます。

* **`type`** - _必須_ - 応答の中で評価したい部分。次のタイプがあります。

  * `header`: ヘッダーを定義するには、ヘッダーのパラメーターキーを `property` パラメーターに、ヘッダーのパラメーター値を `target` パラメーターに指定する必要があります。
  * `body`: `target` 属性を使用して、`body` に予期される値を指定します。
  * `responseTime`: `target` 属性を使用して、`responseTime` に予期される値を指定します。例: `"target":180000`
  * `statusCode`: `target` 属性を使用して、`statusCode` に予期される値を指定します。例: `"target":403`
  * `certificate`:  SSL テストでのみ使用し、`target` 属性を使用して `certificate` に予期される値を指定します。`"target":1` は有効な証明書であり、`"target":0` は無効な証明書です。
  * `property`: SSL テストでのみ使用。証明書の `property` を定義するには、証明書プロパティキーを `property` パラメーターに、証明書プロパティ値を `target` パラメーターに指定する必要があります。

* **`target`** - _必須_ - アサーションに予期される値。
* **`property`** - _オプション_ - `type` パラメーターに `header` を設定する際、ヘッダーのパラメーターキーを定義するためにこれが必要になります。有効な値は `Content-Type` や `Authorization` などの任意のヘッダーキーです。
* **`operator`** - _必須_ - ターゲットと、応答からの実際の値を比較する方法を定義します。有効な演算子はアサーションの `type` に依存します。以下に、型別の有効な演算子を一覧で示します。

| type           | 有効な演算子                                                              | 値の型                                                                                                 |
|----------------|-----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|
| `header`       | `contains`、`does not contain`、`is`、`is not`、`matches`、`does not match` | `contains`/`does not contain`/`is`/`is not` の場合は文字列。`matches`/`does not match` の場合は [RegexString][3]。 |
| `body`         | `contains`、`does not contain`、`is`、`is not`、`matches`、`does not match` | `contains`/`does not contain`/`is`/`is not` の場合は文字列。`matches`/`does not match` の場合は [RegexString][3]。 |
| `responseTime` | `lessThan`                                                                  | 整数                                                                                                    |
| `statusCode`   | `is`、`is not`                                                              | 整数                                                                                                    |
| `certificate`  | `isInMoreThan`                                                              | 整数                                                                                                    |
| `property`     | `contains`、`does not contain`、`is`、`is not`、`matches`、`does not match` | `contains`/`does not contain`/`is`/`is not` の場合は文字列。`matches`/`does not match` の場合は [RegexString][3]。 |

[1]: /ja/synthetics/browser_tests/#record-test
[2]: /ja/tagging/using_tags
[3]: https://en.wikipedia.org/wiki/Regular_expression