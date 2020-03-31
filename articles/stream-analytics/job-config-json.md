---
title: Azure 流分析作業分析.json 欄位
description: 本文列出了 Azure 流分析作業 Config.json 檔的支援欄位，用於在視覺化工作室代碼中創建作業。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617953"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure 流分析作業分析.json 欄位

用於[使用 Visual Studio 代碼創建 Azure 流分析作業](quick-create-vs-code.md)的*JobConfig.json*檔中支援以下欄位。

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
|資料當地語系化|字串|否|流分析作業的資料地區設定。 值應為受支援的名稱。 如果未指定，則預設為"en-US"。|
|輸出錯誤策略|字串|否|指示要應用於到達輸出的事件的策略，並且由於格式錯誤（缺少列值、類型或大小錯誤的列值）無法寫入外部存儲的事件。 - 停止或掉落|
|事件延遲到達最大延遲秒|integer|否|可以包括事件延遲的最大可容忍延遲（以秒為單位）。 支援的範圍為 -1 到 1814399 （20.23：59：59 天）和 -1 用於無限期指定等待。 如果屬性不存在，則將其解釋為值為 -1。|
|事件出序最大延遲秒|integer|否|可調整訂單外事件以按順序返回的最大可容忍延遲（以秒為單位）。|
|事件訂單外策略|字串|否|指示要應用於輸入事件流中順序順序耗盡的事件的策略。 - 調整或掉落|
|流式處理單元|integer|是|指定流式處理作業使用的流式處理單元數。|
|CompatibilityLevel|字串|否|控制流式處理作業的某些運行時行為。 - 可接受的值為"1.0"、"1.1"、"1.2"|
|使用系統分配標識|boolean|否|設置為 true 以啟用此作業使用託管 Azure 活動目錄標識與其他 Azure 服務本身進行通信。|
|全域存儲.帳戶名稱|字串|否|全域存儲帳戶用於存儲與流分析作業相關的內容，如 SQL 參考資料快照。|
|全域存儲.帳戶金鑰|字串|否|全域存儲帳戶的對應金鑰。|
|資料來源憑據域|字串|否|用於憑據本機存放區的保留屬性。|
|腳本類型|字串|是|保留屬性以指示此原始檔案的類型。 可接受值為 JobConfig.json 的"JobConfig"。。|
|Tags|JSON 鍵值對|否|標記是名稱/值對，使您能夠通過將同一標記應用於多個資源和資源組來對資源進行分類並查看合併計費。 標記名稱不區分大小寫，標記值區分大小寫。|

## <a name="next-steps"></a>後續步驟

* [在視覺化工作室代碼中創建 Azure 流分析作業](quick-create-vs-code.md)
* [使用視覺化工作室代碼使用示例資料在本地測試流分析查詢](visual-studio-code-local-run.md)
* [通過使用視覺化工作室代碼](visual-studio-code-local-run-live-input.md)
*[使用 CI/CD npm 包部署 Azure 流分析作業](setup-cicd-vs-code.md)，在本地測試流分析查詢與即時流輸入