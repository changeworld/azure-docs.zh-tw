---
title: 適用于 JAVA 的遙測處理器 (預覽) -Azure 監視器 Application Insights
description: 如何在適用于 JAVA 的 Azure 監視器 Application Insights 中設定遙測處理器
ms.topic: conceptual
ms.date: 10/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 7fd53c77b64e028ffad25c8fa7a9eefd95439513
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387151"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>適用于 JAVA 的遙測處理器 (預覽) -Azure 監視器 Application Insights

> [!NOTE]
> 這項功能仍處於預覽狀態。

適用于 Application Insights 的 JAVA 3.0 代理程式現在具有可在資料匯出之前處理遙測資料的功能。

### <a name="some-use-cases"></a>某些使用案例：
 * 遮罩敏感性資料
 * 有條件地加入自訂維度
 * 更新用於匯總和顯示的遙測名稱

### <a name="supported-processors"></a>支援的處理器：
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

屬性處理器和範圍處理器會公開選項，以提供一組要比對的範圍屬性，以判斷是否應該在處理器中包含或排除該範圍。 若要設定此選項， `include` 必須在和/或 `exclude` 至少一個 `matchType` 和 `spanNames` 或 `attributes` 。 包含/排除設定支援有一個以上的指定條件。 所有指定的條件都必須評估為 true，才會發生相符的情況。 

**必要欄位**： 
* `matchType` 控制如何 `spanNames` 解讀和陣列中的專案 `attributes` 。 可能的值為 `regexp` 或 `strict`。 

**選用欄位**： 
* `spanNames` 至少必須符合其中一個專案。 
* `attributes` 指定要比對的屬性清單。 所有這些屬性都必須完全相符，才會發生相符的情況。

> [!NOTE]
> 如果同時 `include` 指定了和，則屬性 `exclude` 會在 `include` 屬性之前檢查 `exclude` 。

#### <a name="sample-usage"></a>範例用法

下列範例將示範如何指定範圍屬性的集合，以指出應套用此處理器的範圍。 `include`屬性的屬性（property）表示應該包含哪些屬性，以及 `exclude` 進一步篩選出不應該處理的屬性。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "svcA",
            "svcB"
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
  }
}
```

使用上述設定時，下列範圍會符合套用的屬性和處理器動作：

* Span1 Name： ' svcB ' 屬性： {env：生產環境，test_request：123，credit_card：1234，redact_trace： "false"}

* Span2 Name： ' svcA ' 屬性： {env：暫存，test_request： false，redact_trace： true}

下列範圍不符合 include 屬性，也不會套用處理器動作：

* Span3 Name： ' svcB ' 屬性： {env：生產環境，test_request： true，credit_card：1234，redact_trace： false}

* Span4 Name： ' svcC ' 屬性： {env： dev，test_request： false}

## <a name="attribute-processor"></a>屬性處理器 

屬性處理器會修改範圍的屬性。 它可選擇性地支援包含/排除範圍的功能。
它會採用在設定檔中指定的循序執行的動作清單。 支援的動作包括：

* `insert` ：在索引鍵不存在的範圍中插入新的屬性
* `update` ：在索引鍵存在的範圍中更新屬性
* `delete` ：刪除範圍中的屬性
* `hash`   ： (SHA1) 現有屬性值的雜湊

針對動作 `insert` 和 `update`
* `key` 是必要項
* `value`需要或其中 `fromAttribute` 之一
* `action` 是必要的。

針對此 `delete` 動作，
* `key` 是必要項
* `action`： `delete` 是必要項。

針對此 `hash` 動作，
* `key` 是必要項
* `action` ： `hash` 是必要項。

您可以撰寫動作清單來建立豐富的案例，例如，背景填滿屬性、將值複製到新的索引鍵、校訂機密資訊。

#### <a name="sample-usage"></a>範例用法

下列範例示範如何將索引鍵/值插入至範圍：

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "value1",
            "action": "insert"
          },
          {
            "key": "key1",
            "fromAttribute": "anotherkey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

下列範例示範如何將處理器設定為只更新屬性中的現有索引鍵：

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "piiattribute",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

下列範例示範如何處理具有符合 RegExp 模式之範圍名稱的範圍。
此處理器會移除「權杖」屬性，並會在範圍名稱符合「驗證」的範圍中模糊化「密碼」 \* 屬性。而範圍名稱不符合 "login" \* 。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

## <a name="span-processors"></a>範圍處理器

範圍處理器會根據範圍名稱修改範圍的範圍名稱或屬性。 它可選擇性地支援包含/排除範圍的功能。

### <a name="name-a-span"></a>命名範圍

以下是 [名稱] 區段中所需的設定：

* `fromAttributes`：索引鍵的屬性值是用來依照設定中指定的順序來建立新的名稱。 您必須在範圍中指定所有屬性索引鍵，處理器才能將其重新命名。

您可以選擇性地設定下列設定：

* `separator`：指定將用來分割值的字串
> [!NOTE]
> 如果重新命名相依于屬性處理器正在修改的屬性，請確定已在管線規格中的屬性處理器之後指定範圍處理器。

#### <a name="sample-usage"></a>範例用法

下列範例會指定屬性 "db .svc"、"operation" 和 "id" 的值，以該順序來形成範圍的新名稱，並以值 "：：" 分隔。
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name"></a>從範圍名稱解壓縮屬性

取得正則運算式清單，以比對範圍名稱，並根據子運算式從它的屬性中解壓縮屬性。 必須在區段下指定 `toAttributes` 。

需要下列設定：

`rules` ：從範圍名稱解壓縮屬性值的規則清單。 範圍名稱中的值會被解壓縮的屬性名稱取代。 清單中的每個規則都是 RegEx 模式字串。 針對 RegEx 檢查範圍名稱。 如果 RegEx 相符，RegEx 的所有名稱子運算式都會解壓縮為屬性，並加入至範圍中。 每個子運算式名稱都會變成屬性名稱，而子運算式相符的部分會成為屬性值。 範圍名稱中相符的部分會以已解壓縮的屬性名稱取代。 如果屬性已經存在於範圍中，則會覆寫這些屬性。 系統會依照指定的順序，為所有規則重複此程式。 每個後續規則都適用于在處理先前規則之後輸出的範圍名稱。

#### <a name="sample-usage"></a>範例用法

讓我們假設輸入範圍名稱是/api/v1/document/12345678/update。 在輸出範圍名稱/api/v1/document/{documentId}/update 中套用下列結果，會將新屬性 "documentId" = "12345678" 新增至範圍。
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

下列範例將示範如何將範圍名稱重新命名為 "{operation_website}"，並在範圍具有下列屬性時新增屬性 {Key： operation_website，Value： oldSpanName}：
- 範圍名稱在字串中的任何位置都包含 '/'。
- 範圍名稱不是 ' 沒有/change '。
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```