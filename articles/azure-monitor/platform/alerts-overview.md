---
title: Azure 中的警示與通知監視概觀
description: Azure 中的警示概觀。 警示、傳統警示和警示介面。
ms.subservice: alerts
ms.topic: conceptual
ms.date: 01/28/2018
ms.openlocfilehash: d0f71ebf8a7acab6d4b46f59049bf1efd5290284
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186638"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Microsoft Azure 中的警示概觀 

本文章說明何謂警示、其優點，以及如何開始使用它們。  

## <a name="what-are-alerts-in-microsoft-azure"></a>Microsoft Azure 中的警示是什麼？

當您的基礎結構或應用程式使用您的監視資料在 Azure 監視器中找到問題時，警示會主動通知您。 它們可讓您在系統使用者注意到問題之前，找出並解決問題。 

## <a name="overview"></a>總覽

下圖代表警示流程。 

![警示流程的圖表](media/alerts-overview/Azure-Monitor-Alerts.svg)

警示規則會與警示和引發警示時所採取的動作分開。 警示規則會捕捉警示的目標和準則。 警示規則可處於已啟用或已停用狀態。 警示只有在啟用時才會引發。 

以下是警示規則的重要屬性：

**目標資源** - 定義用於警示的範圍和訊號。 目標可以是任何 Azure 資源。 範例目標：

- 虛擬機器。
- 儲存體帳戶。
- Log Analytics 工作區。
- Application Insights。 

針對某些資源 (例如虛擬機器) ，您可以將多個資源指定為警示規則的目標。

由目標資源發出的 **信號**。 信號可以是下列類型：度量、活動記錄、Application Insights 和記錄。

**準則** -套用於目標資源的信號和邏輯組合。 範例： 

- 百分比 CPU > 70%
- 伺服器回應時間 > 4 ms 
- 記錄查詢的結果計數 > 100

**警示名稱** -使用者所設定之警示規則的特定名稱。

**警示描述** -使用者所設定之警示規則的描述。

**嚴重性** -符合警示規則中指定的準則之後的警示嚴重性。 嚴重性的範圍可從 0 到 4。

- 嚴重性 0 = 重大
- 嚴重性 1 = 錯誤
- 嚴重性 2 = 警告
- 嚴重性 3 = 資訊
- 嚴重性 4 = 詳細資訊 

**動作** - 引發警示時所採取的動作。 如需詳細資訊，請參閱 [動作群組](./action-groups.md)。

## <a name="what-you-can-alert-on"></a>您可以發出警示的對象

您可以警示計量和記錄，如 [監視資料來源](./data-sources.md)中所述。 信號包含但不限於：

- 計量值
- 記錄搜尋查詢
- 活動記錄事件
- 底層 Azure 平台健康情況
- 網站可用性測試

## <a name="manage-alerts"></a>管理警示

您可以設定警示的狀態來指定警示在解決流程中的位置。 當符合警示規則中指定的準則時，就會建立或引發警示，而且其狀態會是 [ *新增*]。 當您認可警示並將它關閉時，您可以變更狀態。 任何狀態變更都會儲存在警示的記錄中。

支援下列警示狀態：

| 州 | 描述 |
|:---|:---|
| 新增 | 已偵測到問題，但尚未經過檢查。 |
| 已認可 | 系統管理員已檢閱警示，且已開始處理。 |
| 封閉式 | 已解決問題。 關閉警示之後，您可以將警示變更為另一個狀態以重新開啟它。 |

*警示狀態* 與 *監視條件* 不同且無關。 警示狀態是由使用者所設定的。 監視條檢是由系統所設定的。 當警示引發時，警示的監視條件會設定為「已 *引發*」，當引發警示的基礎條件清除時，監視條件會設定為「 *已解決*」。 

警示狀態需等到使用者變更它之後才會變更。 了解[如何變更警示與智慧群組的狀態](./alerts-managing-alert-states.md?toc=%2fazure%2fazure-monitor%2ftoc.json)。

