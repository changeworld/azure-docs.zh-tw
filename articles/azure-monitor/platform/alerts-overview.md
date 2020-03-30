---
title: Azure 中的警示與通知監視概觀
description: Azure 中的警示概觀。 警報、經典警報和警報介面。
ms.subservice: alerts
ms.topic: conceptual
ms.date: 01/28/2018
ms.openlocfilehash: 7ca77531ed3e1fae8ec297e430597452c7512aea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274784"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Microsoft Azure 中的警示概觀 

本文章說明何謂警示、其優點，以及如何開始使用它們。  

## <a name="what-are-alerts-in-microsoft-azure"></a>Microsoft Azure 中的警示是什麼？
當您的監視資料中發現重要條件時，警示會主動通知您。 它們可讓您在系統使用者注意到問題之前，找出並解決問題。 

本文討論 Azure 監視器中的統一警報體驗，其中包括以前由日誌分析和應用程式見解管理的警報。 [先前的警示體驗](alerts-classic.overview.md)和警示類型稱為*傳統警示*。 您可以通過選擇"在警報頁頂部**查看經典警報"** 來查看此較舊的體驗和較舊的警報類型。 

## <a name="overview"></a>總覽

下圖代表警示流程。 

![警報流圖](media/alerts-overview/Azure-Monitor-Alerts.svg)

警報規則與警報和警報觸發時執行的操作分開。 警報規則捕獲警報的目標和條件。 警示規則可處於已啟用或已停用狀態。 警示只有在啟用時才會引發。 

以下是警報規則的關鍵屬性：

**目標資源**：定義可用於警報的範圍和信號。 目標可以是任何 Azure 資源。 範例目標：虛擬機器、儲存體帳戶、虛擬機器擴展集、Log Analytics 工作區或 Application Insights 資源。 對於某些資源（如虛擬機器），可以指定多個資源作為警報規則的目標。

**信號**：由目標資源發出。 信號可以是以下類型：指標、活動日誌、應用程式見解和日誌。

**條件**：應用於目標資源的信號和邏輯的組合。 範例： 

- 百分比 CPU > 70%
- 伺服器回應時間 > 4 ms 
- 記錄查詢的結果計數 > 100

**警示名稱**：使用者配置的警報規則的特定名稱。

**警報說明**：由使用者配置的警報規則的說明。

**嚴重性**：滿足警報規則中指定的條件後警報的嚴重性。 嚴重性的範圍可從 0 到 4。

- Sev 0 = 嚴重
- 塞夫 1 = 錯誤
- 塞夫 2 = 警告
- Sev 3 = 資訊
- 塞夫 4 = 詳細 

**操作**：觸發警報時執行的特定操作。 有關詳細資訊，請參閱[操作組](../../azure-monitor/platform/action-groups.md)。

## <a name="what-you-can-alert-on"></a>您可以發出警示的對象

您可以對指標和日誌發出警報，如[監視資料來源](../../azure-monitor/platform/data-sources.md)中所述。 包含但不限於：

- 計量值
- 記錄搜尋查詢
- 活動記錄事件
- 基礎 Azure 平台健康情況
- 網站可用性測試

之前，「Azure 監視器」計量、Application Insights、Log Analytics 及「服務健康狀態」具有個別的警示功能。 隨著時間進展，Azure 已改善並結合使用者介面與不同的警示方法。 這樣的整併仍在持續進行中。 因此，新的警示系統中仍可能沒有某些警示功能。  

| **監視器源** | **信號類型**  | **描述** |
|-------------|----------------|-------------|
| 服務健康情況 | 活動記錄檔  | 不支援。 請參閱[建立服務通知的活動記錄警示](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)。  |
| Application Insights | Web 可用性測試 | 不支援。 請參閱 [Web 測試警示](../../azure-monitor/app/monitor-web-app-availability.md)。 可供任何經檢測可傳送資料給 Application Insights 的網站使用。 當網站的可用性或回應能力低於預期時收到通知。 |

## <a name="manage-alerts"></a>管理警示
您可以設定警示的狀態來指定警示在解決流程中的位置。 當滿足警報規則中指定的條件時，將創建或觸發警報，並且其狀態為*New*。 當您認可警示並將它關閉時，您可以變更狀態。 任何狀態變更都會儲存在警示的記錄中。

支援下列警示狀態：

| State | 描述 |
|:---|:---|
| 新增 | 此問題剛剛檢測到，尚未審查。 |
| 已認可 | 系統管理員已檢閱警示，且已開始處理。 |
| 關閉 | 已解決問題。 關閉警示之後，您可以將警示變更為另一個狀態以重新開啟它。 |

