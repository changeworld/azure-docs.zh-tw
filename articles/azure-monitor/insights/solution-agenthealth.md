---
title: Azure 監視器中的代理程式健全狀況解決方案 |Microsoft Docs
description: 本文旨在協助您了解如何使用這個解決方案，針對直接向 Log Analytics 或 System Center Operations Manager 回報的代理程式監視其健全狀況。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2020
ms.openlocfilehash: 9a7cb80b5510ff0ac4a2491d896aded866180c19
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062127"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Azure 監視器中的代理程式健全狀況解決方案
Azure 中的代理程式健全狀況解決方案可協助您瞭解，所有代理程式都會直接向 Azure 監視器中的 Log Analytics 工作區報告，或是連線到 Azure 監視器的 System Center Operations Manager 管理群組（沒有回應），正在提交運算元據。  您可以也追蹤已部署的代理程式數目，其散佈地區，並執行其他查詢，以留意 Azure、其他雲端環境或內部部署中部署之代理程式的散佈情形。    

## <a name="prerequisites"></a>必要條件
部署這個解決方案之前，請確認您目前支援向 Log Analytics 工作區或向與工作區整合之 [Operations Manager 管理群組](../../log-analytics/log-analytics-windows-agent.md)回報的 [Windows 代理程式](../../azure-monitor/platform/om-agents.md)。

## <a name="solution-components"></a>方案元件
此解決方案包含下列已新增到您的工作區以及直接連線之代理程式或 Operations Manager 連線之管理群組的資源。

### <a name="management-packs"></a>管理組件
如果 System Center Operations Manager 管理群組已連線到 Log Analytics 工作區，則下列管理組件會安裝在 Operations Manager 中。  新增此解決方案之後，這些管理組件也會安裝在直接連線的 Windows 電腦上。 這些管理組件不需要進行任何設定或管理。

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer)。  

如需有關方案管理組件如何更新的詳細資訊，請參閱 [將 Operations Manager 連接到 Log Analytics](../../azure-monitor/platform/om-agents.md)。

## <a name="configuration"></a>組態
使用[新增解決方案](solutions.md)中所述的程序，將代理程式健全狀況解決方案新增至您的 Log Analytics 工作區。 不需要進一步的組態。


## <a name="data-collection"></a>資料收集
### <a name="supported-agents"></a>支援的代理程式
下表描述此方案支援的連接來源。

| 連接的來源 | 支援 | 描述 |
| --- | --- | --- |
| Windows 代理程式 | 是 | 系統會從直接 Windows 代理程式收集活動訊號事件。|
| System Center Operations Manager 管理群組 | 是 | 系統會每隔60秒向管理群組回報的代理程式收集心跳事件，然後轉送至 Azure 監視器。 不需要從 Operations Manager 代理程式直接連接到 Azure 監視器。 將事件資料從管理群組轉送至 Log Analytics 工作區。|

