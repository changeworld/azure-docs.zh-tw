---
title: 適用于 JAVA 的遙測處理器 (預覽) -Azure 監視器 Application Insights
description: 如何在適用于 JAVA 的 Azure 監視器 Application Insights 中設定遙測處理器
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: ba4e6b8b5e9db494ab4c0c372c2086087a2d58cb
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133169"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>適用于 JAVA 的遙測處理器 (預覽) -Azure 監視器 Application Insights

> [!NOTE]
> 這項功能仍處於預覽狀態。

適用于 Application Insights 的 JAVA 3.0 代理程式現在具有可在資料匯出之前處理遙測資料的功能。

以下是遙測處理器的一些使用案例：
 * 遮罩敏感性資料
 * 有條件地加入自訂維度
 * 更新用於匯總和顯示的遙測名稱
 * Drop 或 filter span 屬性以控制內嵌成本

## <a name="terminology"></a>詞彙

在我們跳到遙測處理器之前，請務必瞭解追蹤和範圍是什麼。

### <a name="traces"></a>追蹤

追蹤會追蹤單一要求的進展（稱為 a `trace` ），因為它是由構成應用程式的服務所處理。 要求可能會由使用者或應用程式起始。 中的每個工作單位 `trace` 稱為 a `span` ; a `trace` 是範圍的樹狀結構。 `trace`是由單一根範圍和任意數目的子範圍所組成。

### <a name="span"></a>跨度

「範圍」（span）是代表個別服務或元件流經系統時所執行之工作的物件。 `span`包含 `span context` ，它是一組全域唯一識別碼，代表每個範圍所屬的唯一要求。 

範圍封裝：

* 範圍名稱
* 可 `SpanContext` 唯一識別範圍的不可變
* 的父範圍 `Span` ，以、 `SpanContext` 或 null 形式
* `SpanKind`
* 開始時間戳
* 結束時間戳記
* [`Attributes`](#attributes)
* 有時間戳記的事件清單
* `Status`。

一般來說，範圍的生命週期如下所示：

* 服務收到要求。 範圍內容會從要求標頭中解壓縮（如果有的話）。
* 新的範圍會建立為已解壓縮的範圍內容的子系;如果不存在，則會建立新的根範圍。
* 服務會處理要求。 額外的屬性和事件會新增至範圍，此範圍有助於瞭解要求的內容，例如處理要求的電腦主機名稱或客戶識別碼。
* 您可以建立新的範圍，以代表服務的子元件所進行的工作。
* 當服務對另一個服務進行遠端呼叫時，會將範圍內容插入標頭或訊息信封中，藉以序列化目前的範圍內容並轉送至下一個服務。
* 服務所執行的工作已成功完成。 範圍狀態會適當設定，且範圍會標示為已完成。

### <a name="attributes"></a>屬性

`Attributes` 這是在中封裝的零或多個索引鍵/值組清單 `span` 。 屬性必須具有下列屬性：

屬性索引鍵，必須是非 null 和非空白的字串。
屬性值，也就是：
* 基本類型：字串、布林值、雙精確度浮點數 (IEEE 754-1985) 或帶正負號的64位整數。
* 基本類型值的陣列。 陣列必須是同質，亦即它不能包含不同類型的值。 對於原本不支援陣列值的通訊協定，這類值應該以 JSON 字串表示。

## <a name="supported-processors"></a>支援的處理器：
 * 屬性處理器
 * 範圍處理器

## <a name="to-get-started"></a>快速入門

建立名為的設定檔 `applicationinsights.json` ，並 `applicationinsights-agent-***.jar` 使用下列範本，將它放在與相同的目錄中。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="includeexclude-spans"></a>包含/排除範圍

屬性處理器和範圍處理器會公開選項，以提供一組要比對的範圍屬性，以判斷是否應該在遙測處理器中包含或排除該範圍。 若要設定此選項， `include` 必須在和/或 `exclude` 至少一個 `matchType` 和 `spanNames` 或 `attributes` 。 包含/排除設定支援有一個以上的指定條件。 所有指定的條件都必須評估為 true，才會發生相符的情況。 

**必要欄位**： 
* `matchType` 控制如何 `spanNames` 解讀和陣列中的專案 `attributes` 。 可能的值為 `regexp` 或 `strict`。 

**選用欄位**： 
* `spanNames` 至少必須符合其中一個專案。 
* `attributes` 指定要比對的屬性清單。 所有這些屬性都必須完全相符，才會發生相符的情況。

> [!NOTE]
> 如果同時 `include` 指定了和，則屬性 `exclude` 會在 `include` 屬性之前檢查 `exclude` 。

#### <a name="sample-usage"></a>範例用法

```json

"processors": [
  {
    "type": "attribute",
    "include": {
      "matchType": "strict",
      "spanNames": [
        "spanA",
        "spanB"
      ]
    },
    "exclude": {
      "matchType": "strict",
      "attributes": [
        {
          "key": "redact_trace",
          "value": "false"
        }
      ]
    },
    "actions": [
      {
        "key": "credit_card",
        "action": "delete"
      },
      {
        "key": "duplicate_key",
        "action": "delete"
      }
    ]
  }
]
```
若要深入瞭解，請參閱 [遙測處理器範例](./java-standalone-telemetry-processors-examples.md) 檔。

## <a name="attribute-processor"></a>屬性處理器 

屬性處理器會修改範圍的屬性。 它可選擇性地支援包含/排除範圍的功能。 它會採用在設定檔中指定的循序執行的動作清單。 支援的動作包括：

### `insert`

在索引鍵不存在的範圍中插入新的屬性。   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      },
    ]
  }
]
```
`insert`以下是必要動作
  * `key`
  * 或的其中一個 `value``fromAttribute`
  * `action`:`insert`

### `update`

更新索引鍵存在的範圍中的屬性

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      },
    ]
  }
]
```
`update`以下是必要動作
  * `key`
  * 或的其中一個 `value``fromAttribute`
  * `action`:`update`