*警示狀態*與*監視條件*不同且無關。 警示狀態是由使用者所設定的。 監視條檢是由系統所設定的。 當引發警示時，警示的監視條件會設定為「已引發」**。 當導致引發警示的根本條件清除時，監視條件就會設定為「已解決」**。 警示狀態需等到使用者變更它之後才會變更。 了解[如何變更警示與智慧群組的狀態](https://aka.ms/managing-alert-smart-group-states)。

## <a name="smart-groups"></a>智慧群組 

智慧組是基於機器學習演算法的警報聚合，可説明降低警報噪音並説明疑難排解。 [深入了解智慧群組](https://aka.ms/smart-groups)和[如何管理智慧群組](https://aka.ms/managing-smart-groups)。


## <a name="alerts-experience"></a>警示體驗 
預設警報頁提供在特定時間範圍內創建的警報的摘要。 它顯示每個嚴重性的總警報，列用於標識每個嚴重性狀態中的警報總數。 選取任何嚴重性以開啟依照該嚴重性篩選的 [所有警示](#all-alerts-page) 頁面。

或者，您可以使用[REST API 以程式設計方式枚舉訂閱上生成的警報實例](#manage-your-alert-instances-programmatically)。

> [!NOTE]
   >  您只能訪問過去 30 天內生成的警報。

它不顯示或跟蹤經典警報。 您可以變更訂用帳戶或篩選參數來更新頁面。 

![警報頁面的螢幕截圖](media/alerts-overview/alerts-page.png)

您可以通過在頁面頂部的下拉式功能表中選擇值來篩選此視圖。

| 資料行 | 描述 |
|:---|:---|
| 訂用帳戶 | 選擇要為其查看警報的 Azure 訂閱。 您可以選擇選擇所有訂閱。 視圖中僅包含您有權訪問的選定訂閱中的警報。 |
| 資源群組 | 選取單一資源群組。 檢視僅會包含所選資源群組中具有目標的警示。 |
| 時間範圍 | 視圖中僅包含在選定時間範圍內觸發的警報。 支援的值為過去 1 小時、過去 24 小時、過去 7 天和過去 30 天。 |

在"警報"頁頂部選擇以下值以打開另一頁：

| 值 | 描述 |
|:---|:---|
| 警示總計 | 符合所選準則的警示總數。 選取此值以開啟沒有任何篩選的 [所有警示] 檢視。 |
| 智慧群組 | 從符合所選準則之警示建立的智慧群組總數。 選取此值將開啟 [所有警示] 檢視中的智慧群組清單。
| 警示規則總計 | 所選訂用帳戶和資源群組中的警示規則總數。 選取此值以開啟在選取的訂用帳戶與資源群組上篩選的 [規則] 檢視。


## <a name="manage-alert-rules"></a>管理警示規則
要顯示 **"規則"** 頁，請選擇"**管理警報規則**"。 "規則"頁是管理 Azure 訂閱中所有警報規則的單個位置。 它會列出所有警示規則，並可根據目標資源、資源群組、規則名稱或狀態來排序。 您還可以編輯、啟用或禁用此頁面的警報規則。  

 ![規則頁面的螢幕截圖](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>建立警示規則
無論監視服務或信號類型如何，您都可以以一致的方式編寫警報。 所有引發的警示和相關的詳細資料都在單一頁面中提供。
 
以下是創建新警報規則的方式：
1. 挑選警示的_目標_。
1. 從目標的可用訊號中選取_訊號_。
1. 從訊號指定套用於資料的_邏輯_。
 
這個簡化的編寫程序讓您不再需要先知道監視來源或支援的訊號，就能選取 Azure 資源。 可用訊號的清單會自動根據您選取的目標資源進行篩選。 此外，也會根據該目標，自動逐步引導您定義警示規則的邏輯。  

您可以參閱[使用 Azure 監視器來建立、檢視及管理警示](../../azure-monitor/platform/alerts-metric.md)，深入了解如何建立警示規則。

警示可跨數個 Azure 監視服務使用。 如需如何和何時使用每個服務的資訊，請參閱[監視 Azure 應用程式和資源](../../azure-monitor/overview.md)。 


## <a name="all-alerts-page"></a>[所有警示] 頁面 
要查看"**所有警報"** 頁，請選擇 **"總警報**"。 在這裡，您可以查看在選定時間內創建的警報清單。 您可以檢視個別警示的清單，或包含警示的智慧群組清單。 選取頁面頂端的橫幅以切換檢視。

![所有警報頁面的螢幕截圖](media/alerts-overview/all-alerts-page.png)

您可以通過在頁面頂部的下拉式功能表中選擇以下值來篩選視圖：

| 資料行 | 描述 |
|:---|:---|
| 訂用帳戶 | 選擇要為其查看警報的 Azure 訂閱。 您可以選擇選擇所有訂閱。 視圖中僅包含您有權訪問的選定訂閱中的警報。 |
| 資源群組 | 選取單一資源群組。 檢視僅會包含所選資源群組中具有目標的警示。 |
| 資源類型 | 選取一個或多個資源類型。 檢視僅會包含所選類型目標之具目標的警示。 指定資源群組之後，才可使用此欄。 |
| 資源 | 選取資源。 只有以該資源作為目標的警示才會包含在檢視中。 指定資源類型之後，才可使用此欄。 |
| Severity | 選擇警示嚴重性，或選擇 **"全部**"以包含所有嚴重性警報。 |
| 監視器條件 | 選擇監視器條件，或選擇 **"全部"** 以包含所有條件的警報。 |
| 警示狀態 | 選擇警報狀態，或選擇 **"全部**"以包含所有狀態的警報。 |
| 監視器服務 | 選擇服務，或選擇 **"全部"** 以包括所有服務。 只會包含由使用服務作為目標之規則所建立的警示。 |
| 時間範圍 | 視圖中僅包含在選定時間範圍內觸發的警報。 支援的值為過去 1 小時、過去 24 小時、過去 7 天和過去 30 天。 |

選擇頁面頂部的**列**以選擇要顯示的列。 

## <a name="alert-details-page"></a>警報詳細資訊頁面
選擇警報時，此頁面提供警報的詳細資訊，並使您能夠更改其狀態。

![警報詳細資訊頁面的螢幕截圖](media/alerts-overview/alert-detail2.png)

"警報詳細資訊"頁包括以下部分：

| 區段 | 描述 |
|:---|:---|
| 總結 | 顯示警示的內容和其他重要資訊。 |
| 記錄 | 列出警示採取的每個動作，以及對警示所做的任何變更。 目前僅限於狀態變更。 |
| 診斷 | 有關包含警報的智慧組的資訊。 「警示計數」** 是指智慧群組中包含的警示數目。 包括過去 30 天內創建的同一智慧組中的其他警報，而不考慮警報清單頁中的時間篩選器。 選取警示以檢視其詳細資料。 |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>警報實例的基於角色的存取控制 （RBAC）

警報實例的消耗和管理要求使用者具有[監視參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)或[監視讀取器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)的內置 RBAC 角色。 這些角色在任何 Azure 資源管理器作用域中都受支援，從訂閱級別到資源級別的細微性分配。 例如，如果使用者僅對虛擬機器`ContosoVM1`具有監視參與者存取權限，則該使用者可以僅使用和管理在 上`ContosoVM1`生成的警報。

## <a name="manage-your-alert-instances-programmatically"></a>以程式設計方式管理警報實例

您可能希望以程式設計方式查詢針對訂閱生成的警報。 這可能是在 Azure 門戶之外創建自訂視圖，或分析警報以識別模式和趨勢。

您可以通過使用[警報管理 REST API](https://aka.ms/alert-management-api)或使用[Azure 資源圖](../../governance/resource-graph/overview.md)和資源[REST API](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)來查詢針對訂閱生成的警報。

資源圖 REST API 允許您大規模查詢警報實例。 當您必須管理跨多個訂閱生成的警報時，建議這樣做。 

以下對資源圖 REST API 的示例請求返回一個訂閱中的警報計數：

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "AlertsManagementResources | where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()"
}
```

您還可以在門戶中通過 Azure 資源圖資源管理器查看此資源圖查詢的結果[：portal.azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AlertsManagementResources%20%7C%20where%20type%20%3D~%20%27Microsoft.AlertsManagement%2Falerts%27%20%7C%20summarize%20count())

您可以查詢警報中[的基本](alerts-common-schema-definitions.md#essentials)欄位。

使用[警報管理 REST API](https://aka.ms/alert-management-api)獲取有關特定警報的詳細資訊，包括其[警報上下文](alerts-common-schema-definitions.md#alert-context)欄位。

## <a name="next-steps"></a>後續步驟

- [深入了解智慧群組](https://aka.ms/smart-groups)
- [深入了解動作群組](../../azure-monitor/platform/action-groups.md)
- [在 Azure 中管理警示](https://aka.ms/managing-alert-instances)
- [管理智慧群組](https://aka.ms/managing-smart-groups)
- [瞭解有關 Azure 警報定價的更多](https://azure.microsoft.com/pricing/details/monitor/)