## <a name="alerts-experience"></a>警示體驗 
[預設警示] 頁面會提供在特定時間範圍內建立之警示的摘要。 它會顯示每個嚴重性的警示總數，其中的資料行會指出每個嚴重性的每個狀態的警示總數。 選取任何嚴重性以開啟依照該嚴重性篩選的 [所有警示](#all-alerts-page) 頁面。

相反地，您可以 [使用 REST api，以程式設計方式列舉訂用帳戶上所產生的警示實例](#manage-your-alert-instances-programmatically)。

> [!NOTE]
   >  您只能存取過去30天內產生的警示。

它不會顯示或追蹤傳統警示。 您可以變更訂用帳戶或篩選參數來更新頁面。 

![警示頁面的螢幕擷取畫面](media/alerts-overview/alerts-page.png)

您可以在頁面頂端的下拉式功能表中選取值，以篩選此視圖。

| 資料行 | 描述 |
|:---|:---|
| 訂用帳戶 | 選取您要查看其警示的 Azure 訂用帳戶。 您可以選擇性地選擇選取所有訂用帳戶。 只有您在選取的訂用帳戶中有權存取的警示才會包含在此視圖中。 |
| 資源群組 | 選取單一資源群組。 檢視僅會包含所選資源群組中具有目標的警示。 |
| 時間範圍 | 只有在所選時間範圍內引發的警示才會包含在此視圖中。 支援的值為過去 1 小時、過去 24 小時、過去 7 天和過去 30 天。 |

選取 [警示] 頁面頂端的下列值，以開啟另一個頁面：

| 值 | 描述 |
|:---|:---|
| 警示總計 | 符合所選準則的警示總數。 選取此值以開啟沒有任何篩選的 [所有警示] 檢視。 |
| 智慧群組 | 從符合所選準則之警示建立的智慧群組總數。 選取此值將開啟 [所有警示] 檢視中的智慧群組清單。
| 警示規則總計 | 所選訂用帳戶和資源群組中的警示規則總數。 選取此值以開啟在選取的訂用帳戶與資源群組上篩選的 [規則] 檢視。


## <a name="manage-alert-rules"></a>管理警示規則
若要顯示 [ **規則** ] 頁面，請選取 [ **管理警示規則**]。 [規則] 頁面是管理整個 Azure 訂用帳戶之所有警示規則的單一位置。 它會列出所有警示規則，並可根據目標資源、資源群組、規則名稱或狀態來排序。 您也可以從這個頁面編輯、啟用或停用警示規則。  

 ![規則頁面的螢幕擷取畫面](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>建立警示規則
您可以用一致的方式撰寫警示規則，不論監視服務或信號類型為何。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tflw]

 
以下說明如何建立新的警示規則：
1. 挑選警示的 _目標_。
1. 從目標的可用訊號中選取 _訊號_。
1. 從訊號指定套用於資料的 _邏輯_。

這個簡化的編寫程序讓您不再需要先知道監視來源或支援的訊號，就能選取 Azure 資源。 可用訊號的清單會自動根據您選取的目標資源進行篩選。 此外，也會根據該目標，逐步引導您定義警示規則的邏輯。  

您可以參閱[使用 Azure 監視器來建立、檢視及管理警示](./alerts-metric.md)，深入了解如何建立警示規則。

警示可跨數個 Azure 監視服務使用。 如需如何和何時使用每個服務的資訊，請參閱[監視 Azure 應用程式和資源](../overview.md)。 


## <a name="all-alerts-page"></a>[所有警示] 頁面 
若要查看 [ **所有警示** ] 頁面，請選取 [ **警示總計**]。 您可以在這裡查看在選取的時間內建立的警示清單。 您可以檢視個別警示的清單，或包含警示的智慧群組清單。 選取頁面頂端的橫幅以切換檢視。

![[所有警示] 頁面的螢幕擷取畫面](media/alerts-overview/all-alerts-page.png)

您可以在頁面頂端的下拉式功能表中選取下列值，以篩選視圖：

| 資料行 | 描述 |
|:---|:---|
| 訂用帳戶 | 選取您要查看其警示的 Azure 訂用帳戶。 您可以選擇性地選擇選取所有訂用帳戶。 只有您在選取的訂用帳戶中有權存取的警示才會包含在此視圖中。 |
| 資源群組 | 選取單一資源群組。 檢視僅會包含所選資源群組中具有目標的警示。 |
| 資源類型 | 選取一個或多個資源類型。 檢視僅會包含所選類型目標之具目標的警示。 指定資源群組之後，才可使用此欄。 |
| 資源 | 選取資源。 只有以該資源作為目標的警示才會包含在檢視中。 指定資源類型之後，才可使用此欄。 |
| 嚴重性 | 選取警示嚴重性，或選取 [ **全部** ] 以包含所有嚴重性的警示。 |
| 監視器條件 | 選取監視條件，或選取 [ **全部** ] 以包含所有條件的警示。 |
| 警示狀態 | 選取警示狀態，或選取 [ **全部** ] 以包含所有狀態的警示。 |
| 監視器服務 | 選取服務，或選取 [ **全部** ] 以包含所有服務。 只會包含由使用服務作為目標之規則所建立的警示。 |
| 時間範圍 | 只有在所選時間範圍內引發的警示才會包含在此視圖中。 支援的值為過去 1 小時、過去 24 小時、過去 7 天和過去 30 天。 |

選取頁面頂端的 [資料 **行** ]，以選取要顯示的資料行。 

## <a name="alert-details-page"></a>警示詳細資料頁面
當您選取警示時，此頁面會提供警示的詳細資料，並可讓您變更其狀態。

![警示詳細資料頁面的螢幕擷取畫面](media/alerts-overview/alert-detail2.png)

[警示詳細資料] 頁面包含下列各節：

| 區段 | 描述 |
|:---|:---|
| 摘要 | 顯示警示的內容和其他重要資訊。 |
| 歷程記錄 | 列出警示採取的每個動作，以及對警示所做的任何變更。 目前僅限於狀態變更。 |
| 診斷 | 包含警示之智慧群組的相關資訊。 「警示計數」是指智慧群組中包含的警示數目。 在過去30天內建立的相同智慧群組中包含其他警示，警示清單頁面中的任何時間篩選準則。 選取警示以檢視其詳細資料。 |

## <a name="azure-role-based-access-control-azure-rbac-for-your-alert-instances"></a>Azure 角色型存取控制 (Azure RBAC) 適用于您的警示實例

警示實例的耗用量和管理需要使用者擁有 [監視參與者](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 或 [監視讀者](../../role-based-access-control/built-in-roles.md#monitoring-reader)的 Azure 內建角色。 這些角色可在任何 Azure Resource Manager 範圍 (從訂用帳戶層級到資源層級的細微指派) 中受到支援。 例如，如果使用者只擁有虛擬機器的監視參與者存取權 `ContosoVM1` ，該使用者就只能取用和管理在上產生的警示 `ContosoVM1` 。

## <a name="manage-your-alert-instances-programmatically"></a>以程式設計方式管理您的警示實例

您可能會想要以程式設計方式查詢針對您的訂用帳戶所產生的警示。 查詢可能是在 Azure 入口網站之外建立自訂的視圖，或是用來分析您的警示以找出模式和趨勢。

您可以使用 [警示管理 REST API](/rest/api/monitor/alertsmanagement/alerts) ，或使用 [Azure Resource Graph](../../governance/resource-graph/overview.md) 和 [資源的 REST API](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)，查詢針對您的訂用帳戶所產生的警示。

資源的 Resource Graph REST API 可讓您大規模查詢警示實例。 當您必須管理跨多個訂用帳戶所產生的警示時，建議使用 Resource Graph。 

下列 Resource Graph 要求範例 REST API 傳回一個訂用帳戶中的警示計數：

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "AlertsManagementResources | where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()"
}
```

您也可以在入口網站中使用 Azure Resource Graph Explorer 來查看此 Resource Graph 查詢的結果： [portal.azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AlertsManagementResources%20%7C%20where%20type%20%3D~%20%27Microsoft.AlertsManagement%2Falerts%27%20%7C%20summarize%20count())

您可以查詢警示中的 [重要](alerts-common-schema-definitions.md#essentials) 欄位。

使用 [警示管理 REST API](/rest/api/monitor/alertsmanagement/alerts) 取得特定警示的詳細資訊，包括其 [警示內容](alerts-common-schema-definitions.md#alert-context) 欄位。

## <a name="smart-groups"></a>智慧群組

智慧群組是以機器學習演算法為基礎的警示匯總，有助於減少警示雜訊並協助進行疑難排解。 [深入了解智慧群組](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)和[如何管理智慧群組](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json)。

## <a name="next-steps"></a>後續步驟

- [深入了解智慧群組](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [深入了解動作群組](./action-groups.md)
- [在 Azure 中管理警示](./alerts-managing-alert-instances.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [管理智慧群組](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [深入瞭解 Azure 警示定價](https://azure.microsoft.com/pricing/details/monitor/)