---
title: 瞭解布建如何與 Azure Active Directory 中的 Azure 監視器記錄整合。
description: 瞭解布建如何與 Azure Active Directory 中的 Azure 監視器記錄整合。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 4e14bd6365ce53d98d6e0b0d1f2601ff3b3e59b4
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997213"
---
# <a name="understand-how-provisioning-integrates-with-azure-monitor-logs"></a>瞭解布建如何與 Azure 監視器記錄整合

布建與 Azure 監視器記錄和記錄分析整合。 您可以使用 Azure 監視來進行一些作業，例如建立活頁簿，也稱為儀表板、儲存30天的布建記錄，以及建立自訂查詢和警示。 本文討論布建記錄如何與 Azure 監視器記錄整合。 若要深入瞭解布建記錄的一般運作方式，請參閱布建 [記錄](../reports-monitoring/concept-provisioning-logs.md)。

## <a name="enabling-provisioning-logs"></a>啟用布建記錄

您應該已熟悉 Azure 監視和 Log Analytics。 如果沒有，請跳至瞭解它們，然後再回來瞭解應用程式布建記錄。 若要深入瞭解 Azure 監視，請參閱 [Azure 監視器總覽](../../azure-monitor/overview.md)。 若要深入瞭解 Azure 監視器記錄和 Log Analytics 的詳細資訊，請參閱 [Azure 監視器中的記錄查詢總覽](../../azure-monitor/log-query/log-query-overview.md)。

在 Azure 監視上設定好之後，您可以啟用應用程式布建的記錄。 選項位於 [ **診斷設定** ] 頁面上。

:::image type="content" source="media/application-provisioning-log-analytics/diagnostic-settings.png" alt-text="存取診斷設定" lightbox="media/application-provisioning-log-analytics/diagnostic-settings.png":::

:::image type="content" source="media/application-provisioning-log-analytics/enable-log-analytics.png" alt-text="存取診斷設定" lightbox="media/application-provisioning-log-analytics/enable-log-analytics.png":::

> [!NOTE]
> 如果您剛剛布建工作區，可能需要一些時間，才能將記錄傳送給它。 如果您收到錯誤，指出訂用帳戶未註冊為使用 *microsoft* ，請在幾分鐘後回來查看。
 
