---
title: 診斷記錄
titleSuffix: Azure Cognitive Services
description: 本指南提供分步說明，用於為 Azure 認知服務啟用診斷日誌記錄。 這些日誌提供有關用於問題識別和調試的資源操作的豐富、頻繁的資料。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 539a35f170b2ee0c94762a30ed9376ca4a416210
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "71827907"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>為 Azure 認知服務啟用診斷日誌記錄

本指南提供分步說明，用於為 Azure 認知服務啟用診斷日誌記錄。 這些日誌提供有關用於問題識別和調試的資源操作的豐富、頻繁的資料。 在繼續之前，必須具有至少訂閱一個認知服務的 Azure 帳戶，例如[必應 Web 搜索](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview)、[語音服務](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)或[LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)。

## <a name="prerequisites"></a>Prerequisites

要啟用診斷日誌記錄，您需要在某處存儲日誌資料。 本教程使用 Azure 存儲和日誌分析。

* [Azure 存儲](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs)- 保留用於策略審核、靜態分析或備份的診斷日誌。 儲存體帳戶不一定要和資源發出記錄屬於相同的訂用帳戶，只要使用者有適當的設定可 RBAC 存取這兩個訂用帳戶即可。
* [日誌分析](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics)- 一種靈活的日誌搜索和分析工具，可用於分析 Azure 資源生成的原始日誌。

> [!NOTE]
> 其他配置選項可用。 要瞭解更多資訊，請參閱[從 Azure 資源收集和使用日誌資料](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)。

## <a name="enable-diagnostic-log-collection"></a>啟用診斷日誌收集  

讓我們首先使用 Azure 門戶啟用診斷日誌記錄。

> [!NOTE]
> 要使用 PowerShell 或 Azure CLI 啟用此功能，請使用["從 Azure 資源收集和使用日誌資料](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)"中提供的說明。

1. 瀏覽至 Azure 入口網站。 然後查找並選擇認知服務資源。 例如，訂閱必應 Web 搜索。   
2. 接下來，從左側導航功能表中，找到 **"監視"** 並選擇 **"診斷設置**"。 此螢幕包含以前為此資源創建的所有診斷設置。
3. 如果以前創建的資源要使用，則可以立即選擇它。 否則，選擇 **= 添加診斷設置**。
4. 輸入設置的名稱。 然後選擇 **"存檔到存儲帳戶****"併發送到日誌分析**。
5. 當提示您配置時，選擇要用於存儲診斷日誌的存儲帳戶和 OMS 工作區。 **注意**：如果您沒有存儲帳戶或 OMS 工作區，請按照提示創建一個。
6. 選擇**審核**、**請求回應****和所有指標**。 然後設置診斷日誌資料的保留期。 如果保留原則設置為零，則該日誌類別的事件將無限期存儲。
7. 按一下 [儲存]****。

最多可能需要兩個小時才能對日誌記錄資料進行查詢和分析。 所以，如果你沒有看到任何馬上看到任何東西，不要擔心。

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>從 Azure 存儲查看和匯出診斷資料

Azure 存儲是一個健壯的物件存儲解決方案，用於存儲大量非結構化資料。 在本節中，您將學習查詢存儲帳戶在 30 天時間範圍內的總事務，並將資料匯出到 Excel。

1. 從 Azure 門戶查找在上一節中創建的 Azure 存儲資源。
2. 從左側導航功能表中，找到 **"監視"** 並選擇 **"指標**"。
3. 使用可用的下拉清單配置查詢。 在此示例中，我們將時間範圍設置為 **"過去 30 天**"，將指標設置為 **"事務**"。
4. 查詢完成後，您將看到過去 30 天的事務視覺化。 要匯出此資料，請使用位於頁面頂部的 **"匯出到 Excel"** 按鈕。

詳細瞭解在[Azure 存儲](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)中可以執行哪些診斷資料。

## <a name="view-logs-in-log-analytics"></a>檢視 Log Analytics 中的記錄

按照這些說明瞭解資源的日誌分析資料。

1. 從 Azure 門戶中查找並選擇左側導航功能表中的**日誌分析**。
2. 查找並選擇啟用診斷時創建的資源。
3. 在 **"常規"** 下，查找並選擇 **"日誌**"。 在此頁面中，您可以針對日誌執行查詢。

### <a name="sample-queries"></a>範例查詢

下面是一些可用於流覽日誌資料的基本 Kusto 查詢。

在指定時間段內對 Azure 認知服務中的所有診斷日誌運行此查詢：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

運行此查詢以查看 10 個最新日誌：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

運行此查詢以按**資源**對操作進行分組：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
運行此查詢以查找執行操作的平均時間：

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

運行此查詢以查看隨時間劃分的操作量，按操作名稱拆分，每 10 秒對計數進行裝箱。

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>後續步驟

* 要瞭解如何啟用日誌記錄以及各種 Azure 服務支援的指標和日誌類別，請閱讀 Microsoft Azure 中的[指標概述](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)和[Azure 診斷日誌文章概述](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)。
* 閱讀下列文章來了解事件中樞：
  * [Azure 事件中樞是什麼？](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [開始使用事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* 閱讀[從 Azure 儲存體下載計量和診斷記錄](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs)。
* 讀取[Azure 監視器日誌中的瞭解日誌搜索](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new)。
