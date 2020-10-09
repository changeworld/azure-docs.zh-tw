---
title: Azure Batch 集區自動調整事件
description: Batch 集區自動調整事件的參考，會在執行集區自動調整之後發出。 記錄檔的內容將會公開集區的自動調整公式和評估結果。
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e548ed484e5f683a8f79434ce4095ac66900f01a
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91852106"
---
# <a name="pool-autoscale-event"></a>集區自動調整事件

 一旦執行集區自動調整之後，就會發出此事件。 記錄檔的內容將會公開集區的自動調整公式和評估結果。

 下列範例顯示集區自動調整的集區自動調整事件的內文，由於範例資料不足而失敗。

```
{
    "id": "myPool1",
    "timestamp": "2020-09-21T18:59:56.204Z",
    "formula": "...",
    "results": "...",
    "error": {
        "code": "InsufficientSampleData",
        "message": "Autoscale evaluation failed due to insufficient sample data",
        "values": [{
                "name": "Message",
                "value": "Line 15, Col 44: Insufficient data from data set: $RunningTasks wanted 70%, received 50%"
            }
        ]
    }
}
```

|元素|類型|注意|
|-------------|----------|-----------|
|`id`|String|集區識別碼。|
|`timestamp`|Datetime|執行自動調整的時間戳記。|
|`formula`|String|針對自動調整所定義的公式。|
|`results`|String|公式中所使用之所有變數的評估結果。|
|[`error`](#error)|複雜類型|自動調整的詳細錯誤。|

###  <a name="error"></a><a name="error"></a> 個錯誤

|元素名稱|類型|注意|
|------------------|----------|-----------|
|`code`|String|自動調整錯誤的識別碼。 程式碼是不變的，旨在以程式設計的方式取用。|
|`message`|String|描述自動調整錯誤的訊息，適合在使用者介面中顯示。|
|`values`|Array|名稱/值組的清單，描述自動調整錯誤的詳細資料。|
