---
title: Azure 應用程式組態 REST API 金鑰
description: 使用 Azure 應用程式組態 REST API 處理金鑰的參考頁面
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: f44ecdf571791d54a78d25dde514d57053b59160
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424090"
---
# <a name="keys"></a>索引鍵

api 版本：1。0

下列語法代表金鑰資源：

```http
{
    "name": [string]             // Name of the key
}
```

## <a name="operations"></a>Operations

重要資源支援下列操作：

- 清單

所有作業 `name` 都是選擇性的篩選參數。 如果省略，則表示 *任何* 金鑰。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-keys"></a>列出金鑰

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**反應：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{key-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>分頁

如果傳回的專案數超過回應限制，則會將結果編頁。 遵循選用的 `Link` 回應標頭，並使用 `rel="next"` 進行導覽。 或者，內容會以屬性的形式提供下一個連結 `@nextLink` 。 下一個連結包含 `api-version` 參數。

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**回應：**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8
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

支援的篩選 ```name``` 。

```http
GET /keys?name={key-name}&api-version={api-version}
```

以下是支援的篩選：

|索引鍵篩選|作用|
|--|--|
|省略 `name` 或使用 `name=*`|符合 **任何** 索引鍵|
|`name=abc`|符合名為 **abc** 的索引鍵|
|`name=abc*`|符合以 **abc** 開頭的索引鍵名稱|
|`name=abc,xyz`|符合索引鍵名稱 **abc** 或 **xyz** (限制為5個 CSV) |

保留下列字元： `*` 、 `\` 、 `,`

如果保留字元是值的一部分，則必須使用來將它換成 `\{Reserved Character}` 。 非保留字元也可以進行轉義。

## <a name="filter-validation"></a>篩選驗證

在篩選驗證錯誤的情況下，回應會是 HTTP `400` 與錯誤詳細資料：

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

## <a name="examples"></a>範例

- 全部

    ```http
    GET /keys?api-version={api-version}
    ```

- 索引鍵名稱以 **abc** 開頭

    ```http
    GET  /keys?name=abc*&api-version={api-version}
    ```

- 索引鍵名稱為 **abc** 或 **xyz**

    ```http
    GET /keys?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>要求特定欄位

使用選擇性 `$select` 查詢字串參數，並提供以逗號分隔的要求欄位清單。 如果 `$select` 省略此參數，則回應會包含預設集合。

```http
GET /keys?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Time-Based 存取

取得過去一次結果的標記法。 請參閱[2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)章節

```http
GET /keys&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**回應：**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <relative uri>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
