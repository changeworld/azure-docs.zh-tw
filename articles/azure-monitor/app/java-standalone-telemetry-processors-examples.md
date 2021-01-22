---
title: 遙測處理器範例-適用于 JAVA 的 Azure 監視器 Application Insights
description: 說明 Azure 監視器 Application Insights for JAVA 的遙測處理器範例
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 9b29c9611359c97c4097ad0b90ee2673bb28f37c
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696307"
---
# <a name="telemetry-processors-examples---azure-monitor-application-insights-for-java"></a>遙測處理器範例-適用于 JAVA 的 Azure 監視器 Application Insights

## <a name="includeexclude-samples"></a>包含/排除範例

### <a name="1-include-spans"></a>1. 包含範圍

下列範例將示範包含此屬性處理器的範圍。 所有不符合屬性的其他範圍都不會由此處理器處理。

以下是符合的條件：
* 範圍名稱必須等於 "spanA" 或 "spanB" 

以下是符合 include 屬性和套用處理器動作的範圍。
* Span1 Name： ' spanA ' 屬性： {env： dev，test_request：123，credit_card： 1234}
* Span2 Name： ' spanB ' 屬性： {env： dev，test_request： false}
* Span3 Name： ' spanA ' 屬性： {env：1，test_request： dev，credit_card： 1234}

下列範圍不符合 include 屬性，也不會套用處理器動作。
* Span4 Name： ' spanC ' 屬性： {env： dev，test_request： false}

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
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="2-exclude-spans"></a>2. 排除範圍

下列範例會示範排除此屬性處理器的範圍。 所有符合屬性的範圍都不會由此處理器處理。

以下是符合的條件：
* 範圍名稱必須等於 "spanA" 或 "spanB" 

以下是符合排除屬性的範圍，而不會套用處理器動作。
* Span1 Name： ' spanA ' 屬性： {env： dev，test_request：123，credit_card： 1234}
* Span2 Name： ' spanB ' 屬性： {env： dev，test_request： false}
* Span3 Name： ' spanA ' 屬性： {env：1，test_request： dev，credit_card： 1234}

下列範圍不符合排除屬性，而且會套用處理器動作。
* Span4 Name： ' spanC ' 屬性： {env： dev，test_request： false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="3-excludemulti-spans"></a>3. ExcludeMulti 範圍

下列範例會示範排除此屬性處理器的範圍。 所有符合屬性的範圍都不會由此處理器處理。

以下是符合的條件：
* 符合的範圍中必須有 ' env '、' dev ' )  ( 屬性。
* 只要範圍內有索引鍵為 ' test_request ' 的屬性，就會有相符的。

以下是符合排除屬性的範圍，而不會套用處理器動作。
* Span1 Name： ' spanB ' 屬性： {env： dev，test_request：123，credit_card： 1234}
* Span2 Name： ' spanA ' 屬性： {env： dev，test_request： false}

下列範圍不符合排除屬性，而且會套用處理器動作。
* Span3 Name： ' spanB ' 屬性： {env：1，test_request： dev，credit_card： 1234}
* Span4 Name： ' spanC ' 屬性： {env： dev，dev_request： false}


```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ],
          "attributes": [
            {
              "key": "env",
              "value": "dev"
            },
            {
              "key": "test_request"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="4-selective-processing"></a>4. 選擇性處理

下列範例將示範如何指定範圍屬性的集合，以指出應套用此處理器的範圍。 `include`屬性的屬性（property）表示應該包含哪些屬性，以及 `exclude` 進一步篩選出不應該處理的屬性。

使用下列設定時，下列範圍會符合套用的屬性和處理器動作：

* Span1 Name： ' spanB ' 屬性： {env：生產環境，test_request：123，credit_card：1234，redact_trace： "false"}
* Span2 Name： ' spanA ' 屬性： {env：暫存，test_request： false，redact_trace： true}

下列範圍不符合 include 屬性，也不會套用處理器動作：
* Span3 Name： ' spanB ' 屬性： {env：生產環境，test_request： true，credit_card：1234，redact_trace： false}
* Span4 Name： ' spanC ' 屬性： {env： dev，test_request： false}

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
  }
}
```
## <a name="attribute-processor-samples"></a>屬性處理器範例

### <a name="insert"></a>插入

以下會將新屬性 {"attribute1"： "attributeValue1"} 插入至不存在索引鍵 "attribute1" 的範圍。

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
            "value": "attributeValue1",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="insert-from-another-key"></a>從另一個索引鍵插入

以下會使用屬性 "anotherkey" 的值，將新的屬性 {"newKey"： "值從屬性 ' anotherkey '} 插入至不存在索引鍵" newKey "的範圍。 如果屬性 ' anotherkey ' 不存在，則不會在範圍中插入新的屬性。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "newKey",
            "fromAttribute": "anotherKey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="update"></a>更新

下列內容會將屬性更新為 {"db. secret"： "修訂"}，並使用屬性 ' foo ' 的值更新屬性 ' boo '。 沒有屬性 ' boo ' 的範圍將不會變更。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "db.secret",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "boo",
            "fromAttribute": "foo",
            "action": "update" 
          }
        ]
      }
    ]
  }
}
```

### <a name="delete"></a>刪除

下列範例將示範如何使用索引鍵 ' credit_card ' 刪除屬性。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="hash"></a>雜湊

以下示範雜湊現有的屬性值。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
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

### <a name="extract"></a>Extract

下列範例將示範如何使用 RegEx，根據另一個屬性的值來建立新的屬性。
例如，假設有 HTTP. url = ' http://example.com/path?queryParam1=value1 ，queryParam2 = value2 '，將會插入下列屬性：
* HTTPProtocol： HTTP
* HTTPDomain： example.com
* HTTPPath： path
* HTTPQueryParams： queryParam1 = value1，queryParam2 = value2
* HTTP. url 值不會變更。

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "http.url",
            "pattern": "^(?<httpProtocol>.*):\\/\\/(?<httpDomain>.*)\\/(?<httpPath>.*)(\\?|\\&)(?<httpQueryParams>.*)",
            "action": "extract"
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


## <a name="span-processor-samples"></a>範圍處理器範例

### <a name="name-a-span"></a>命名範圍

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

### <a name="extract-attributes-from-span-name-with-include-and-exclude"></a>使用 include 和 exclude 從範圍名稱解壓縮屬性

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