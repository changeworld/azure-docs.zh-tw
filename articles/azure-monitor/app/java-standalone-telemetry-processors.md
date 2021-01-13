---
title: 適用于 JAVA 的遙測處理器 (預覽) -Azure 監視器 Application Insights
description: 如何在適用于 JAVA 的 Azure 監視器 Application Insights 中設定遙測處理器
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 39897e490e4653fbaad7a64ecc0b33f161d1264b
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165785"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>適用于 JAVA 的遙測處理器 (預覽) -Azure 監視器 Application Insights

> [!NOTE]
> 這項功能仍處於預覽狀態。

適用于 Application Insights 的 JAVA 3.0 代理程式現在具有可在資料匯出之前處理遙測資料的功能。

以下是遙測處理器的一些使用案例：
 * 遮罩敏感性資料
 * 有條件地加入自訂維度
 * 更新用於匯總並顯示在 Azure 入口網站中的名稱
 * 捨棄範圍屬性以控制內嵌成本

## <a name="terminology"></a>詞彙

在我們跳到遙測處理器之前，請務必先瞭解「範圍」（span）所參考的內容。

範圍是下列三種情況的一般詞彙：

* 傳入要求
* 外寄相依性 (例如對另一個服務的遠端呼叫) 
* 同進程的相依性 (例如，服務的子元件所進行的工作) 

針對遙測處理器用途，範圍的重要元件如下：

* 名稱
* 屬性

範圍名稱是用於 Azure 入口網站中的要求和相依性的主要顯示。

Span 屬性代表給定要求或相依性的標準和自訂屬性。

## <a name="telemetry-processor-types"></a>遙測處理器類型

目前有兩種類型的遙測處理器。

#### <a name="attribute-processor"></a>屬性處理器

屬性處理器可以插入、更新、刪除或雜湊屬性。
它也可以透過正則運算式來將 (解壓縮) 現有屬性中的一個或多個新屬性。

#### <a name="span-processor"></a>範圍處理器

範圍處理器可以更新遙測名稱。
它也可以透過正則運算式來將 (解壓縮) 範圍名稱中的一或多個新屬性。

> [!NOTE]
> 請注意，目前的遙測處理器只會處理類型字串的屬性，而且不會處理布林值或數位類型的屬性。

## <a name="getting-started"></a>開始使用

建立名為的設定檔 `applicationinsights.json` ，並 `applicationinsights-agent-*.jar` 使用下列範本，將它放在與相同的目錄中。

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

## <a name="includeexclude-criteria"></a>包含/排除準則

屬性處理器和 span 處理器都支援選擇性 `include` 和 `exclude` 準則。
處理器只會套用至符合條件的範圍 `include` (如果有提供) _，而且_ 不符合其 `exclude` 準則 (（如果有提供) ）。

若要設定此選項， `include` 必須在和/或 `exclude` 至少一個 `matchType` 和 `spanNames` 或 `attributes` 。
包含/排除設定支援有一個以上的指定條件。
所有指定的條件都必須評估為 true，才會發生相符的情況。 

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
      }
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
      }
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
      }
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
      }
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
      }
    ]
  }
]
```
`extract`以下是必要動作
* `key`
* `pattern`
* `action` : `extract`

若要深入瞭解，請參閱 [遙測處理器範例](./java-standalone-telemetry-processors-examples.md) 檔。

## <a name="span-processor"></a>範圍處理器

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