## <a name="using-the-solution"></a>使用解決方案
當您將解決方案新增至 Log Analytics 工作區時，[代理程式健全狀況] 磚會新增至您的儀表板。 此圖格會顯示過去 24 小時內的代理程式總數和沒有回應的代理程式數目。<br><br> ![儀表板上的代理程式健全狀況圖格](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

按一下 [代理程式健全狀況] 圖格，以開啟 [代理程式健全狀況] 儀表板。  此儀表板包含下表中的資料行。 每個資料行依計數列出前十個事件，這幾個事件符合該資料行中指定時間範圍的準則。 您可以選取每個資料行右下角的 [查看全部]，或按一下資料行標頭，以執行記錄搜尋來提供完整清單。

| 資料行 | 描述 |
|--------|-------------|
| 不同時間的代理程式計數 | Linux 和 Windows 代理程式為期七天的代理程式計數趨勢。|
| 沒有回應的代理程式計數 | 在過去 24 小時內尚未傳送活動訊號的代理程式清單。|
| 依 OS 類型分配 | 劃分您的環境中有多少個 Windows 和 Linux 代理程式。|
| 依代理程式版本分配 | 劃分您的環境中安裝的不同代理程式版本，以及每個版本的計數。|
| 依代理程式類別分配 | 劃分傳送活動訊號事件的不同代理程式類別：直接代理程式、OpsMgr 代理程式或 OpsMgr 管理伺服器。|
| 依管理群組分配 | 環境中不同 Operations Manager 管理群組的資料分割。|
| 代理程式的地理位置 | 您擁有代理程式之不同國家/地區的分割區，以及每個國家/地區中已安裝之代理程式數目的總計數。|
| 已安裝的閘道計數 | 已安裝 Log Analytics 閘道的伺服器數目，以及這些伺服器的清單。|

![代理程式健全狀況儀表板範例](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Azure 監視器記錄
此解決方案會在 Log Analytics 工作區中建立一種類型的記錄。  

### <a name="heartbeat-records"></a>活動訊號記錄
系統會建立類型為 [活動訊號] 的記錄。  這些記錄具有下表中的屬性。  

| 屬性 | 描述 |
| --- | --- |
| `Type` | *活動訊號*|
| `Category` | 值為 [直接代理程式]、[SCOM 代理程式] 或 [SCOM 管理伺服器]。|
| `Computer` | 電腦名稱。|
| `OSType` | Windows 或 Linux 作業系統。|
| `OSMajorVersion` | 作業系統主要版本。|
| `OSMinorVersion` | 作業系統次要版本。|
| `Version` | Log Analytics 代理程式或 Operations Manager 代理程式版本。|
| `SCAgentChannel` | 值為 [Direct] 和/或 [SCManagementServer]。|
| `IsGatewayInstalled` | 如果已安裝 Log Analytics 閘道，則值為「true」，否則值為「false」。|
| `ComputerIP` | 電腦的公用 IP 位址。 在 Azure Vm 上，這會顯示公用 IP （如果有的話）。 針對使用私人 ip 的 Vm，這會顯示 Azure SNAT 位址（而非私人 IP 位址）。 |
| `RemoteIPCountry` | 電腦部署所在的地理位置。|
| `ManagementGroupName` | Operations Manager 管理群組的名稱。|
| `SourceComputerId` | 電腦的唯一識別碼。|
| `RemoteIPLongitude` | 電腦的地理位置經度。|
| `RemoteIPLatitude` | 電腦的地理位置緯度。|

向 Operations Manager 管理伺服器回報的每個代理程式會傳送兩個心跳，而 SCAgentChannel 屬性的值將會包含**Direct**和**scmanagementserver (** ，這取決於您在訂用帳戶中啟用的資料來源和監視解決方案。 如果您還記得，解決方案中的資料會從 Operations Manager 管理伺服器直接傳送到 Azure 監視器，或因為代理程式上收集的資料量而直接從代理程式傳送至 Azure 監視器。 若為具有 **SCManagementServer** 值的活動訊號事件，ComputerIP 值為管理伺服器的 IP 位址，因為資料實際上由它上傳。  若為 SCAgentChannel 設為 [Direct] 的活動訊號，此值為代理程式的公用 IP 位址。  

## <a name="sample-log-searches"></a>記錄搜尋範例
下表提供此解決方案所收集之記錄的記錄搜尋範例。

| 查詢 | 描述 |
|:---|:---|
| Heartbeat &#124; distinct Computer |代理程式總數 |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |過去 24 小時內沒有回應的代理程式計數 |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |過去 15 分鐘內沒有回應的代理程式計數 |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |在線上的電腦 (過去 24 小時內) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |在過去 30 分鐘內離線的代理程式總數 (針對過去 24 小時) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |依 OSType 取得一段時間內的代理程式數目趨勢|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |依 OS 類型分配 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |依代理程式版本分配 |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |依代理程式類別分配 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | 依管理群組分配 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |代理程式的地理位置 |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |安裝的 Log Analytics 閘道數目 |




## <a name="next-steps"></a>後續步驟

* 如需有關從記錄查詢產生警示的詳細資訊，請瞭解[Azure 監視器中的警示](../platform/alerts-overview.md)。 
