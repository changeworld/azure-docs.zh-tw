---
title: 欄位上的 Azure 串流分析 JobConfig.js
description: 本文列出 Azure 串流分析 JobConfig.js在 Visual Studio Code 用來建立作業之檔案的支援欄位。
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 0eebd0b62e973572a40b7b141ae908046700ba3c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020480"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>欄位上的 Azure 串流分析 JobConfig.js

在用來 [建立使用 Visual Studio Code 的 Azure 串流分析作業](quick-create-visual-studio-code.md)的 *JobConfig.js* 檔案中，支援下欄欄位。

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
|DataLocale|字串|No|串流分析作業的資料地區設定。 值應該是支援的名稱。 如果未指定，則預設為 ' en-us '。|
|OutputErrorPolicy|字串|No|指出要套用至抵達輸出之事件的原則，而且由於 (遺漏資料行值、錯誤類型或大小) 的資料行值的格式錯誤，因此無法寫入外部儲存體。 -Stop 或 Drop|
|EventsLateArrivalMaxDelayInSeconds|integer|否|可以包含延遲延遲的最大可容忍延遲（以秒為單位）。 支援的範圍為-1 至 1814399 (20.23：59：59天) ，而-1 用來指定無限期等候。 如果屬性不存在，則會將其解釋為值-1。|
|EventsOutOfOrderMaxDelayInSeconds|integer|否|可依序調整順序外事件的最大可容忍延遲（以秒為單位）。|
|EventsOutOfOrderPolicy|字串|No|表示要套用至輸入事件資料流程中依順序抵達之事件的原則。 -調整或捨棄|
|StreamingUnits|integer|是|指定串流作業所使用的串流單位數目。|
|CompatibilityLevel|字串|No|控制串流作業的特定執行時間行為。 -可接受的值為 "1.0"、"1.1"、"1.2"|
|UseSystemAssignedIdentity|boolean|否|設定 true 可讓此工作使用受控 Azure Active Directory 身分識別，與其他 Azure 服務通訊。|
|GlobalStorage AccountName|字串|No|全域儲存體帳戶是用來儲存與您的串流分析作業相關的內容，例如 SQL 參考資料快照集。|
|GlobalStorage. AccountKey|字串|No|全域儲存體帳戶的對應索引鍵。|
|DataSourceCredentialDomain|字串|No|認證本機儲存體的保留屬性。|
|ScriptType|字串|是|保留的屬性，表示此來源檔案的類型。 JobConfig.js的可接受值為 ">jobconfig.json"。|
|標籤|JSON 索引鍵/值組|否|標籤為成對的名稱和值，可讓您將相同的標籤套用至多個資源與資源群組，以便為資源分類及檢視合併的帳單。 標記名稱不區分大小寫，而且標記值會區分大小寫。|

## <a name="next-steps"></a>後續步驟

* [在 Visual Studio Code 中建立 Azure 串流分析作業](quick-create-visual-studio-code.md)
* [使用 Visual Studio Code 和範例資料在本機測試串流分析查詢](visual-studio-code-local-run.md)
* [使用 Visual Studio Code 在本機針對即時資料流輸入測試串流分析查詢](visual-studio-code-local-run-live-input.md) 
*[使用 CI/CD npm 套件部署 Azure 串流分析作業](./cicd-overview.md)