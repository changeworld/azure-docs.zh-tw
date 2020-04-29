---
title: Azure 串流分析 Jobconfig.json 的 json 欄位
description: 本文列出在 Visual Studio Code 中用來建立作業的 Azure 串流分析 Jobconfig.json json 檔案支援的欄位。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77617953"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure 串流分析 Jobconfig.json 的 json 欄位

下欄欄位在用來[建立使用 Visual Studio Code 的 Azure 串流分析作業](quick-create-vs-code.md)的*jobconfig.json*中受到支援。

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|名稱|類型|必要|值|
|----|----|--------|-----|
|DataLocale|字串|否|串流分析工作的資料地區設定。 值應該是支援的名稱。 如果未指定，則預設為 ' en-us '。|
|OutputErrorPolicy|字串|否|指出要套用至到達輸出的事件，而且因為格式錯誤而無法寫入外部儲存體的原則（遺漏資料行值、錯誤類型或大小的資料行值）。 -Stop 或 Drop|
|EventsLateArrivalMaxDelayInSeconds|integer|否|最大可容忍延遲（以秒為單位），其中可能包含事件抵達延遲的時間。 支援的範圍是-1 到1814399（20.23：59：59天），而-1 是用來指定無限期等候。 如果屬性不存在，則會將其轉譯為值為-1。|
|EventsOutOfOrderMaxDelayInSeconds|integer|否|以秒為單位的最大可容忍延遲（秒），順序中的事件可以調整為反向順序。|
|EventsOutOfOrderPolicy|字串|否|指出要套用至輸入事件資料流程中未依順序送達之事件的原則。 -調整或捨棄|
|StreamingUnits|integer|是|指定串流作業使用的串流處理單位數。|
|CompatibilityLevel|字串|否|控制串流作業的特定執行時間行為。 -可接受的值為 "1.0"、"1.1"、"1.2"|
|UseSystemAssignedIdentity|boolean|否|設定 true 可讓此作業使用受控 Azure Active Directory 身分識別，來與其他 Azure 服務通訊。|
|GlobalStorage AccountName|字串|否|全域儲存體帳戶用來儲存串流分析作業的相關內容，例如 SQL 參考資料快照集。|
|GlobalStorage. AccountKey|字串|否|全域儲存體帳戶的對應索引鍵。|
|DataSourceCredentialDomain|字串|否|認證本機儲存體的保留屬性。|
|ScriptType|字串|是|保留的屬性，表示此來源檔案的類型。 可接受的值為 "Jobconfig.json" （適用于 Jobconfig.json）。|
|Tags|JSON 索引鍵/值組|否|標籤是成對的名稱/值，可讓您將相同的標籤套用至多個資源和資源群組，藉此將資源分類及查看合併計費。 標記名稱不區分大小寫，且標記值會區分大小寫。|

## <a name="next-steps"></a>後續步驟

* [在 Visual Studio Code 中建立 Azure 串流分析作業](quick-create-vs-code.md)
* [使用 Visual Studio Code 和範例資料在本機測試串流分析查詢](visual-studio-code-local-run.md)
* [使用 Visual Studio Code](visual-studio-code-local-run-live-input.md)
*[使用 CI/CD npm 套件部署 Azure 串流分析作業](setup-cicd-vs-code.md)，在本機針對即時串流輸入測試串流分析查詢