---
title: Azure 應用程式組態 REST API-索引鍵-值修訂
description: 使用 Azure 應用程式組態 REST API 來處理索引鍵/值修訂的參考頁面
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 668345da8bb89412f7b1dd36975c5bed6f229580
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2020
ms.locfileid: "95246379"
---
# <a name="key-value-revisions"></a>索引鍵/值修訂

索引 *鍵-值修訂* 會定義索引鍵/值資源的歷程表示。 在免費層商店的7天后修訂到期日，或標準層商店的30天后到期。 修訂支援此作業 `List` 。

針對所有作業， ``key`` 都是選擇性參數。 如果省略，則表示任何金鑰。

針對所有作業， ``label`` 都是選擇性參數。 如果省略，則表示任何標籤。

本文適用于 API 版本1.0。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-revisions"></a>列出修訂

```http
GET /revisions?label=*&api-version={api-version} HTTP/1.1
```

**反應：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8"
Accept-Ranges: items
```

```json
{
    "items": [
        {
          "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
          "key": "{key}",
          "label": "{label}",
          "content_type": null,
          "value": "example value",
          "last_modified": "2017-12-05T02:41:26.4874615+00:00",
          "tags": []
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>分頁

如果傳回的專案數超過回應限制，則會將結果編頁。 遵循選用的 ``Link`` 回應標頭，並使用 ``rel="next"`` 進行導覽。 或者，內容會以屬性的形式提供下一個連結 ``@nextLink`` 。

```http
GET /revisions?api-version={api-version} HTTP/1.1
```

**回應：**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Accept-Ranges: items
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

## <a name="list-subset-of-revisions"></a>列出修訂的子集

使用 `Range` 要求標頭。 回應包含 `Content-Range` 標題。 如果伺服器無法滿足要求的範圍，它會以 HTTP `416` (`RangeNotSatisfiable`) 回應。

```http
GET /revisions?api-version={api-version} HTTP/1.1
Range: items=0-2
```

**回應**

```http
HTTP/1.1 206 Partial Content
Content-Type: application/vnd.microsoft.appconfig.revs+json; charset=utf-8
Content-Range: items 0-2/80
```

## <a name="filtering"></a>篩選

`key`支援和篩選的組合 `label` 。
使用選擇性 `key` 和 `label` 查詢字串參數。

```http
GET /revisions?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>支援的篩選器

|索引鍵篩選|效果|
|--|--|
|省略 `key` 或使用 `key=*`|符合 **任何** 索引鍵|
|`key=abc`|符合名為 **abc** 的索引鍵|
|`key=abc*`|符合以 **abc** 開頭的索引鍵名稱|
|`key=*abc`|符合以 **abc** 結尾的索引鍵名稱|
|`key=*abc*`|符合包含 **abc** 的索引鍵名稱|
|`key=abc,xyz`|符合索引鍵名稱 **abc** 或 **xyz** (限制為5個 CSV) |

|標籤篩選|效果|
|--|--|
|省略 `label` 或使用 `label=`|符合不含標籤的專案|
|`label=*`|符合 **任何** 標籤|
|`label=prod`|符合標籤 **生產**|
|`label=prod*`|符合從 **生產** 開始的標籤|
|`label=*prod`|符合以 **生產** 結尾的標籤|
|`label=*prod*`|符合包含 **生產** 的標籤|
|`label=prod,test`|符合標籤 **生產** 或 **測試** (限制為5個 CSV) |

### <a name="reserved-characters"></a>保留字元

保留的字元包括：

`*`, `\`, `,`

如果保留字元是值的一部分，則必須使用來將它加上轉義 `\{Reserved Character}` 。 非保留字元也可以進行轉義。

### <a name="filter-validation"></a>篩選驗證

如果發生篩選驗證錯誤，回應會是 HTTP， `400` 並提供錯誤詳細資料：

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

### <a name="examples"></a>範例

- 全部：

    ```http
    GET /revisions
    ```

- 索引鍵名稱以 **abc** 開頭的專案：

    ```http
    GET /revisions?key=abc*&api-version={api-version}
    ```

- 索引鍵名稱為 **abc** 或 **xyz** 的專案，且標籤包含 **生產**：

    ```http
    GET /revisions?key=abc,xyz&label=*prod*&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>要求特定欄位

使用選擇性 `$select` 查詢字串參數，並提供以逗號分隔的要求欄位清單。 如果 `$select` 省略此參數，則回應會包含預設集合。

```http
GET /revisions?$select=value,label,last_modified&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>以時間為基礎的存取

取得過去一次結果的標記法。 如需詳細資訊，請參閱 [Time-Based 存取資源狀態的 HTTP 架構--Memento](https://tools.ietf.org/html/rfc7089#section-2.1)，第2.1.1 節。

```http
GET /revisions?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**回應：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.revs+json"
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
