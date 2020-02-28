---
title: Azure 平臺記錄的總覽 |Microsoft Docs
description: 概述 Azure 監視器中的記錄，可提供有關 Azure 資源作業的豐富、經常性資料。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 89de6b3737c8a1e91832aba8f749078806b64e90
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77659315"
---
# <a name="overview-of-azure-platform-logs"></a>Azure 平臺記錄的總覽
平臺記錄會針對 Azure 資源和它們所依賴的 Azure 平臺，提供詳細的診斷和審核資訊。 雖然您需要將特定平臺記錄檔設定為要保留的一個或多個目的地，否則會自動產生它們。 本文提供平臺記錄的總覽，包括它們所提供的資訊，以及如何設定它們以進行收集和分析。

## <a name="types-of-platform-logs"></a>平臺記錄的類型
下表列出可在不同 Azure 層級使用的特定平臺記錄。

| Log | 層 | 描述 |
|:---|:---|:---|
| 資源記錄 | Azure 資源 | 提供在 Azure 資源（*資料平面*）內執行之作業的深入解析，例如從 Key Vault 取得秘密，或對資料庫提出要求。 資源記錄的內容會因 Azure 服務和資源類型而異。<br><br>*資源記錄先前稱為診斷記錄。*  |
| 活動記錄檔 | Azure 訂閱 | 除了服務健康狀態事件的更新之外，還可讓您深入瞭解訂用帳戶中的每個 Azure 資源上的作業（*管理平面*）。 使用活動記錄來_判斷對訂_用帳戶中的資源所採取的任何寫入作業（PUT、 _POST、DELETE_）的_內容_、物件和時機。 您也可以了解作業的狀態和其他相關屬性。  每個 Azure 訂用帳戶都有單一活動記錄。 |
| Azure Active Directory 記錄 | Azure 租用戶 |  包含登入活動的歷程記錄，以及在特定租使用者的 Azure Active Directory 中所做變更的審核線索。 如需 Azure Active Directory 記錄的完整描述，請參閱[什麼是 Azure Active Directory 報表？](../../active-directory/reports-monitoring/overview-reports.md) 。   |

> [!NOTE]
> Azure 活動記錄主要是針對 Azure Resource Manager 中發生的活動。 此記錄不會追蹤使用傳統/RDFE 模型的資源。 某些傳統資源類型在 Azure Resource Manager 中有 Proxy 資源提供者 (例如，Microsoft.ClassicCompute)。 如果您透過使用這些 Proxy 資源提供者的 Azure Resource Manager 來與傳統資源類型互動，則作業會顯示在活動記錄。 如果您在 Azure Resource Manager Proxy 之外與傳統資源類型互動，您的動作將只會記錄於「作業記錄」。 可在入口網站的個別區段中，瀏覽作業記錄。

![平台記錄概觀](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>查看平臺記錄
有不同的選項可讓您查看和分析不同的 Azure 平臺記錄。

- 在 Azure 入口網站中查看活動記錄，並從 PowerShell 和 CLI 存取事件。 如需詳細資訊，請參閱[View and 取出 Azure 活動記錄事件](activity-log-view.md)。 
- 在 Azure 入口網站中查看 Azure Active Directory 的安全性和活動報告。 請參閱[什麼是 Azure Active Directory 報表？](../../active-directory/reports-monitoring/overview-reports.md)  以取得詳細資料。
- 資源記錄會由支援的 Azure 資源自動產生，但除非您將它們傳送至[目的地](#destinations)，否則無法查看它們。 

## <a name="destinations"></a>Destinations
您可以根據您的監視需求，將平臺記錄傳送到下表中的一或多個目的地。 [建立診斷設定](diagnostic-settings.md)以設定平臺記錄的目的地。

| Destination | 狀況 | 參考 |
|:---|:---|:---|:---|
| Log Analytics 工作區 | 分析具有其他監視資料的記錄，並利用記錄查詢和警示等 Azure 監視器功能。 | [活動記錄和資源記錄](resource-logs-collect-workspace.md)<br>[Azure 活動目錄記錄](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure 儲存體 | 封存記錄以進行審核、靜態分析或備份。 |[活動記錄和資源記錄](archive-diagnostic-logs.md)<br>[Azure 活動目錄記錄](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| 事件中樞 | 將記錄串流至協力廠商記錄和遙測系統。  |[活動記錄和資源記錄](resource-logs-stream-event-hubs.md)<br>[Azure 活動目錄記錄](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>後續步驟

* [查看不同類別的活動記錄架構](activity-log-schema.md)
* [查看不同 Azure 服務的資源記錄架構](diagnostic-logs-schema.md)