### `delete` 

從範圍中刪除屬性

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      },
    ]
  }
]
```
`delete`以下是必要動作
  * `key`
  * `action`: `delete`

### `hash`

 (SHA1) 現有屬性值的雜湊

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      },
    ]
  }
]
```
`hash`以下是必要動作
* `key`
* `action` : `hash`

### `extract`

> [!NOTE]
> 這項功能僅適用于3.0.1 和更新版本

使用來自輸入索引鍵的正則運算式規則，將值解壓縮至規則中指定的目標索引鍵。 如果目標索引鍵已經存在，則會覆寫它。 其行為類似于[](#extract-attributes-from-span-name) `toAttributes` 使用現有屬性作為來源的範圍處理器設定。

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      },
    ]
  }
]
```
`extract`以下是必要動作
* `key`
* `pattern`
* `action` : `extract`

若要深入瞭解，請參閱 [遙測處理器範例](./java-standalone-telemetry-processors-examples.md) 檔。

## <a name="span-processors"></a>範圍處理器

範圍處理器會根據範圍名稱修改範圍的範圍名稱或屬性。 它可選擇性地支援包含/排除範圍的功能。

### <a name="name-a-span"></a>命名範圍

以下是 [名稱] 區段中所需的設定：

* `fromAttributes`：索引鍵的屬性值是用來依照設定中指定的順序來建立新的名稱。 您必須在範圍中指定所有屬性索引鍵，處理器才能將其重新命名。

您可以選擇性地設定下列設定：

* `separator`：指定將用來分割值的字串
> [!NOTE]
> 如果重新命名相依于屬性處理器正在修改的屬性，請確定已在管線規格中的屬性處理器之後指定範圍處理器。

```json
"processors": [
  {
    "type": "span",
    "name": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-span-name"></a>從範圍名稱解壓縮屬性

取得正則運算式清單，以比對範圍名稱，並根據子運算式從它的屬性中解壓縮屬性。 必須在區段下指定 `toAttributes` 。

需要下列設定：

`rules` ：從範圍名稱解壓縮屬性值的規則清單。 範圍名稱中的值會被解壓縮的屬性名稱取代。 清單中的每個規則都是 RegEx 模式字串。 針對 RegEx 檢查範圍名稱。 如果 RegEx 相符，RegEx 的所有名稱子運算式都會解壓縮為屬性，並加入至範圍中。 每個子運算式名稱都會變成屬性名稱，而子運算式相符的部分會成為屬性值。 範圍名稱中相符的部分會以已解壓縮的屬性名稱取代。 如果屬性已經存在於範圍中，則會覆寫這些屬性。 系統會依照指定的順序，為所有規則重複此程式。 每個後續規則都適用于在處理先前規則之後輸出的範圍名稱。

```json

"processors": [
  {
    "type": "span",
    "name": {
      "toAttributes": {
        "rules": [
          "rule1",
          "rule2",
          "rule3"
        ]
      }
    }
  }
]

```

## <a name="list-of-attributes"></a>屬性清單

以下是可在遙測處理器中使用的一些常見範圍屬性清單。

### <a name="http-spans"></a>HTTP 範圍

| 屬性  | 類型 | 描述 | 
|---|---|---|
| `http.method` | 字串 | HTTP 要求方法。|
| `http.url` | 字串 | 表單中的完整 HTTP 要求 URL `scheme://host[:port]/path?query[#fragment]` 。 通常不會透過 HTTP 傳輸片段，但如果已知，則應該包含此片段。|
| `http.status_code` | number | [HTTP 回應狀態碼](https://tools.ietf.org/html/rfc7231#section-6)。|
| `http.flavor` | 字串 | 使用的 HTTP 通訊協定種類 |
| `http.user_agent` | 字串 | 用戶端傳送的 [HTTP 使用者代理程式](https://tools.ietf.org/html/rfc7231#section-5.5.3) 標頭值。 |

### <a name="jdbc-spans"></a>JDBC 範圍

| 屬性  | 類型 | 描述  |
|---|---|---|
| `db.system` | 字串 | 使用的資料庫管理系統 (DBMS) 產品的識別碼。 |
| `db.connection_string` | 字串 | 用來連接到資料庫的連接字串。 建議您移除內嵌認證。|
| `db.user` | 字串 | 用來存取資料庫的使用者名稱。 |
| `db.name` | 字串 | 這個屬性是用來報告要存取的資料庫名稱。 針對切換資料庫的命令，即使命令) 失敗，也應該將此設定為目標資料庫 (。|
| `db.statement` | 字串 | 正在執行的資料庫語句。|