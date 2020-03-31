---
title: 使用 Azure 監視器監視 Azure 虛擬機器
description: 介紹如何使用 Azure 監視器從 Azure 中的虛擬機器收集和分析監視資料。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 2cb53d0c88d8c29da2bd8bf52d6536555d56c76e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283934"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>使用 Azure 監視器監視 Azure 虛擬機器
本文介紹如何使用 Azure 監視器收集和分析 Azure 虛擬機器的監視資料以維護其運行狀況。 虛擬機器可以像[任何其他 Azure 資源](monitor-azure-resource.md)一樣使用 Azure 監視器監視可用性和性能，但它們與其他資源是唯一的，因為您還需要監視客體作業系統和系統以及其中運行的工作負載。 

> [!NOTE]
> 本文全面概述了在 Azure 監視器中監視虛擬機器的概念和選項。 要快速監視虛擬機器而不關注基本概念，請參閱[快速入門：使用 Azure 監視器監視 Azure 虛擬機器](../learn/quick-monitor-azure-vm.md)。


## <a name="differences-from-other-azure-resources"></a>與其他 Azure 資源的差異
[使用 Azure 監視器監視 Azure 資源](monitor-azure-resource.md)將介紹 Azure 資源生成的監視資料，以及如何使用 Azure 監視器的功能來分析和警報此資料。 您可以從 Azure 虛擬機器收集相同的監視資料並處理具有以下差異的資料：

