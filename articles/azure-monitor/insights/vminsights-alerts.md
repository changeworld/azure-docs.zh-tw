---
title: 來自 Azure 監視器的 VM 警報
description: 介紹如何從 Azure 監視器為 VM 收集的效能資料創建警報規則。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/23/2020
ms.openlocfilehash: 987537d8497b3d8f2728941334d8328320ec6997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289598"
---
# <a name="how-to-create-alerts-from-azure-monitor-for-vms"></a>如何從 Azure 監視器為 VM 創建警報
[Azure 監視器中的警報](../platform/alerts-overview.md)會主動通知您監視資料中有趣的資料和模式。 VM 的 Azure 監視器不包括預先配置的警報規則，但您可以根據它收集的資料創建自己的警報規則。 本文提供有關創建警報規則（包括一組依例查詢）的指導。


## <a name="alert-rule-types"></a>警報規則類型
Azure 監視器具有不同類型的[警報規則](../platform/alerts-overview.md#what-you-can-alert-on)，具體取決於用於創建警報的資料。 Azure 監視器為 VM 收集的所有資料都存儲在支援[日誌警報的](../platform/alerts-log.md)Azure 監視器日誌中。 當前不能將[指標警報](../platform/alerts-log.md)與從 Azure 監視器收集的 VM 效能資料一起使用，因為資料不會收集到 Azure 監視器指標中。 要收集指標警報的資料，請安裝 Windows VM 的[診斷擴展](../platform/diagnostics-extension-overview.md)或 Linux VM 的[Telegraf 代理](../platform/collect-custom-metrics-linux-telegraf.md)，以便將效能資料收集到指標中。

Azure 監視器中有兩種類型的日誌警報：

- 當查詢返回至少指定數量的記錄時，[結果警報數](../platform/alerts-unified-log.md#number-of-results-alert-rules)創建單個警報。 這些是日誌分析代理收集的非數位資料以及[日誌分析代理](../platform/log-analytics-agent.md)收集的 Windows 和 Syslog 事件或分析多台電腦的性能趨勢的理想選擇。
- [指標度量警報](../platform/alerts-unified-log.md#metric-measurement-alert-rules)為查詢中的每個記錄創建單獨的警報，該記錄的值超過警報規則中定義的閾值。 這些警報規則非常適合 Azure 監視器為 VM 收集的效能資料，因為它們可以為每台電腦創建單獨的警報。


## <a name="alert-rule-walkthrough"></a>警報規則演練
本節介紹使用來自 VM Azure 監視器的效能資料創建指標度量警報規則。 您可以將此基本過程與各種日誌查詢一起用於在不同的效能計數器上發出警報。

首先，[在使用 Azure 監視器 中創建、查看和管理日誌警報](../platform/alerts-log.md)的過程之後創建新的警報規則。 對於**資源**，選擇 Azure 監視器 VM 在訂閱中使用的日誌分析工作區。 由於日誌警報規則的目標資源始終是日誌分析工作區，因此日誌查詢必須包含特定虛擬機器或虛擬機器縮放集的任何篩選器。 

對於警報規則**的條件**，請使用[以下部分](#sample-alert-queries)中的查詢之一作為**搜索查詢**。 查詢必須返回名為*聚合值*的數位屬性。 它應按電腦匯總資料，以便可以為每個超過閾值的虛擬機器創建單獨的警報。

在 **"警報"邏輯**中，選擇 **"指標"度量**，然後提供**閾值**。 在 **"基於觸發警報"** 中，指定在創建警報之前必須超出閾值的次數。 例如，您可能不關心處理器是否超過閾值一次，然後恢復正常，但您確實在多次連續測量中是否繼續超過閾值。

**基於"評估"** 部分定義查詢的運行頻率和查詢的時間視窗。 在下面的示例中，查詢將每 15 分鐘運行一次，並計算在過去 15 分鐘內收集的性能值。


![指標測量警報規則](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>示例警報查詢
使用 Azure 監視器為 VM 收集的效能資料，可以使用以下查詢與指標度量警報規則一起使用。 每個電腦匯總資料，以便為每個值超過閾值的每台電腦創建警報。

### <a name="cpu-utilization"></a>CPU 使用率

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>可用記憶體（MB）

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>可用記憶體百分比

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>使用邏輯磁片 - 每台電腦上的所有磁片

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>使用的邏輯磁片 - 單個磁片

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

### <a name="network-interfaces-bytes-received---all-interfaces"></a>接收的網路介面位元組 - 所有介面

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>接收的網路介面位元組 - 單個介面

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>發送的網路介面位元組 - 所有介面

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>發送的網路介面位元組 - 單個介面

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>虛擬機器擴展集
使用訂閱 ID、資源組和虛擬機器縮放集名稱進行修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>特定虛擬機器
使用訂閱 ID、資源組和 VM 名稱進行修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>訂閱中所有計算資源的 CPU 利用率
使用訂閱 ID 進行修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>資源組中所有計算資源的 CPU 利用率
使用訂閱 ID 和資源組進行修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>後續步驟

- 瞭解有關[Azure 監視器 中的警報](../platform/alerts-overview.md)的更多詳細資訊。
- 詳細瞭解使用[Azure 監視器中的資料為 VM 的日誌查詢](vminsights-log-search.md)。