## <a name="understanding-the-data"></a>了解資料
布建記錄檢視器的基礎資料流程幾乎完全相同。 Azure 監視器記錄會取得與 Azure 入口網站 UI 和 Azure API 幾乎相同的資料流程。 記錄欄位只有一些 **差異** ，如下表所述。 若要深入瞭解這些欄位，請參閱 [清單 provisioningObjectSummary](https://docs.microsoft.com/graph/api/provisioningobjectsummary-list?view=graph-rest-beta&tabs=http&preserve-view=true)。

|Azure 監視器記錄   |Azure 入口網站 UI   |Azure API |
|----------|-----------|------------|
|errorDescription |reason |resultDescription |
|status |resultType |resultType |
|activityDateTime |TimeGenerated |TimeGenerated |


## <a name="azure-monitor-workbooks"></a>Azure 監視器活頁簿

Azure 監視器活頁簿為數據分析提供了彈性的畫布。 也可讓您在 Azure 入口網站中建立豐富的視覺效果報表。 若要深入瞭解，請參閱 [Azure 監視器活頁簿總覽](../../azure-monitor/platform/workbooks-overview.md)。

應用程式布建隨附一組預先建立的活頁簿。 您可以在 [活頁簿] 頁面上找到它們。 若要查看資料，您必須確定已填入 (timeRange、jobID、appName) 的所有篩選準則。 您也必須確定您已布建應用程式，否則記錄中不會有任何資料。

:::image type="content" source="media/application-provisioning-log-analytics/workbooks.png" alt-text="存取診斷設定" lightbox="media/application-provisioning-log-analytics/workbooks.png":::

:::image type="content" source="media/application-provisioning-log-analytics/report.png" alt-text="存取診斷設定" lightbox="media/application-provisioning-log-analytics/report.png":::

## <a name="custom-queries"></a>自訂查詢

您可以建立自訂查詢，並在 Azure 儀表板上顯示資料。 若要深入瞭解，請參閱 [建立和共用 Log Analytics 資料的儀表板](../../azure-monitor/log-query/get-started-queries.md)。 此外，請務必查看 [Azure 監視器中的記錄查詢總覽](../../azure-monitor/log-query/log-query-overview.md)。

以下是一些範例，可讓您開始進行應用程式布建。

根據來源系統中的識別碼，查詢使用者 a 的記錄：
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| where tostring(SourceIdentity.Id) == "49a4974bb-5011-415d-b9b8-78caa7024f9a"
```

摘要每個 ErrorCode 的計數：
```kusto
AADProvisioningLogs
| summarize count() by ErrorCode = ResultSignature
```

依動作摘要每日事件的計數：
```kusto
AADProvisioningLogs
| where TimeGenerated > ago(7d)
| summarize count() by Action, bin(TimeGenerated, 1d)
```

取得100事件和專案索引鍵屬性：
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| extend TargetIdentity = parse_json(TargetIdentity)
| extend ServicePrincipal = parse_json(ServicePrincipal)
| where tostring(SourceIdentity.identityType) == "Group"
| project tostring(ServicePrincipal.Id), tostring(ServicePrincipal.Name), ModifiedProperties, JobId, Id, CycleId, ChangeId, Action, SourceIdentity.identityType, SourceIdentity.details, TargetIdentity.identityType, TargetIdentity.details, ProvisioningSteps
|take 100
```

## <a name="custom-alerts"></a>自訂警示

Azure 監視器可讓您設定自訂警示，讓您可以收到與布建相關的重要事件通知。 例如，您可能會想要收到失敗尖峰的警示。 或可能會導致停用或刪除的尖峰。 您可能會想要收到警示的另一個範例是缺少任何布建，這表示有問題。

若要深入瞭解警示，請參閱 [使用 Azure 監視器警示來回應事件](../../azure-monitor/learn/tutorial-response.md)。

當失敗的尖峰時發出警示。 將 jobID 取代為應用程式的 jobID。

:::image type="content" source="media/application-provisioning-log-analytics/alert1.png" alt-text="存取診斷設定" lightbox="media/application-provisioning-log-analytics/alert1.png":::

可能有問題導致布建服務停止執行。 使用下列警示來偵測指定時間間隔內沒有任何布建事件的時間。

:::image type="content" source="media/application-provisioning-log-analytics/alert2.png" alt-text="存取診斷設定" lightbox="media/application-provisioning-log-analytics/alert2.png":::

當停用或刪除出現尖峰時發出警示。

:::image type="content" source="media/application-provisioning-log-analytics/alert3.png" alt-text="存取診斷設定" lightbox="media/application-provisioning-log-analytics/alert3.png":::


## <a name="community-contributions"></a>社群投稿

我們會針對應用程式布建查詢和儀表板，採用開放原始碼和以社區為基礎的方法。 如果您已建立您認為其他人會覺得有用的查詢、警示或活頁簿，請務必將它發佈至 [AzureMonitorCommunity GitHub](https://github.com/microsoft/AzureMonitorCommunity)存放庫。 然後讓我們以連結傳送電子郵件。 我們會檢查並將其發佈至服務，讓其他人也能受益。 您可以透過 provisioningfeedback@microsoft.com 來與我們連絡。

## <a name="next-steps"></a>接下來的步驟

- [Log analytics](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)
- [開始使用 Azure 監視器記錄中的查詢](../../azure-monitor/log-query/get-started-queries.md)
- [在 Azure 入口網站中建立和管理警示群組](../../azure-monitor/platform/action-groups.md)
- [安裝與使用適用於 Azure Active Directory 的記錄分析檢視](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [布建記錄 API](https://docs.microsoft.com/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta.md&preserve-view=true)
