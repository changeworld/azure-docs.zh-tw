---
title: 來自適用於 VM 的 Azure 監視器的警示
description: 描述如何從適用於 VM 的 Azure 監視器所收集的效能資料建立警示規則。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/23/2020
ms.openlocfilehash: 987537d8497b3d8f2728941334d8328320ec6997
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80289598"
---
# <a name="how-to-create-alerts-from-azure-monitor-for-vms"></a>如何從適用於 VM 的 Azure 監視器建立警示
[Azure 監視器中的警示](../platform/alerts-overview.md)會主動通知您監視資料中有興趣的資料與模式。 適用於 VM 的 Azure 監視器不包含預先設定的警示規則，但您可以根據它所收集的資料來建立自己的警示規則。 本文提供建立警示規則的指引，包括一組範例查詢。


## <a name="alert-rule-types"></a>警示規則類型
根據用來建立警示的資料，Azure 監視器具有[不同類型的警示規則](../platform/alerts-overview.md#what-you-can-alert-on)。 適用於 VM 的 Azure 監視器所收集的所有資料都會儲存在支援[記錄警示](../platform/alerts-log.md)的 Azure 監視器記錄中。 您目前無法將計量[警示](../platform/alerts-log.md)用於從適用於 VM 的 Azure 監視器收集的效能資料，因為不會將資料收集到 Azure 監視器計量中。 若要收集計量警示的資料，請安裝適用于 Windows Vm 的[診斷擴充](../platform/diagnostics-extension-overview.md)功能或 Linux Vm 的[Telegraf 代理程式](../platform/collect-custom-metrics-linux-telegraf.md)，以將效能資料收集到度量中。

Azure 監視器中的記錄警示類型有兩種：

- [結果數目警示](../platform/alerts-unified-log.md#number-of-results-alert-rules)會在查詢至少傳回指定的記錄數目時，建立單一警示。 這些適用于非數值資料，例如[Log Analytics 代理程式](../platform/log-analytics-agent.md)所收集的 Windows 和 Syslog 事件，或用來分析多部電腦上的效能趨勢。
- [計量測量警示](../platform/alerts-unified-log.md#metric-measurement-alert-rules)會針對查詢中的每筆記錄，建立一個值超過警示規則中所定義之閾值的個別警示。 這些警示規則適用于適用於 VM 的 Azure 監視器所收集的效能資料，因為它們可以為每部電腦建立個別警示。


## <a name="alert-rule-walkthrough"></a>警示規則逐步解說
本節將逐步解說如何使用適用於 VM 的 Azure 監視器的效能資料來建立計量測量警示規則。 您可以使用此基本程式搭配各種不同的記錄查詢，以針對不同的效能計數器發出警示。

請先依照[使用 Azure 監視器建立、查看和記錄管理警示](../platform/alerts-log.md)中的程式，建立新的警示規則。 針對 [**資源**]，選取您的訂用帳戶中 Azure 監視器 vm 使用的 Log Analytics 工作區。 由於記錄警示規則的目標資源一律是 Log Analytics 工作區，因此記錄查詢必須包含特定虛擬機器或虛擬機器擴展集的任何篩選。 

針對警示規則的**條件**，請使用[下一節](#sample-alert-queries)中的其中一個查詢作為**搜尋查詢**。 此查詢必須傳回名為*AggregatedValue*的數值屬性。 它應該依電腦摘要資料，讓您可以針對每個超過閾值的虛擬機器建立個別警示。

在**警示邏輯**中，選取 [**計量測量**]，然後提供 [**臨界值**]。 在 [**觸發警示依據**] 中，指定必須超過閾值多少次，才會建立警示。 例如，您可能不在意處理器是否超過臨界值一次，然後恢復正常，但如果持續超過多個連續測量的閾值，您就會在意。

**根據區段評估**的會定義執行查詢的頻率，以及查詢的時間範圍。 在下面顯示的範例中，查詢會每隔15分鐘執行一次，並評估在過去15分鐘內所收集的效能值。


![度量測量警示規則](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>範例警示查詢
下列查詢可搭配計量測量警示規則使用適用於 VM 的 Azure 監視器所收集的效能資料。 每個都會依電腦匯總資料，以便為每一部電腦建立一個值超過閾值的警示。

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

### <a name="available-memory-in-percentage"></a>可用的記憶體（百分比）

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

### <a name="network-interfaces-bytes-received---all-interfaces"></a>已接收的網路介面位元組數-所有介面

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>已接收的網路介面位元組-個別介面

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>網路介面已傳送位元組數-所有介面

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>網路介面已傳送位元組數-個別介面

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>虛擬機器擴展集
使用您的訂用帳戶識別碼、資源群組和虛擬機器擴展集名稱進行修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>特定虛擬機器
使用您的訂用帳戶識別碼、資源群組和 VM 名稱進行修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>訂用帳戶中所有計算資源的 CPU 使用率
使用您的訂用帳戶識別碼進行修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>資源群組中所有計算資源的 CPU 使用率
使用您的訂用帳戶識別碼和資源群組進行修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>後續步驟

- 深入瞭解[Azure 監視器中的警示](../platform/alerts-overview.md)。
- 深入瞭解[使用適用於 VM 的 Azure 監視器資料的記錄查詢](vminsights-log-search.md)。
