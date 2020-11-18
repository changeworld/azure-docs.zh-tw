---
title: 適用於 VM 的 Azure 監視器的警示
description: 說明如何從適用於 VM 的 Azure 監視器所收集的效能資料建立警示規則。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: f582f0dc7547a607351fcfc4ff9d39e8c5a077df
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686172"
---
# <a name="how-to-create-alerts-from-azure-monitor-for-vms"></a>如何從適用於 VM 的 Azure 監視器建立警示
[Azure 監視器中的警示](../platform/alerts-overview.md) 會主動通知您監視資料中有興趣的資料和模式。 適用於 VM 的 Azure 監視器不包含預先設定的警示規則，但您可以根據它所收集的資料來建立自己的警示規則。 本文提供建立警示規則的指引，包括一組範例查詢。

> [!IMPORTANT]
> 本文所述的警示是根據適用於 VM 的 Azure 監視器所收集資料的記錄查詢。 這與 [VM 來賓健康情況 Azure 監視器](vminsights-health-overview.md) 所建立的警示不同，這是目前處於公開預覽狀態的功能。 因為此功能接近正式運作，所以會合並警示的指引。


## <a name="alert-rule-types"></a>警示規則類型
根據用來建立警示的資料，Azure 監視器具有 [不同類型的警示規則](../platform/alerts-overview.md#what-you-can-alert-on) 。 適用於 VM 的 Azure 監視器收集的所有資料都會儲存在支援 [記錄警示](../platform/alerts-log.md)的 Azure 監視器記錄中。 您目前無法使用計量 [警示](../platform/alerts-log.md) 搭配從適用於 VM 的 Azure 監視器收集的效能資料，因為資料不會收集到 Azure 監視器計量中。 若要收集計量警示的資料，請安裝適用于 Windows Vm 的 [診斷擴充](../platform/diagnostics-extension-overview.md) 功能或適用于 Linux Vm 的 [Telegraf 代理程式](../platform/collect-custom-metrics-linux-telegraf.md) ，以將效能資料收集到計量中。

Azure 監視器有兩種類型的記錄警示：

- 當查詢至少傳回指定的記錄數目時，[結果數警示](../platform/alerts-unified-log.md#count-of-the-results-table-rows)會建立單一警示。 這些適用于 [Log Analytics 代理程式](../platform/log-analytics-agent.md) 所收集的非數值資料，例如 Windows 和 Syslog 事件，或可跨多部電腦分析效能趨勢。
- [計量測量警示](../platform/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) 會針對查詢中的每筆記錄建立個別的警示，此查詢的值超過警示規則中所定義的臨界值。 這些警示規則適用于適用於 VM 的 Azure 監視器收集的效能資料，因為它們可以為每部電腦建立個別的警示。


## <a name="alert-rule-walkthrough"></a>警示規則逐步解說
本節將逐步解說如何使用適用於 VM 的 Azure 監視器的效能資料來建立計量測量警示規則。 您可以使用此基本程式搭配各種記錄查詢來發出不同效能計數器的警示。

首先，請遵循 [使用 Azure 監視器建立、查看和記錄管理警示](../platform/alerts-log.md)中的程式，建立新的警示規則。 針對 **資源**，請選取 Azure 監視器 vm 在您訂用帳戶中使用的 Log Analytics 工作區。 因為記錄警示規則的目標資源一律是 Log Analytics 工作區，所以記錄查詢必須包含特定虛擬機器或虛擬機器擴展集的任何篩選準則。 

針對警示規則的 **條件** ，請使用 [下一節](#sample-alert-queries) 中的其中一個查詢作為 **搜尋查詢**。 查詢必須傳回名為 *AggregatedValue* 的數值屬性。 它應該會依電腦摘要資料，讓您可以為每個超過閾值的虛擬機器建立個別的警示。

在 **警示邏輯** 中，選取 [ **計量測量** ]，然後提供 **臨界值**。 在 [ **觸發程式警示依據**] 中，指定在建立警示之前必須超過閾值的次數。 例如，您可能不在意處理器是否超過臨界值一次，然後恢復正常，但如果它持續超過多個連續度量的閾值，您就會在意。

根據區段進行 **評估** 會定義查詢的執行頻率，以及查詢的時間範圍。 在如下所示的範例中，查詢會每隔15分鐘執行一次，並評估過去15分鐘所收集到的效能值。


![計量測量警示規則](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>範例警示查詢
下列查詢可搭配使用適用於 VM 的 Azure 監視器所收集之效能資料的計量測量警示規則使用。 每一部電腦都會依電腦摘要資料，以便為值超過閾值的每部電腦建立警示。

### <a name="cpu-utilization"></a>CPU 使用率

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>可用的記憶體（MB）

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>可用記憶體（百分比）

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>使用的邏輯磁片-每部電腦上的所有磁片

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>使用的邏輯磁片-個別磁片

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>邏輯磁片 IOPS

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>邏輯磁片資料速率

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>已接收的網路介面位元組-所有介面

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>網路介面接收的位元組數-個別介面

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>網路介面傳送位元組數-所有介面

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>網路介面傳送位元組數-個別介面

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>虛擬機器擴展集
使用您的訂用帳戶識別碼、資源群組和虛擬機器擴展集名稱來修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>特定虛擬機器
使用您的訂用帳戶識別碼、資源群組和 VM 名稱來修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>訂用帳戶中所有計算資源的 CPU 使用率
使用您的訂用帳戶 ID 進行修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>資源群組中所有計算資源的 CPU 使用率
使用您的訂用帳戶識別碼和資源群組來修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure 監視器中的警示](../platform/alerts-overview.md)。
- 深入瞭解如何 [使用適用於 VM 的 Azure 監視器中的資料進行記錄查詢](vminsights-log-search.md)。
