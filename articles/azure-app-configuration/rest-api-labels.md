---
title: Azure 應用程式組態 REST API-標籤
description: 使用 Azure 應用程式組態 REST API 處理標籤的參考頁面
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: e6fcc8399c1dbc36a5b2e915c726f1c2496ee2f9
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423923"
---
# <a name="labels"></a>標籤

api 版本：1。0

**標籤** 資源的定義如下：

```json
{
      "name": [string]             // Name of the label
}
```

支援下列作業：

- 清單

所有作業 ``name`` 都是選擇性的篩選參數。 如果省略，則表示 **任何** 標籤。

## <a name="prerequisites"></a>必要條件

- 所有 HTTP 要求都必須經過驗證。 請參閱 [驗證](./rest-api-authentication-index.md) 一節。
- 所有 HTTP 要求都必須提供明確 `api-version` 的。 請參閱 [版本控制](./rest-api-versioning.md) 一節。

## <a name="list-labels"></a>列出標籤

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**反應：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{label-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>分頁

如果傳回的專案數超過回應限制，則會將結果編頁。 遵循選用的 `Link` 回應標頭，並使用 `rel="next"` 進行導覽。 或者，內容會以屬性的形式提供下一個連結 `@nextLink` 。 下一個連結包含 `api-version` 參數。

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**回應：**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8
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

## <a name="filtering"></a>篩選

支援的篩選 `name` 。

```http
GET /labels?name={label-name}&api-version={api-version}
```

### <a name="supported-filters"></a>支援的篩選器

|索引鍵篩選|作用|
|--|--|
|省略 `name` 或使用 `name=*`|符合 **任何** 標籤|
|`name=abc`|符合名為 **abc** 的標籤|
|`name=abc*`|符合以 **abc** 開頭的標籤名稱|
|`name=abc,xyz`|符合標籤名稱 **abc** 或 **xyz** (限制為5個 CSV) |

### <a name="reserved-characters"></a>保留字元

`*`, `\`, `,`

如果保留字元是值的一部分，則必須使用來將它換成 `\{Reserved Character}` 。 非保留字元也可以進行轉義。

### <a name="filter-validation"></a>篩選驗證

如果發生篩選驗證錯誤，回應會是 HTTP， `400` 並提供錯誤詳細資料：

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter 'name'",
  "name": "name",
  "detail": "name(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>範例

- 全部

    ```http
    GET /labels?api-version={api-version}
    ```

- 標籤名稱以 **abc** 開頭

    ```http
    GET  /labels?name=abc*&api-version={api-version}
    ```

- 標籤名稱可以是 **abc** 或 **xyz**

    ```http
    GET /labels?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>要求特定欄位

使用選擇性 `$select` 查詢字串參數，並提供以逗號分隔的要求欄位清單。 如果 `$select` 省略此參數，則回應會包含預設集合。

```http
GET /labels?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Time-Based 存取

取得過去一次結果的標記法。 請參閱[2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)章節

```http
GET /labels&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**回應：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json"
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
