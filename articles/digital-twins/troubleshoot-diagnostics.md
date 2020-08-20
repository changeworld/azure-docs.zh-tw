---
title: 設定診斷
titleSuffix: Azure Digital Twins
description: 請參閱如何使用診斷設定來啟用記錄。
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 5091edbf9138cb8ff03df193dcbeed692aaf13e3
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612396"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>針對 Azure 數位 Twins 進行疑難排解：診斷記錄

Azure 數位 Twins 會收集服務實例的 [計量](troubleshoot-metrics.md) ，以提供您資源狀態的相關資訊。 您可以使用這些計量來評估 Azure 數位 Twins 服務及其連線資源的整體健康情況。 這些使用者對應的統計資料可協助您瞭解 Azure 數位 Twins 的狀況，並協助您對問題進行根本原因分析，而不需要聯繫 Azure 支援。

本文說明如何從 Azure 數位 Twins 實例開啟計量資料的 **診斷記錄** 。 您可以使用這些記錄來協助您針對服務問題進行疑難排解，並設定診斷設定，以將 Azure 數位 Twins 計量傳送至不同的目的地。 您可以在建立診斷設定中閱讀有關這些設定的詳細資訊， [*以將平臺記錄和計量傳送至不同的目的地*](../azure-monitor/platform/diagnostic-settings.md)。

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>使用 Azure 入口網站開啟診斷設定

以下說明如何啟用 Azure 數位 Twins 實例的診斷設定：

1. 登入 [Azure 入口網站](https://portal.azure.com) ，然後流覽至您的 Azure 數位 Twins 實例。 您可以在入口網站的搜尋列中輸入其名稱來尋找它。 

2. 從功能表選取 [ **診斷設定** ]，然後 **新增診斷設定**。

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="顯示診斷設定頁面和要新增之按鈕的螢幕擷取畫面":::

3. 在接下來的頁面上，填入下列值：
     * **診斷設定名稱**：提供診斷設定的名稱。
     * **類別細節**：選擇您想要監視的作業，並核取方塊以啟用這些作業的診斷。 診斷設定可以報告的作業為：
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        如需這些選項的詳細資訊，請參閱下面的 [*類別目錄詳細資料*](#category-details) 一節。
     * **目的地詳細資料**：選擇您要傳送記錄的位置。 您可以選取這三個選項的任意組合：
        - 傳送至 Log Analytics
        - 封存至儲存體帳戶
        - 串流至事件中樞

        如果您的目的地選取專案需要，系統可能會要求您填寫其他詳細資料。  
    
4. 儲存新設定。 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="顯示診斷設定頁面和要新增之按鈕的螢幕擷取畫面":::

新的設定大約會在 10 分鐘內生效。 之後，記錄會出現在您實例的 [ **診斷設定** ] 頁面上的已設定目標中。 

## <a name="category-details"></a>類別目錄詳細資料

以下是在設定診斷設定時，可以在 [ **類別目錄詳細資料** ] 底下選取之記錄類別的更多詳細資料。

| 記錄分類 | 描述 |
| --- | --- |
| ADTModelsOperation | 記錄與模型相關的所有 API 呼叫 |
| ADTQueryOperation | 記錄與查詢相關的所有 API 呼叫 |
| ADTEventRoutesOperation | 記錄與事件路由相關的所有 API 呼叫，以及將事件從 Azure 數位 Twins 輸出到端點服務，例如事件方格、事件中樞和服務匯流排 |
| ADTDigitalTwinsOperation | 記錄與 Azure 數位 Twins 相關的所有 API 呼叫 |

每個記錄類別都包含寫入、讀取、刪除和動作的作業。  這些會對應至 REST API 呼叫，如下所示：

| 事件類型 | REST API 作業 |
| --- | --- |
| 寫入 | PUT 和 PATCH |
| 讀取 | GET |
| 刪除 | 刪除 |
| 動作 | POST |

以下是每個類別中記錄的作業和對應的 [Azure 數位 Twins REST API 呼叫](https://docs.microsoft.com/rest/api/azure-digitaltwins/) 的完整清單。 

>[!NOTE]
> 每個記錄類別都包含數個作業/REST API 呼叫。 在下表中，每個記錄類別都會對應至其下的所有作業/REST API 呼叫，直到列出下一個記錄類別為止。 

| 記錄分類 | 作業 | REST API 呼叫和其他事件 |
| --- | --- | --- |
| ADTModelsOperation | DigitalTwins/模型/寫入 | 數位對應項模型更新 API |
|  | DigitalTwins/模型/讀取 | 數位對應項模型會依識別碼和清單 Api 取得 |
|  | DigitalTwins/模型/刪除 | 數位對應項模型刪除 API |
|  | DigitalTwins/模型/動作 | 數位對應項模型新增 API |
| ADTQueryOperation | DigitalTwins/query/action | 查詢 Twins API |
| ADTEventRoutesOperation | DigitalTwins/eventroutes/write | 事件路由新增 API |
|  | DigitalTwins/eventroutes/read | 事件路由會依識別碼和清單 Api 取得 |
|  | DigitalTwins/eventroutes/delete | 事件路由刪除 API |
|  | DigitalTwins/eventroutes/action | 嘗試將事件發佈至端點服務時失敗， (不是 API 呼叫)  |
| ADTDigitalTwinsOperation | DigitalTwins/DigitalTwins/write | 數位 Twins 新增、加入關聯性、更新、更新元件 |
|  | DigitalTwins/DigitalTwins/read | 數位 Twins 依識別碼取得、取得元件、依識別碼取得關聯性、列出連入關聯性、清單關聯性 |
|  | DigitalTwins/DigitalTwins/delete | 數位 Twins 刪除、刪除關聯性 |
|  | DigitalTwins/DigitalTwins/action | 數位 Twins 傳送元件遙測，傳送遙測 |

## <a name="next-steps"></a>後續步驟

* 如需有關設定診斷的詳細資訊，請參閱 [*收集和取用來自 Azure 資源的記錄資料*](../azure-monitor/platform/platform-logs-overview.md)。
* 如需 Azure 數位 Twins 計量的詳細資訊，請參閱 [*疑難排解：使用 Azure 監視器來查看計量*](troubleshoot-metrics.md)。
* 若要瞭解如何為您的計量啟用警示，請參閱 [*疑難排解：設定警示*](troubleshoot-alerts.md)。
