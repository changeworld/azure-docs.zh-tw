---
title: 取得 Azure App Service 中的資源事件
description: 了解如何透過 App Service 應用程式上的活動記錄和事件方格取得資源事件。
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: 7075e3eacc85198e22a9aa0e53f67a22416b2678
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649064"
---
# <a name="get-resource-events-in-azure-app-service"></a>取得 Azure App Service 中的資源事件

Azure App Service 提供內建工具，可用來監視資源的狀態和健康情況。 資源事件可協助您了解對基礎 Web 應用程式資源所做的任何變更，並視需要採取動作。 事件範例包括：調整執行個體、更新應用程式設定、重新啟動 Web 應用程式，以及其他更多項目。 在本文中，您將了解如何檢視 [Azure 活動記錄](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)，並啟用[事件方格](https://docs.microsoft.com/azure/event-grid/)來監視與您 App Service Web 應用程式相關的資源事件。

> [!NOTE]
> App Service 與事件方格的整合目前處於**預覽**狀態。 [如需詳細資料，請檢視公告。](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>檢視 Azure 活動記錄
Azure 活動記錄包含在您訂用帳戶中的資源上執行之作業所發出的資源事件。 Azure 入口網站和 Azure Resource Manager 範本中的兩個使用者動作都會參與活動記錄所擷取到的事件。 

App Service 詳細資料的 Azure 活動記錄，例如：
- 在資源上進行了哪些作業 (例如：App Service 方案)
- 啟動作業的人員
- 作業進行的時間
- 作業的狀態
- 可協助您研究作業的屬性值

### <a name="what-can-you-do-with-azure-activity-logs"></a>Azure 活動記錄有何功用？

您可以使用 Azure 入口網站、PowerShell、REST API 或 CLI 來查詢 Azure 活動記錄。 您可以將記錄傳送至儲存體帳戶、事件中樞和記錄分析。 您也可以在 Power BI 中加以分析，或建立警示以隨時掌握資源事件的更新。

[檢視及擷取 Azure 活動記錄事件。](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

## <a name="ship-activity-logs-to-event-grid"></a>將活動記錄傳送至事件方格

雖然活動記錄是以使用者為基礎，但有一個新的[事件方格](https://docs.microsoft.com/azure/event-grid/)與 App Service (預覽) 整合，以記錄使用者動作和自動化事件。 透過事件方格，您可以設定處理常式來回應所述之事件。 例如，使用 Event Grid 來立即觸發無伺服器功能，以在每次相片新增至 Blob 儲存體容器時執行影像分析。

或者，您可以使用 Event Grid 搭配 Logic Apps 隨處處理資料，而不需撰寫程式碼。 Event Grid 可連線資料來源與事件處理常式。 例如，使用 Event Grid 來立即觸發無伺服器功能，以在每次相片新增至 Blob 儲存體容器時執行影像分析。

[檢視 Azure App Service 事件的屬性和結構描述。](https://docs.microsoft.com/azure/event-grid/event-schema-app-service)

## <a name="next-steps"></a><a name="nextsteps"></a> 後續步驟
* [使用 Azure 監視器來查詢記錄](../azure-monitor/log-query/log-query-overview.md)
* [如何監視 Azure App Service](web-sites-monitor.md)
* [在 Visual Studio 中進行 Azure App Service 的疑難排解](troubleshoot-dotnet-visual-studio.md)
* [在 HDInsight 中分析應用程式記錄](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413) \(英文\)
