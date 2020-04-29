---
title: 使用 Azure 監視器監視 Azure 虛擬機器
description: 說明如何使用 Azure 監視器，從 Azure 中的虛擬機器收集和分析監視資料。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 2cb53d0c88d8c29da2bd8bf52d6536555d56c76e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80283934"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>使用 Azure 監視器監視 Azure 虛擬機器
本文說明如何使用 Azure 監視器來收集和分析來自 Azure 虛擬機器的監視資料，以維持其健康狀態。 虛擬機器可以使用與任何[其他 Azure 資源](monitor-azure-resource.md)一樣的 Azure 監視器來監視可用性和效能，但它們與其他資源是唯一的，因為您也需要監視來賓作業和系統以及其中執行的工作負載。 

> [!NOTE]
> 本文提供在 Azure 監視器中監視虛擬機器的概念和選項的完整總覽。 若要快速開始監視您的虛擬機器，而不著重于基礎概念，請參閱[快速入門：使用 Azure 監視器監視 Azure 虛擬機器](../learn/quick-monitor-azure-vm.md)。


## <a name="differences-from-other-azure-resources"></a>與其他 Azure 資源的差異
[使用 Azure 監視器監視 azure 資源](monitor-azure-resource.md)會說明 azure 資源所產生的監視資料，以及如何使用 Azure 監視器的功能來分析和警示此資料。 您可以從 Azure 虛擬機器收集並採取相同的監視資料，但有下列差異：

