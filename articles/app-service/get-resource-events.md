---
title: 取得 Azure App Service 中的資源事件
description: 瞭解如何透過 App Service 應用程式上的活動記錄和事件方格取得資源事件。
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: 1fd283f95823a67319dc467a3a1d6251193182da
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124732"
---
# <a name="get-resource-events-in-azure-app-service"></a>取得 Azure App Service 中的資源事件

Azure App Service 提供內建工具，可用來監視資源的狀態和健康情況。 資源事件可協助您瞭解對基礎 web 應用程式資源所做的任何變更，並視需要採取動作。 事件範例包括：調整實例、更新應用程式設定、重新開機 web 應用程式，以及其他更多專案。 在本文中，您將瞭解如何查看[Azure 活動記錄](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)，並啟用[事件方格](https://docs.microsoft.com/azure/event-grid/)來監視與您 App Service web 應用程式相關的資源事件。

> [!NOTE]
> 與 Event Grid App Service 整合目前為**預覽**狀態。 [如需詳細資訊，請參閱公告。](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>查看 Azure 活動記錄
Azure 活動記錄包含在您的訂用帳戶中的資源上執行的作業所發出的資源事件。 Azure 入口網站和 Azure Resource Manager 範本中的兩個使用者動作都會參與活動記錄所捕捉到的事件。 

Azure 活動記錄以取得 App Service 詳細資料，例如：
- 在資源上執行的作業（例如： App Service 方案）
- 啟動作業的人員
- 作業進行的時間
- 作業的狀態
- 可協助您研究作業的屬性值

### <a name="what-can-you-do-with-azure-activity-logs"></a>您可以使用 Azure 活動記錄做什麼？

您可以使用 Azure 入口網站、PowerShell、REST API 或 CLI 來查詢 Azure 活動記錄。 您可以將記錄傳送至儲存體帳戶、事件中樞和 Log Analytics。 您也可以在 Power BI 中分析它們，或建立警示以隨時掌握資源事件的更新。

[查看並取出 Azure 活動記錄事件。](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

## <a name="ship-activity-logs-to-event-grid"></a>將活動記錄傳送至事件方格

雖然活動記錄是以使用者為基礎，但有一個新的[Event Grid](https://docs.microsoft.com/azure/event-grid/)與 App Service （預覽）整合，會記錄使用者動作和自動化事件。 透過事件方格，您可以設定處理常式來回應所說的事件。 例如，使用 Event Grid 來立即觸發無伺服器功能，以在每次相片新增至 Blob 儲存體容器時執行影像分析。

或者，您可以使用 Event Grid 搭配 Logic Apps 隨處處理資料，而不需撰寫程式碼。 Event Grid 可連線資料來源與事件處理常式。 例如，使用 Event Grid 來立即觸發無伺服器功能，以在每次相片新增至 Blob 儲存體容器時執行影像分析。

### <a name="supported-event-types"></a>支援的事件類型
| 事件類型 |說明|
| -----------| ------------- |
| Microsoft.web/sites | Webapp |
| BackupOperationCompleted |已成功完成 webapp 備份|
| BackupOperationFailed | Webapp 備份失敗|
| RestoreOperationStarted |從備份還原已開始|
| RestoreOperationCompleted |已成功完成從備份還原|
| RestoreOperationFailed |從備份還原失敗|
| SlotSwapStarted |位置交換已啟動|
| SlotSwapCompleted |已成功完成位置交換|
| SlotSwapFailed |位置交換失敗|
| SlotSwapWithPreviewStarted |已開始使用預覽的位置交換|
| SlotSwapWithPreviewCancelled |具有預覽的位置交換失敗|
| AppUpdated | |
| 啟 | 已重新開機 webapp |
| 已停止 | Webapp 已停止 |
| ChangedAppSettings | Webapp 上的應用程式設定已變更 |
| - | - |
| Microsoft web/serverfarms | （App Service 方案） |
| AspUpdated | 已更新 app service 方案。 事件物件包含已變更屬性的詳細資料。 |
| 相應增加/減少 | App service 方案會相應增加或減少。 事件物件包含實例計數。|


## <a name="next-steps"></a><a name="nextsteps"></a> 後續步驟
* [使用 Azure 監視器查詢記錄](../azure-monitor/log-query/log-query-overview.md)
* [如何監視 Azure App Service](web-sites-monitor.md)
* [在 Visual Studio 中進行 Azure App Service 的疑難排解](troubleshoot-dotnet-visual-studio.md)
* [在 HDInsight 中分析應用程式記錄](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413) \(英文\)
