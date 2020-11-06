---
title: Azure 應用程式組態 REST API-Key-Value
description: 使用 Azure 應用程式組態 REST API 處理索引鍵/值的參考頁面
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 50d97a330507e9361674776acf29d1007ee5bf58
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423925"
---
# <a name="key-values"></a>Key-Values

api 版本：1。0

索引鍵/值是由的唯一組合所識別的資源 `key`  +  `label` 。 `label` 是選擇性的。 若要明確參考沒有標籤的索引鍵/值，請使用 "\ 0" (以) 編碼的 url ``%00`` 。 查看每項作業的詳細資料。

## <a name="operations"></a>Operations

- Get
- 清單多個
- 設定
- 刪除

## <a name="prerequisites"></a>必要條件

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="syntax"></a>語法

```json
{
  "etag": [string],
  "key": [string],
  "label": [string, optional],
  "content_type": [string, optional],
  "value": [string],
  "last_modified": [datetime ISO 8601],
  "locked": [boolean],
  "tags": [object with string properties, optional]
}
```

## <a name="get-key-value"></a>取得 Key-Value

**必要：** ``{key}`` 、 ``{api-version}``  
*選用：* ``label`` -如果省略，則表示沒有標籤的索引鍵/值

```http
GET /kv/{key}?label={label}&api-version={api-version}
```

**反應：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26+00:00",
  "locked": "false",
  "tags": {
    "t1": "value1",
    "t2": "value2"
  }
}
```

如果機碼不存在，則會傳回下列回應：

```http
HTTP/1.1 404 Not Found
```

## <a name="get-conditionally"></a>有條件地取得 () 

若要改善用戶端快取，請使用 `If-Match` 或 `If-None-Match` 要求標頭。 `etag`引數是索引鍵標記法的一部分。 請參閱 [14.24 和14.26 一節](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)。

只有當目前的標記法不符合指定的時，下列要求才會抓取索引鍵/值 `etag` ：

```http
GET /kv/{key}?api-version={api-version} HTTP/1.1
Accept: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "{etag}"
```

**反應：**

```http
HTTP/1.1 304 NotModified
```

或

```http
HTTP/1.1 200 OK
```

## <a name="list-key-values"></a>列出 Key-Values

請參閱 **篩選** 其他選項

*選用：* ``key`` -如果未指定，則會隱含 **任何** 索引鍵。
*選用：* ``label`` -如果未指定，則表示 **任何** 標籤。

```http
GET /kv?label=*&api-version={api-version} HTTP/1.1
```

**回應：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8
```

## <a name="pagination"></a>分頁

如果傳回的專案數超過回應限制，則會將結果編頁。 遵循選用的 `Link` 回應標頭，並使用 `rel="next"` 進行導覽。
或者，內容會以屬性的形式提供下一個連結 `@nextLink` 。 連結的 uri 包含 `api-version` 引數。

```http
GET /kv?api-version={api-version} HTTP/1.1
```

**回應：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="filtering"></a>篩選

`key`支援和篩選的組合 `label` 。
使用選擇性 `key` 和 `label` 查詢字串參數。

```http
GET /kv?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>支援的篩選器

|索引鍵篩選|作用|
|--|--|
|省略 `key` 或使用 `key=*`|符合 **任何** 索引鍵|
|`key=abc`|符合名為 **abc** 的索引鍵|
|`key=abc*`|符合以 **abc** 開頭的索引鍵名稱|
|`key=abc,xyz`|符合索引鍵名稱 **abc** 或 **xyz** (限制為5個 CSV) |

|標籤篩選|作用|
|--|--|
|省略 `label` 或使用 `label=*`|符合 **任何** 標籤|
|`label=%00`|符合不含標籤的 KV|
|`label=prod`|符合標籤 **生產**|
|`label=prod*`|符合從 **生產** 開始的標籤|
|`label=prod,test`|符合標籤 **生產** 或 **測試** (限制為5個 CSV) |

**_保留字元_* _

`_`, `\`, `,`

如果保留字元是值的一部分，則必須使用來將它換成 `\{Reserved Character}` 。 非保留字元也可以進行轉義。

***篩選驗證** _

在篩選驗證錯誤的情況下，回應會是 HTTP `400` 與錯誤詳細資料：

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

_ *範例**

- 全部

    ```http
    GET /kv?api-version={api-version}
    ```

- 索引鍵名稱以 **abc** 開頭，並包含所有標籤

    ```http
    GET /kv?key=abc*&label=*&api-version={api-version}
    ```

- 索引鍵名稱以 **abc** 開頭，標籤等於 **v1** 或 **v2**

    ```http
    GET /kv?key=abc*&label=v1,v2&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>要求特定欄位

使用選擇性 `$select` 查詢字串參數，並提供以逗號分隔的要求欄位清單。 如果 `$select` 省略此參數，則回應會包含預設集合。

```http
GET /kv?$select=key,value&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Time-Based 存取

取得過去一次結果的標記法。 請參閱 [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)區段。 如上面所定義，仍支援分頁。

```http
GET /kv?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**回應：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```

## <a name="set-key"></a>設定索引鍵

- **必要：**``{key}``
- *選用：* ``label`` -如果未指定或標籤 = %00，則表示 KV 沒有標籤。

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
```

```json
{
  "value": "example value",         // optional
  "content_type": "user defined",   // optional
  "tags": {                         // optional
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

**反應：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": "user defined",
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26.4874615+00:00",
  "tags": {
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

如果專案已鎖定，您將會收到下列回應：

```http
HTTP/1.1 409 Conflict
Content-Type: application/problem+json; charset="utf-8"
```

```json
{
    "type": "https://azconfig.io/errors/key-locked",
    "title": "Modifing key '{key}' is not allowed",
    "name": "{key}",
    "detail": "The key is read-only. To allow modification unlock it first.",
    "status": "409"
}
```

## <a name="set-key-conditionally"></a>依條件 (設定金鑰) 

若要防止競爭條件，請使用 `If-Match` 或 `If-None-Match` 要求標頭。 `etag`引數是索引鍵標記法的一部分。
如果 `If-Match` `If-None-Match` 省略或，則作業將會無條件進行。

只有當目前的標記法符合指定的時，下列回應才會更新此值。 `etag`

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

只有當目前的標記法不符合指定的時，下列回應才 *會* 更新此值。 `etag`

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

只有當標記法已經存在時，下列要求才會加入值：

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-Match: "*"
```

只有當標記法尚未存在時，下列要求才 *會* 新增值：

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-None-Match: "*"
```

**回應**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

或

```http
HTTP/1.1 412 PreconditionFailed
```

## <a name="delete"></a>刪除

- **必要：** `{key}` 、 `{api-version}`
- *選用：* `{label}` -如果未指定或標籤 = %00，則表示 KV 沒有標籤。

```http
DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**回應：** 傳回已刪除的索引鍵/值，如果索引鍵/值不存在，則為 none。

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

或

```http
HTTP/1.1 204 No Content
```

## <a name="delete-key-conditionally"></a>依條件) 刪除金鑰 (

與 **Set Key (有條件地)**
