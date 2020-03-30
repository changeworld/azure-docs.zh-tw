---
title: Azure Application Insights | Microsoft Docs
description: 將應用程式見解的資料與其他資料集（如資料擴充或查閱資料表、非應用程式見解資料來源和自訂資料）相關聯。
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 121e4699bd6a72f6865d3a6ffdef58c1b3806047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082756"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>使自訂資料來源與 Application Insights 資料相互關聯

Application Insights 會收集數個不同的資料類型：例外狀況、追蹤、網頁檢視和其他項目。 雖然這通常足以調查應用程式的性能、可靠性和使用，但在某些情況下，將應用程式見解中存儲的資料與其他完全自訂資料集相關聯非常有用。

您需要自訂資料的情況可能包括：

- 資料擴充或查閱資料表：例如，在伺服器名稱中增添伺服器擁有者，以及能在其中找到該伺服器名稱的實驗室位置 
- 與非 Application Insights 資料來源相互關聯：例如，讓網路商店上的購買資料與您的購買履約服務相互關聯，以判斷寄送時間的預估準確度 
- 完全自訂的資料：我們有許多客戶喜愛 Azure 監視器記錄平台 (用來支援 Application Insights) 的查詢語言和效能，而且想要使用它來查詢與 Application Insights 完全不相關的資料。 例如，追蹤屬於智慧型家電安裝的太陽能面板效能，如[此處](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/)所述。

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>如何使自訂資料與 Application Insights 資料相互關聯 

由於 Application Insights 受到功能強大的 Azure 監視器記錄平台所支援，因此我們能夠使用 Azure 監視器的完整功能來內嵌資料。 然後，我們將使用"join"運算子編寫查詢，該運算子將此自訂資料與 Azure 監視器日誌中可用的資料相關聯。 

## <a name="ingesting-data"></a>內嵌資料

在本節中，我們將檢閱如何將您的資料放入 Azure 監視器記錄。

如果還沒有，請通過執行[這些說明](../learn/quick-collect-azurevm.md)（包括"創建工作區"步驟）來預配新的日誌分析工作區。

若要開始將記錄資料傳送至 Azure 監視器。 有幾種選項可用：

- 對於同步機制，您可以直接調用[資料收集器 API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)或使用邏輯應用連接器 - 只需查找"Azure 日誌分析"並選擇"發送資料"選項：

  ![選擇和動作的螢幕擷取畫面](./media/custom-data-correlation/01-logic-app-connector.png)  

- 若要使用非同步選項，請使用「資料收集器 API」來建置處理管線。 有關詳細資訊，請參閱[此文章](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api)。

## <a name="correlating-data"></a>使資料相互關聯

Application Insights 會以 Azure 監視器記錄平台作為基礎。 因此我們可以使用[跨資源聯結](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search)，讓內嵌至 Azure 監視器的所有資料與 Application Insights 資料相互關聯。

例如，我們可以在日誌分析工作區中稱為"myLA"的日誌分析工作區中，將實驗室清單和位置引入名為"LabLocations_CL"的表中。 如果然後我們想要查看在名為"myAI"的應用程式見解應用中跟蹤的請求，並將服務請求的電腦名稱稱關聯到前面提到的自訂表格中存儲的這些電腦的位置，我們可以從應用程式見解或 Azure 監視器上下文：

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>後續步驟

- 請參閱[資料收集器 API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) 參考。
- 取得[跨資源聯結](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search)的詳細資訊。
