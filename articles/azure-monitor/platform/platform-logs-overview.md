---
title: Azure 平臺日誌概述 |微軟文檔
description: Azure 監視器中日誌概述，這些日誌提供有關 Azure 資源操作的豐富、頻繁的資料。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 89de6b3737c8a1e91832aba8f749078806b64e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659315"
---
# <a name="overview-of-azure-platform-logs"></a>Azure 平台記錄概觀
平臺日誌為 Azure 資源和它們所依賴的 Azure 平臺提供詳細的診斷和審核資訊。 它們會自動生成，儘管您需要配置某些平臺日誌以轉發到一個或多個要保留的目標。 本文概述了平臺日誌，包括它們提供哪些資訊以及如何配置它們以進行收集和分析。

## <a name="types-of-platform-logs"></a>平臺日誌的類型
下表列出了 Azure 不同層可用的特定平臺日誌。

| Log | 階層 | 描述 |
|:---|:---|:---|
| 資源記錄 | Azure 資源 | 提供對 Azure 資源（*資料平面*）內執行的操作的見解，例如從金鑰保存庫獲取機密或向資料庫發出請求。 資源日誌的內容因 Azure 服務和資源類型而異。<br><br>*資源日誌以前稱為診斷日誌。*  |
| 活動記錄檔 | Azure 訂閱 | 除了服務運行狀況事件的更新外，還提供從外部（*管理平面*）對訂閱中每個 Azure 資源的操作的深入瞭解。 使用活動日誌，確定對訂閱中_what_的資源執行的任何寫入操作（PUT、POST、DELETE）的_編寫操作_（PUT、POST、DELETE）的哪些 、_以及何時_執行。 您也可以了解作業的狀態和其他相關屬性。  每個 Azure 訂閱都有一個活動日誌。 |
| Azure 活動目錄日誌 | Azure 租用戶 |  包含在特定租戶的 Azure 活動目錄中所做的更改的登錄活動和審核跟蹤的歷史記錄。 有關 Azure 活動目錄日誌的完整說明，請參閱[什麼是 Azure 活動目錄報告？](../../active-directory/reports-monitoring/overview-reports.md)   |

> [!NOTE]
> Azure 活動記錄主要是針對 Azure Resource Manager 中發生的活動。 此記錄不會追蹤使用傳統/RDFE 模型的資源。 某些傳統資源類型在 Azure Resource Manager 中有 Proxy 資源提供者 (例如，Microsoft.ClassicCompute)。 如果您透過使用這些 Proxy 資源提供者的 Azure Resource Manager 來與傳統資源類型互動，則作業會顯示在活動記錄。 如果您在 Azure Resource Manager Proxy 之外與傳統資源類型互動，您的動作將只會記錄於「作業記錄」。 可在入口網站的個別區段中，瀏覽作業記錄。

![平台記錄概觀](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>查看平臺日誌
查看和分析不同的 Azure 平臺日誌有不同的選項。

- 在 Azure 門戶中查看活動日誌，並從 PowerShell 和 CLI 訪問事件。 有關詳細資訊[，請參閱查看和檢索 Azure 活動日誌事件](activity-log-view.md)。 
- 在 Azure 門戶中查看 Azure 活動目錄安全和活動報告。 請參閱[什麼是 Azure 活動目錄報告？](../../active-directory/reports-monitoring/overview-reports.md)  。
- 資源日誌由受支援的 Azure 資源自動生成，但除非將它們發送到[目標](#destinations)，否則無法查看這些資源日誌。 

## <a name="destinations"></a>Destinations
您可以根據監視要求將平臺日誌發送到下表中的一個或多個目標。 通過[創建診斷設置配置](diagnostic-settings.md)平臺日誌的目標。

| Destination | 狀況 | 參考 |
|:---|:---|:---|:---|
| Log Analytics 工作區 | 使用其他監視資料分析日誌，並利用 Azure 監視器功能（如日誌查詢和警報）。 | [活動日誌和資源日誌](resource-logs-collect-workspace.md)<br>[Azure 活動目錄日誌](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure 儲存體 | 存檔日誌以進行審核、靜態分析或備份。 |[活動日誌和資源日誌](archive-diagnostic-logs.md)<br>[Azure 活動目錄日誌](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| 事件中樞 | 將日誌資料流到協力廠商日誌記錄和遙測系統。  |[活動日誌和資源日誌](resource-logs-stream-event-hubs.md)<br>[Azure 活動目錄日誌](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>後續步驟

* [查看不同類別的活動日誌架構](activity-log-schema.md)
* [查看不同 Azure 服務的資源日誌架構](diagnostic-logs-schema.md)