- 系統會針對虛擬機器自動收集[平臺計量](../platform/data-platform-metrics.md)，但僅針對[虛擬機器主機](#monitoring-data)。 您需要代理程式來收集來自客體作業系統的效能資料。 
- 虛擬機器不會產生[資源記錄](../platform/platform-logs-overview.md)，可讓您深入瞭解在 Azure 資源內執行的作業。 您可以使用代理程式，從客體作業系統收集記錄資料。
- 您可以建立虛擬機器的[診斷設定](../platform/diagnostic-settings.md)，以將平臺計量傳送至其他目的地（例如儲存體和事件中樞），但您無法在 Azure 入口網站中設定這些診斷設定。 

## <a name="monitoring-data"></a>監視資料
Azure 中的虛擬機器會產生[記錄](../platform/data-platform-logs.md)和[計量](../platform/data-platform-metrics.md)，如下圖所示。

![總覽](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>虛擬機器主機
Azure 中的虛擬機器會針對虛擬機器主機產生下列資料，如同[監視資料](monitor-azure-resource.md#monitoring-data)中所述的其他 Azure 資源一樣。

- [平臺計量](../platform/data-platform-metrics.md)-自動收集的數值，並在特定時間描述資源的某些層面。 系統會針對虛擬機器主機收集平臺計量，但您需要診斷擴充功能來收集客體作業系統的計量。
- [活動記錄](../platform/platform-logs-overview.md)-可讓您深入瞭解訂用帳戶（管理平面）中的每個 Azure 資源上的作業。 若為虛擬機器，這會包含啟動時的資訊，以及任何設定變更。


### <a name="guest-operating-system"></a>客體作業系統
若要從虛擬機器的客體作業系統收集資料，您需要代理程式，它會在每部虛擬機器上本機執行，並將資料傳送至 Azure 監視器。 有多個代理程式可供 Azure 監視器使用，每個都收集不同的資料，並將資料寫入不同的位置。 在[Azure 監視器代理程式總覽](../platform/agents-overview.md)中，取得不同代理程式的詳細比較。 

- [Log Analytics 代理](../platform/agents-overview.md#log-analytics-agent)程式-適用于 Azure 中的虛擬機器、其他雲端環境，以及內部部署。 收集資料以 Azure 監視器記錄。 支援適用於 VM 的 Azure 監視器和監視解決方案。 這是用於 System Center Operations Manager 的相同代理程式。
- 相依性[代理](../platform/agents-overview.md#dependency-agent)程式-收集在虛擬機器上執行的進程及其相依性的相關資料。 依賴 Log Analytics 代理程式將資料傳輸至 Azure，並支援適用於 VM 的 Azure 監視器、服務對應和 Wire Data 2.0 解決方案。
- [Azure 診斷擴充](../platform/agents-overview.md#azure-diagnostics-extension)功能-僅適用于 Azure 監視器虛擬機器。 可以將資料收集到多個位置，但主要是用來將來賓效能資料收集到 Windows 虛擬機器的 Azure 監視器計量中。
- [Telegraf 代理](../platform/collect-custom-metrics-linux-telegraf.md)程式-將 Linux vm 的效能資料收集到 Azure 監視器計量中。


## <a name="configuration-requirements"></a>組態需求
若要啟用 Azure 監視器的所有功能來監視虛擬機器，您需要將監視資料從虛擬機器主機和客體作業系統收集到[Azure 監視器計量](../platform/data-platform-logs.md)和[Azure 監視器記錄](../platform/data-platform-logs.md)。 下表列出必須執行才能啟用此集合的設定。 根據您的特定需求，您可以選擇不要執行所有這些步驟。

| 設定步驟 | 已完成的動作 | 已啟用功能 |
|:---|:---|:---|
| 無組態 | -收集到計量的主機平臺計量。<br>-已收集活動記錄。 | -主機的計量瀏覽器。<br>-主機的計量警示。<br>-活動記錄警示。 |
| [啟用適用於 VM 的 Azure 監視器](#enable-azure-monitor-for-vms) | -已安裝 Log Analytics 代理程式。<br>-已安裝 Dependency agent。<br>-收集到記錄的來賓效能資料。<br>-收集到記錄檔的進程和相依性詳細資料。 | -適用于來賓效能資料的效能圖表和活頁簿。<br>-記錄來賓效能資料的查詢。<br>-來賓效能資料的記錄警示。<br>-相依性對應。 |
| [安裝診斷擴充功能和 telegraf 代理程式](#enable-diagnostics-extension-and-telegraf-agent) | -收集到計量的來賓效能資料。 | -適用于來賓的計量瀏覽器。<br>-來賓的計量警示。  |
| [設定 Log Analytics 工作區](#configure-log-analytics-workspace) | -從來賓收集的事件。 | -適用于來賓事件的記錄查詢。<br>-來賓事件的記錄警示。 |
| [建立虛擬機器的診斷設定](#collect-platform-metrics-and-activity-log) | -收集到記錄的平臺計量。<br>-收集到記錄的活動記錄檔。 | -Loq 主機計量的查詢。<br>-主機計量的記錄警示。<br>-記錄活動記錄的查詢。

下列各節將說明每個設定步驟。

### <a name="enable-azure-monitor-for-vms"></a>啟用適用於 VM 的 Azure 監視器
[適用於 VM 的 Azure 監視器](vminsights-overview.md)是 Azure 監視器中的[深入](insights-overview.md)解析，這是在 Azure 監視器中監視虛擬機器的主要工具。 它透過標準 Azure 監視器功能提供下列額外的價值。

- 簡化 Log Analytics 代理程式和相依性代理程式的上線，以啟用虛擬機器客體作業系統和工作負載的監視。 
- 預先定義的趨勢效能圖表和活頁簿，可讓您從虛擬機器的客體作業系統分析核心效能計量。
- 相依性對應會顯示在每部虛擬機器上執行的處理常式，以及與其他電腦和外部來源相互關聯的元件。

![適用於 VM 的 Azure 監視器](media/monitor-vm-azure/vminsights-01.png)

![適用於 VM 的 Azure 監視器](media/monitor-vm-azure/vminsights-02.png)


從 Azure 入口網站的 [虛擬機器] 功能表中的 [**深入**解析] 選項啟用適用於 VM 的 Azure 監視器。 如需詳細資訊和其他設定方法，請參閱[啟用適用於 VM 的 Azure 監視器總覽](vminsights-enable-overview.md)。

![啟用適用於 VM 的 Azure 監視器](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>設定 Log Analytics 工作區
適用於 VM 的 Azure 監視器使用的 Log Analytics 代理程式會將資料傳送至[Log analytics 工作區](../platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured)。 您可以藉由設定 Log Analytics 工作區，啟用從代理程式收集其他效能資料、事件和其他監視資料。 它只需要設定一次，因為任何連線到工作區的代理程式都會自動下載設定，並立即開始收集定義的資料。 

您可以從 [**開始**] 選取 [**工作區**設定]，直接從適用於 VM 的 Azure 監視器存取工作區的設定。 按一下工作區名稱，以開啟其功能表。

![工作區設定](media/monitor-vm-azure/workspace-configuration.png)

從 [工作區] 功能表中選取 [ **Advanced Settings** ]，然後從 [**資料**] 設定資料來源。 若是 Windows 代理程式，請選取 [ **Windows 事件記錄**檔]，然後新增常見的事件記錄檔，例如*系統*和*應用程式*。 若是 Linux 代理程式，請選取 [ **Syslog** ]，然後新增 [*字偶間距*和*守護*程式] 之類的一般功能。 如需可用的資料來源清單和設定的詳細資訊，請參閱[Azure 監視器中的代理程式資料來源](../platform/agent-data-sources.md)。 

![設定事件](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> 您可以設定從工作區設定收集效能計數器，但這可能會與適用於 VM 的 Azure 監視器所收集的效能計數器重複。 適用於 VM 的 Azure 監視器會以每分鐘一次的頻率收集最常見的一組計數器。 只有在您想要收集適用於 VM 的 Azure 監視器尚未收集的計數器，或如果您有使用效能資料的常設查詢時，才設定工作區要收集的效能計數器。


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>啟用診斷擴充功能和 Telegraf 代理程式
適用於 VM 的 Azure 監視器是以將資料收集到 Log Analytics 工作區的 Log Analytics 代理程式為基礎。 這支援[Azure 監視器的多項功能](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)，例如[記錄查詢](../log-query/log-query-overview.md)、[記錄警示](../platform/alerts-log.md)和活頁[簿](../platform/workbooks-overview.md)。 [診斷擴充](../platform/diagnostics-extension-overview.md)功能會從 Windows 虛擬機器的客體作業系統收集效能資料，以 Azure 儲存體並選擇性地將效能資料傳送至[Azure 監視器計量](../platform/data-platform-metrics.md)。 針對 Linux 虛擬機器，必須要有[Telegraf 代理程式](../platform/collect-custom-metrics-linux-telegraf.md)，才能將資料傳送至 Azure 計量。  這會啟用 Azure 監視器的其他功能，例如[計量瀏覽器](../platform/metrics-getting-started.md)和[計量警示](../platform/alerts-metric.md)。 您也可以使用 Azure 事件中樞，將診斷擴充功能設定為在 Azure 監視器外部傳送事件和效能資料。

在 [Azure 入口網站中，從 [VM] 功能表的 [**診斷] 設定**選項，安裝單一 Windows 虛擬機器的診斷延伸模組。 選取選項以啟用 [**接收**] 索引標籤中的**Azure 監視器**。若要從範本或命令列為多部虛擬機器啟用擴充功能，請參閱[安裝和](../platform/diagnostics-extension-overview.md#installation-and-configuration)設定。 不同于 Log Analytics 代理程式，要收集的資料會定義在每個虛擬機器上的擴充功能設定中。

![診斷設定](media/monitor-vm-azure/diagnostic-setting.png)

如需在 Linux 虛擬機器上設定 Telegraf 代理程式的詳細資訊，請參閱[安裝和設定 Telegraf](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) 。 [**診斷設定**] 功能表選項適用于 Linux，但它只允許您將資料傳送至 Azure 儲存體。

### <a name="collect-platform-metrics-and-activity-log"></a>收集平臺計量和活動記錄
您可以在 Azure 入口網站中，查看針對每部虛擬機器主機收集的平臺計量和活動記錄。 將此資料收集到與適用於 VM 的 Azure 監視器相同的 Log Analytics 工作區，以使用針對虛擬機器所收集的其他監視資料進行分析。 此集合是以[診斷設定](../platform/diagnostic-settings.md)進行設定。 使用訂用帳戶的[診斷設定](../platform/diagnostic-settings.md#create-diagnostic-settings-in-azure-portal)收集活動記錄。

使用虛擬機器的診斷設定收集平臺計量。 與其他 Azure 資源不同的是，您無法在 Azure 入口網站中建立虛擬機器的診斷設定，但必須使用[另一種方法](../platform/diagnostic-settings.md#create-diagnostic-settings-using-powershell)。 下列範例示範如何使用 PowerShell 和 CLI 來收集虛擬機器的計量。

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace

```

## <a name="monitoring-in-the-azure-portal"></a>Azure 入口網站中的監視 
設定虛擬機器的監視資料收集之後，您有多個選項可供您在 Azure 入口網站中進行存取：

- 使用 [ **Azure 監視器**] 功能表來存取所有受監視資源的資料。 
- 使用適用於 VM 的 Azure 監視器來大規模監視虛擬機器的集合。
- 從 Azure 入口網站中的功能表，分析單一虛擬機器的資料。 下表列出監視 [虛擬機器] 功能表的不同選項。

![Azure 入口網站中的監視](media/monitor-vm-azure/monitor-menu.png)

| 功能表選項 | 描述 |
|:---|:---|
| 總覽 | 顯示虛擬機器主機的[平臺計量](../platform/data-platform-metrics.md)。 按一下圖表，即可在[計量瀏覽器](../platform/metrics-getting-started.md)中使用此資料。 |
| 活動記錄檔 | 針對目前虛擬機器篩選的[活動記錄](../platform/activity-log-view.md)專案。 |
| 深入資訊 | 使用所選目前虛擬機器的對應來開啟[適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md)。 |
| 警示 | 查看目前虛擬機器的[警示](../platform/alerts-overview.md)。  |
| 計量 | 開啟 [[計量瀏覽器](../platform/metrics-getting-started.md)]，並將範圍設定為目前的虛擬機器。 |
| 診斷設定 | 啟用並設定目前虛擬機器的[診斷延伸](../platform/diagnostics-extension-overview.md)模組。 |
| Advisor 建議 | [Azure Advisor](/azure/advisor/)目前虛擬機器的建議。 |
| 記錄 | 開啟[Log Analytics](../log-query/log-query-overview.md#what-is-log-analytics) ，並將[範圍](../log-query/scope.md)設定為目前的虛擬機器。 |
| 連線監視 | 開啟[網路監看員連線監視器](../../network-watcher/connection-monitor-preview.md)，以監視目前虛擬機器與其他虛擬機器之間的連線。 |


## <a name="analyzing-metric-data"></a>分析度量資料
您可以從虛擬機器的功能表開啟 [**計量**]，以使用計量瀏覽器來分析虛擬機器的計量。 如需使用此工具的詳細資訊，請參閱[開始使用 Azure 計量瀏覽器](../platform/metrics-getting-started.md)。 

虛擬機器所使用的命名空間有兩個用於計量：

| 命名空間 | 描述 |
|:---|:---|
| 虛擬機器主機 | 自動收集所有 Azure 虛擬機器的主機計量。 Microsoft 的計量詳細清單[。 Compute/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines)。 |
| 虛擬機器來賓 | 從已安裝診斷擴充功能並設定為傳送至 Azure 監視器接收的虛擬機器收集的客體作業系統計量。 |

![計量](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>分析記錄資料
Azure 虛擬機器將會收集下列資料以 Azure 監視器記錄。 

適用於 VM 的 Azure 監視器可讓您收集一組預先定義的效能計數器，並將其寫入*InsightsMetrics*資料表。 這是[Azure 監視器用於容器](container-insights-overview.md)的相同資料表。 

| 資料來源 | 需求 | 資料表 |
|:---|:---|:---|
| 適用於 VM 的 Azure 監視器 | 在每部虛擬機器上啟用。 | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>如需詳細資訊，請參閱[如何從適用於 VM 的 Azure 監視器查詢記錄](vminsights-log-search.md)。 |
| 活動記錄檔 | 訂用帳戶的診斷設定。 | AzureActivity |
| 主機計量 | 虛擬機器的診斷設定。 | AzureMetrics |
| 來自客體作業系統的資料來源 | 啟用 Log Analytics 代理程式並設定資料來源。 | 請參閱每個資料來源的檔。 |


> [!NOTE]
> Log Analytics 代理程式所收集的效能資料會寫入到*Perf*資料表，而適用於 VM 的 Azure 監視器會將它收集至*InsightsMetrics*資料表。 這是相同的資料，但資料表具有不同的結構。 如果您的常設查詢是以效能為基礎，則必須重*寫，才能*使用*InsightsMetrics*。


## <a name="alerts"></a>警示
Azure 監視器中的[警示](../platform/alerts-overview.md)會在您的監視資料中發現重要條件時主動通知您，而且可能會啟動邏輯應用程式或呼叫 webhook 之類的動作。 警示規則會定義用來判斷何時應建立警示的邏輯。 Azure 監視器會收集警示規則所使用的資料，但您必須建立規則，以定義 Azure 訂用帳戶中的警示條件。

下列各節說明警示規則的類型，以及每次使用時的建議。 這項建議是以警示規則類型的功能和成本為基礎。 如需警示的詳細定價，請參閱[Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。


### <a name="activity-log-alert-rules"></a>活動記錄警示規則
活動記錄[警示規則](../platform/alerts-activity-log.md)會在活動記錄中建立符合特定準則的專案時引發。 它們不會有任何費用，因此如果您所需的邏輯是在活動記錄中，它們應該是您的第一個選擇。 

活動記錄警示的目標資源可以是特定的虛擬機器、資源群組中的所有虛擬機器，或訂用帳戶中的所有虛擬機器。

例如，選取 [*關閉虛擬機器的電源*] 以取得信號名稱，以建立警示（如果關鍵虛擬機器已停止）。

![活動記錄警示](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>計量警示規則
度量[警示規則](../platform/alerts-metric.md)會在計量值超過閾值時引發。 您可以定義特定的閾值，或允許 Azure 監視器根據歷程記錄資料動態判斷臨界值。  盡可能使用計量警示與計量資料，因為其成本較低，且回應速度比記錄警示規則更快。 它們也是可設定狀態的，這表示當計量低於臨界值時，它們會自行解決。

活動記錄警示的目標資源可以是特定的虛擬機器或資源群組中的所有虛擬機器。

例如，若要在虛擬機器的處理器超過特定值時建立警示，請使用 [ *CPU 百分比*] 做為信號類型來建立計量警示規則。 設定特定的臨界值，或允許 Azure 監視器設定動態閾值。 

![度量警示](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>記錄警示
當排程記錄查詢的結果符合特定準則時，就會引發[記錄警示規則](../platform/alerts-log.md)。 記錄查詢警示是警示規則最昂貴且最不回應的，但它們可以存取最多樣化的資料，並可執行無法由其他警示規則執行的複雜邏輯。 

記錄查詢的目標資源是 Log Analytics 工作區。 篩選查詢中的特定電腦。

例如，若要建立警示來檢查特定資源群組中的任何虛擬機器是否離線，請使用下列查詢，這會針對每一部在過去十分鐘內錯過了心跳的電腦傳回記錄。 如果至少有一部電腦有遺失的心跳，請使用閾值1，這會引發。

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![記錄警示](media/monitor-vm-azure/log-alert-01.png)

若要在訂用帳戶中的任何 Windows 虛擬機器上發生過多的失敗登入次數時建立警示，請使用下列查詢，這會傳回過去一小時內每個失敗登入事件的記錄。 使用 [閾值] 設為您允許的失敗登入數目。 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![記錄警示](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager （SCOM）提供虛擬機器上工作負載的細微監視。 如需監視平臺的比較和不同的執行策略，請參閱[雲端監視指南](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/)。

如果您有想要繼續使用的現有 SCOM 環境，您可以將它與 Azure 監視器整合，以提供額外的功能。 Azure 監視器所使用的 Log Analytics 代理程式與 SCOM 所使用的相同，因此您已受監視的虛擬機器將資料傳送至兩者。 您仍然需要將代理程式新增至適用於 VM 的 Azure 監視器，並設定工作區收集其他資料（如上所示），但是虛擬機器可以繼續在 SCOM 環境中執行其現有的管理元件，而不需進行修改。

增強現有 SCOM 功能的 Azure 監視器功能包括下列各項：

- 使用 Log Analytics 以互動方式分析您的記錄檔和效能資料。
- 使用記錄警示來定義跨多部虛擬機器的警示狀況，並使用不可能使用 SCOM 中警示的長期趨勢。   

如需將現有 SCOM 管理群組連線至 Log Analytics 工作區的詳細資訊，請參閱[將 Operations Manager 連線至 Azure 監視器](../platform/om-agents.md)。


## <a name="next-steps"></a>後續步驟

* [瞭解如何使用記錄查詢來分析 Azure 監視器記錄中的資料。](../log-query/get-started-queries.md)
* [瞭解在 Azure 監視器中使用計量和記錄的警示。](../platform/alerts-overview.md)