- [平臺指標](../platform/data-platform-metrics.md)自動為虛擬機器收集，但僅適用于[虛擬機器主機](#monitoring-data)。 您需要一個代理來從客體作業系統收集效能資料。 
- 虛擬機器不生成[資源日誌](../platform/platform-logs-overview.md)，這些日誌提供對在 Azure 資源中執行的操作的見解。 您可以使用代理從客體作業系統收集日誌資料。
- 可以為虛擬機器創建[診斷設置](../platform/diagnostic-settings.md)，將平臺指標發送到其他目標（如存儲和事件中心），但不能在 Azure 門戶中配置這些診斷設置。 

## <a name="monitoring-data"></a>監視資料
Azure 中的虛擬機器生成[日誌](../platform/data-platform-logs.md)和[指標](../platform/data-platform-metrics.md)，如下所示。

![總覽](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>虛擬機器主機
Azure 中的虛擬機器為虛擬機器主機生成以下資料，與[監視資料](monitor-azure-resource.md#monitoring-data)中描述的其他 Azure 資源相同。

- [平臺指標](../platform/data-platform-metrics.md)- 定期自動收集的數位值，並描述特定時間資源的某些方面。 為虛擬機器主機收集平臺指標，但您需要診斷擴展來收集客體作業系統的指標。
- [活動日誌](../platform/platform-logs-overview.md)- 從外部（管理平面）提供對訂閱中每個 Azure 資源的操作的見解。 對於虛擬機器，這包括啟動時間以及任何配置更改等資訊。


### <a name="guest-operating-system"></a>客體作業系統
要從虛擬機器的客體作業系統收集資料，需要代理，該代理在每個虛擬機器上本地運行並將資料發送到 Azure 監視器。 Azure 監視器可以使用多個代理，每個代理都收集不同的資料並將資料寫入不同的位置。 在[Azure 監視器代理的概述](../platform/agents-overview.md)中詳細比較不同的代理。 

- [日誌分析代理](../platform/agents-overview.md#log-analytics-agent)- 可用於 Azure、其他雲環境和本地中的虛擬機器。 將資料收集到 Azure 監視器日誌。 支援針對 VM 的 Azure 監視器和監視解決方案。 這是用於系統中心操作管理器的相同代理。
- [依賴項代理](../platform/agents-overview.md#dependency-agent)- 收集有關在虛擬機器上運行的進程及其依賴項的資料。 依賴日誌分析代理將資料傳輸到 Azure，並支援針對 VM 的 Azure 監視器、服務映射和有線資料 2.0 解決方案。
- [Azure 診斷擴展](../platform/agents-overview.md#azure-diagnostics-extension)- 僅適用于 Azure 監視器虛擬機器。 可以將資料收集到多個位置，但主要用於將來賓效能資料收集到 Windows 虛擬機器的 Azure 監視器指標中。
- [Telegraf 代理](../platform/collect-custom-metrics-linux-telegraf.md)- 從 Linux VM 收集效能資料到 Azure 監視器指標中。


## <a name="configuration-requirements"></a>組態需求
要啟用 Azure 監視器的所有功能以監視虛擬機器，需要從虛擬機器主機和客體作業系統將監視資料收集到[Azure 監視器指標](../platform/data-platform-logs.md)和[Azure 監視器日誌](../platform/data-platform-logs.md)。 下表列出了啟用此集合必須執行的配置。 您可以選擇不執行所有這些步驟，具體取決於您的特定要求。

| 設定步驟 | 已完成的操作 | 啟用功能 |
|:---|:---|:---|
| 無組態 | - 收集到指標的主機平臺指標。<br>- 收集活動日誌。 | - 主機的指標資源管理器。<br>- 主機的指標警報。<br>- 活動日誌警報。 |
| [啟用適用於 VM 的 Azure 監視器](#enable-azure-monitor-for-vms) | - 已安裝日誌分析代理。<br>- 已安裝依賴項代理。<br>- 將來賓效能資料收集到日誌。<br>- 收集到日誌的進程和依賴項詳細資訊。 | - 客戶績效資料的性能圖表和活頁簿。<br>- 記錄來賓效能資料的查詢。<br>- 記錄來賓效能資料的警報。<br>- 依賴項映射。 |
| [安裝診斷擴展和遠端廣播代理](#enable-diagnostics-extension-and-telegraf-agent) | - 收集到指標的來賓效能資料。 | - 供客人使用指標的資源管理器。<br>- 針對來賓的指標警報。  |
| [配置日誌分析工作區](#configure-log-analytics-workspace) | - 從客人那裡收集的活動。 | - 記錄來賓事件的查詢。<br>- 記錄來賓事件的警報。 |
| [為虛擬機器創建診斷設置](#collect-platform-metrics-and-activity-log) | - 收集到日誌的平臺指標。<br>- 收集到日誌的活動日誌。 | - 主機指標的 Loq 查詢。<br>- 記錄主機指標的警報。<br>- 記錄活動日誌的查詢。

以下各節將介紹每個配置步驟。

### <a name="enable-azure-monitor-for-vms"></a>啟用適用於 VM 的 Azure 監視器
[VM 的 Azure 監視器](vminsights-overview.md)是 Azure 監視器中的[見解](insights-overview.md)，是監視 Azure 監視器中虛擬機器的主要工具。 它提供以下附加值，而不是標準 Azure 監視器功能。

- 簡化日誌分析代理和依賴項代理的載入，以便監視虛擬機器客體作業系統和工作負載。 
- 預定義的趨勢性能圖表和活頁簿，允許您從虛擬機器的客體作業系統分析核心性能指標。
- 依賴項映射，顯示在每個虛擬機器上運行的進程，以及與其他電腦和外部源的互連元件。

![適用於 VM 的 Azure 監視器](media/monitor-vm-azure/vminsights-01.png)

![適用於 VM 的 Azure 監視器](media/monitor-vm-azure/vminsights-02.png)


從 Azure 門戶的虛擬機器功能表中的 **"見解"** 選項為 VM 啟用 Azure 監視器。 有關詳細資訊和其他配置方法，請參閱[啟用 VM 的 Azure 監視器概述](vminsights-enable-overview.md)。

![啟用適用於 VM 的 Azure 監視器](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>配置日誌分析工作區
Azure 監視器用於 VM 的日誌分析代理將資料發送到[日誌分析工作區](../platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured)。 您可以通過配置日誌分析工作區，從代理中收集其他效能資料、事件和其他監視資料。 它只需要配置一次，因為任何連接到工作區的代理都將自動下載配置，並立即開始收集定義的資料。 

您可以通過從 **"開始"** 中選擇**工作區配置**，直接從 AZURE 監視器訪問工作區的配置。 按一下工作區名稱以打開其功能表。

![工作區設定](media/monitor-vm-azure/workspace-configuration.png)

從工作區功能表中選擇 **"高級設置****"，然後選擇"資料**"以配置資料來源。 對於 Windows 代理，選擇**Windows 事件日誌**並添加常見事件日誌，如*系統和**應用程式*。 對於 Linux 代理，選擇**Syslog**並添加通用設施，如*kern*和*守護進程*。 有關可用資料來源的清單以及有關配置資料來源的詳細資訊，請參閱[Azure 監視器中的代理資料來源](../platform/agent-data-sources.md)。 

![配置事件](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> 您可以將效能計數器配置為從工作區配置中收集，但對於 Azure 監視器為 VM 收集的效能計數器，這可能是多餘的。 VM 的 Azure 監視器以每分鐘一次的頻率收集最常見的計數器集。 僅當要收集 Azure 監視器尚未收集的 VM 尚未收集的計數器或使用效能資料的常設查詢時，才配置工作區要收集的效能計數器。


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>啟用診斷擴展和遠端格拉夫代理
VM 的 Azure 監視器基於日誌分析代理，該代理將資料收集到日誌分析工作區中。 這支援[Azure 監視器的多個功能](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)，如[日誌查詢](../log-query/log-query-overview.md)、[日誌警報](../platform/alerts-log.md)和[活頁簿](../platform/workbooks-overview.md)。 [診斷擴展](../platform/diagnostics-extension-overview.md)從 Windows 虛擬機器的客體作業系統收集效能資料到 Azure 存儲，並可以選擇將效能資料發送到[Azure 監視器指標](../platform/data-platform-metrics.md)。 對於 Linux 虛擬機器，[需要 Telegraf 代理](../platform/collect-custom-metrics-linux-telegraf.md)將資料發送到 Azure 指標。  這支援 Azure 監視器的其他功能，如[指標資源管理器](../platform/metrics-getting-started.md)和[指標警報](../platform/alerts-metric.md)。 還可以將診斷擴展配置為使用 Azure 事件中心在 Azure 監視器之外發送事件和效能資料。

從 VM 功能表中的 **"診斷"設置**選項在 Azure 門戶中安裝單個 Windows 虛擬機器的診斷擴展。 在 **"接收器"** 選項卡中選擇啟用**Azure 監視器**的選項。要從多個虛擬機器的範本或命令列啟用擴展，請參閱[安裝和配置](../platform/diagnostics-extension-overview.md#installation-and-configuration)。 與 Log Analytics 代理不同，要收集的資料在每個虛擬機器上的擴展的配置中定義。

![診斷設定](media/monitor-vm-azure/diagnostic-setting.png)

有關在 Linux 虛擬機器上配置 Telegraf 代理的詳細資訊，請參閱[安裝和配置 Telegraf。](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) **診斷設置**功能表選項可用於 Linux，但它僅允許您將資料發送到 Azure 存儲。

### <a name="collect-platform-metrics-and-activity-log"></a>收集平臺指標和活動日誌
您可以查看為 Azure 門戶中的每個虛擬機器主機收集的平臺指標和活動日誌。 將此資料收集到與 VM Azure 監視器相同的日誌分析工作區中，以便使用為虛擬機器收集的其他監視資料進行分析。 此集合配置了[診斷設置](../platform/diagnostic-settings.md)。 使用[訂閱的診斷設置](../platform/diagnostic-settings.md#create-diagnostic-settings-in-azure-portal)收集活動日誌。

使用虛擬機器的診斷設置收集平臺指標。 與其他 Azure 資源不同，您不能在 Azure 門戶中為虛擬機器創建診斷設置，但必須使用[另一種方法](../platform/diagnostic-settings.md#create-diagnostic-settings-using-powershell)。 以下示例演示如何使用 PowerShell 和 CLI 收集虛擬機器的指標。

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
為虛擬機器配置監視資料收集後，在 Azure 門戶中具有多個訪問選項：

- 使用**Azure 監視器**功能表訪問來自所有受監視資源的資料。 
- 使用 VM 的 Azure 監視器大規模監視虛擬機器集。
- 從 Azure 門戶中的功能表分析單個虛擬機器的資料。 下表列出了監視虛擬機器功能表的不同選項。

![Azure 入口網站中的監視](media/monitor-vm-azure/monitor-menu.png)

| 功能表選項 | 描述 |
|:---|:---|
| 總覽 | 顯示虛擬機器主機[的平臺指標](../platform/data-platform-metrics.md)。 按一下圖形以在[指標資源管理器](../platform/metrics-getting-started.md)中使用此資料。 |
| 活動記錄檔 | 為當前虛擬機器篩選[的活動日誌](../platform/activity-log-view.md)條目。 |
| 深入解析 | 打開[Azure 監視器，顯示](../insights/vminsights-overview.md)已選擇的當前虛擬機器的映射。 |
| 警示 | 查看當前虛擬機器的[警報](../platform/alerts-overview.md)。  |
| 計量 | 打開[指標資源管理器](../platform/metrics-getting-started.md)，範圍設置為當前虛擬機器。 |
| 診斷設定 | 為當前虛擬機器啟用和配置[診斷擴展](../platform/diagnostics-extension-overview.md)。 |
| Advisor 建議 | [Azure 顧問](/azure/advisor/)對當前虛擬機器的建議。 |
| 記錄 | 打開[日誌分析](../log-query/log-query-overview.md#what-is-log-analytics)，將[範圍](../log-query/scope.md)設置為當前虛擬機器。 |
| 連線監視 | 打開[網路觀察程式連接監視器](../../network-watcher/connection-monitor-preview.md)，以監視當前虛擬機器和其他虛擬機器之間的連接。 |


## <a name="analyzing-metric-data"></a>分析指標資料
您可以通過從虛擬機器功能表中打開**指標**來分析使用指標資源管理器的指標。 有關使用此工具的詳細資訊[，請參閱使用 Azure 指標資源管理器入門](../platform/metrics-getting-started.md)。 

虛擬機器使用兩個命名空間用於指標：

| 命名空間 | 描述 |
|:---|:---|
| 虛擬機器主機 | 自動收集所有 Azure 虛擬機器的主機指標。 微軟的詳細指標清單[。](../platform/metrics-supported.md#microsoftcomputevirtualmachines) |
| 虛擬機器來賓 | 從虛擬機器收集的客體作業系統指標，並安裝診斷擴展並配置為發送到 Azure 監視器接收器。 |

![計量](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>分析日誌資料
Azure 虛擬機器將收集以下資料到 Azure 監視器日誌。 

VM 的 Azure 監視器支援收集寫入*InsightsMetrics*表的預定效能計數器集。 這是[Azure 監視器用於容器的相同](container-insights-overview.md)表。 

| 資料來源 | 需求 | 資料表 |
|:---|:---|:---|
| 適用於 VM 的 Azure 監視器 | 在每個虛擬機器上啟用。 | 見解指標<br>VMBundPort<br>虛擬機器<br>VMConnection<br>VMProcess<br>有關詳細資訊[，請參閱如何從 Azure 監視器查詢日誌以瞭解 VM。](vminsights-log-search.md) |
| 活動記錄檔 | 訂閱的診斷設置。 | AzureActivity |
| 主機指標 | 虛擬機器的診斷設置。 | AzureMetrics |
| 來自客體作業系統的資料來源 | 啟用日誌分析代理並配置資料來源。 | 請參閱每個資料來源的文檔。 |


> [!NOTE]
> 日誌分析代理收集的效能資料寫入*Perf*表，而 VM 的 Azure 監視器將將其收集到*見解指標*表。 這是相同的資料，但表具有不同的結構。 如果您有基於*Perf*的常設查詢，則需要重寫該查詢才能使用*InsightsMetrics*。


## <a name="alerts"></a>警示
Azure 監視器中的[警報](../platform/alerts-overview.md)會在監視資料中找到重要條件時主動通知您，並可能啟動操作，如啟動邏輯應用或調用 Webhook。 警報規則定義用於確定何時應創建警報的邏輯。 Azure 監視器收集警報規則使用的資料，但需要創建規則以在 Azure 訂閱中定義警示準則。

以下各節介紹警報規則的類型，以及何時應使用每個規則的建議。 此建議基於警報規則類型的功能和成本。 有關警報的詳細資訊定價，請參閱[Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。


### <a name="activity-log-alert-rules"></a>活動記錄警示規則
在活動日誌中創建匹配特定條件的條目時，[活動日誌警報規則](../platform/alerts-activity-log.md)將觸發。 它們沒有成本，因此，如果您所需的邏輯位於活動日誌中，它們應該是您的第一選擇。 

活動日誌警報的目標資源可以是特定虛擬機器、資源組中的所有虛擬機器或訂閱中的所有虛擬機器。

例如，如果關鍵虛擬機器停止，通過為信號名稱選擇 *"關閉虛擬機器"* 來創建警報。

![活動記錄警示](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>計量警示規則
當指標值超過閾值時，[指標警報規則](../platform/alerts-metric.md)將觸發。 可以定義特定的閾值，也可以允許 Azure 監視器根據歷史資料動態確定閾值。  盡可能將指標警報與指標資料一起使用，因為它們的成本更低，並且比日誌警報規則回應更快。 它們也是有狀態的，這意味著當指標低於閾值時，它們會自行解決。

活動日誌警報的目標資源可以是特定虛擬機器或資源組中的所有虛擬機器。

例如，要在虛擬機器的處理器超過特定值時創建警報，請使用*百分比 CPU*作為信號類型創建指標警報規則。 設置特定的閾值或允許 Azure 監視器設置動態閾值。 

![指標警報](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>記錄警示
當計畫日誌查詢的結果符合特定條件時，[日誌警報規則](../platform/alerts-log.md)將觸發。 日誌查詢警報是警報規則中成本最大、回應速度最低的警報，但它們可以訪問最多樣化的資料，並且可以執行其他警報規則無法執行的複雜邏輯。 

日誌查詢的目標資源是日誌分析工作區。 篩選查詢中特定電腦。

例如，要創建檢查特定資源組中的任何虛擬機器是否離線的警報，請使用以下查詢，該查詢返回最近十分鐘內丟失活動訊號的每台電腦的記錄。 使用閾值 1，如果至少有一台電腦的心跳未命中，則該閾值觸發。

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![日誌警報](media/monitor-vm-azure/log-alert-01.png)

如果要在訂閱中的任何 Windows 虛擬機器上發生過多失敗登錄，請創建警報，請使用以下查詢，該查詢返回過去一小時內每個失敗登錄事件的記錄。 使用設置為允許的失敗登錄數的閾值。 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![日誌警報](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
系統中心操作管理器 （SCOM） 提供虛擬機器上工作負載的精細監視。 有關監控平臺和不同實施策略的比較，請參閱[雲監控指南](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/)。

如果現有 SCOM 環境要繼續使用，則可以將其與 Azure 監視器集成以提供其他功能。 Azure 監視器使用的日誌分析代理與 SCOM 使用的日誌分析代理相同，因此已監視虛擬機器將資料發送到兩者。 您仍然需要將代理添加到 VM 的 Azure 監視器，並將工作區配置為按照上述指定收集其他資料，但虛擬機器可以在 SCOM 環境中繼續運行其現有管理包，而無需修改。

增強現有 SCOM 功能的 Azure 監視器功能包括：

- 使用日誌分析以互動式分析日誌和效能資料。
- 使用日誌警報定義跨多個虛擬機器的警示準則，並使用 SCOM 中的警報無法使用的長期趨勢。   

有關將現有 SCOM 管理組連接到日誌分析工作區的詳細資訊，請參閱[將操作管理器連接到 Azure 監視器](../platform/om-agents.md)。


## <a name="next-steps"></a>後續步驟

* [瞭解如何使用日誌查詢分析 Azure 監視器日誌中的資料。](../log-query/get-started-queries.md)
* [使用 Azure 監視器中的指標和日誌瞭解警報。](../platform/alerts-overview.md)

