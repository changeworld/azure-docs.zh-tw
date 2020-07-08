---
title: 在 Azure 監視器中收集並分析效能計數器 | Microsoft Docs
description: Azure 監視器會收集效能計數器以分析 Windows 和 Linux 代理程式的效能。  本文說明如何設定 Windows 和 Linux 代理程式的效能計數器收集、儲存在工作區中的相關詳細資料，以及如何在 Azure 入口網站中分析這些資料。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 49f944aa98bf0bf8090b10d2feeb50af4a2d42b2
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85955483"
---
# <a name="windows-and-linux-performance-data-sources-in-azure-monitor"></a>Azure 監視器中的 Windows 和 Linux 效能資料來源
Windows 和 Linux 的效能計數器可讓您深入了解硬體元件、作業系統及應用程式的效能。  Azure 監視器可收集效能計數器，以頻繁間隔進行接近即時 (NRT) 的分析，並彙總較長期分析和報告所需的效能資料。

![效能計數器](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>設定效能計數器
從 [[進階]\ 設定的 \[資料\] 功能表](agent-data-sources.md#configuring-data-sources)設定效能計數器。

當您第一次為新的工作區設定 Windows 或 Linux 效能計數器時，系統會提供選項，讓您快速建立數個常用的計數器。  這些計數器旁邊皆會列出核取方塊。  確認已核取所有想一開始就建立的計數器，然後按一下 **[Add the selected performance counters]** \ (加入選取的效能計數器) 。

對於 Windows 效能計數器，您可以選擇每個效能計數器的特定執行個體。 對於 Linux 效能計數器，您選擇的每個計數器的執行個體會套用至父計數器的所有子計數器。 下表顯示可用於 Linux 和 Windows 效能計數器的常見執行個體。

| 執行個體名稱 | Description |
| --- | --- |
| \_總計 |所有執行個體的總數 |
| \* |所有執行個體 |
| (/&#124;/var) |比對名為 / 或 /var 的執行個體 |

### <a name="windows-performance-counters"></a>Windows 效能計數器

![設定 Windows 效能計數器](media/data-sources-performance-counters/configure-windows.png)

請遵循此程序以加入要收集的新 Windows 效能計數器。

1. 在文字方塊中輸入計數器名稱，格式為 *object(instance)\counter*。  開始輸入時，您就會看到符合的常用計數器清單。  您可以從清單中選取計數器，或自行輸入。  您也可以指定 *object\counter*，以傳回特定計數器的所有執行個體。  

    從具名執行個體收集 SQL Server 效能計數器時，所有具名執行個體的計數器會以 MSSQL$** 作為開頭，後面接著執行個體的名稱。  例如，若要從具名 SQL 執行個體 INST2 的資料庫效能物件收集所有資料庫的「記錄快取命中率」計數器，請指定 `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`。

2. 按一下 **+** 或按**enter** ，將計數器新增至清單。
3. 新增計數器時，它會以 10 秒作為 [取樣間隔時間]**** 的預設值。  如果您想要降低所收集之效能資料的儲存需求，可以將此值變更為最多 1800 秒 (30 分鐘)。
4. 加入所要的計數器後，請按一下畫面頂端的 [儲存] **** 按鈕以儲存設定。

### <a name="linux-performance-counters"></a>Linux 效能計數器

![設定 Linux 效能計數器](media/data-sources-performance-counters/configure-linux.png)

請遵循此程序以加入要收集的新 Linux 效能計數器。

1. 根據預設，所有組態變更都會自動發送給所有代理程式。  若是 Linux 代理程式，組態檔會傳送給 Fluentd 資料收集器。  如果您想在每個 Linux 代理程式上手動修改這個檔案，請取消核取 [將下列設定套用至我的 Linux 機器]** 方塊，並遵循下列指引。
2. 在文字方塊中輸入計數器名稱，格式為 *object(instance)\counter*。  開始輸入時，您就會看到符合的常用計數器清單。  您可以從清單中選取計數器，或自行輸入。  
3. 按一下 **+** 或按下**enter** ，將計數器新增至物件的其他計數器清單。
4. 物件的所有計數器都會使用相同的 [取樣間隔時間] ****。  預設值是 10 秒。  如果您想要降低所收集之效能資料的儲存空間需求，請將此值變更為最多 1800 秒 (30 分鐘)。
5. 加入所要的計數器後，請按一下畫面頂端的 [儲存] **** 按鈕以儲存設定。

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>在組態檔中設定 Linux 效能計數器
除了使用 Azure 入口網站設定 Linux 效能計數器，您還可以選擇在 Linux 代理程式上編輯組態檔。  要收集的效能計量是由 **/etc/opt/microsoft/omsagent/ \<workspace id\> /conf/omsagent.conf**中的設定所控制。

要收集之效能計量的每個物件或類別都應該當成單一 `<source>` 元素定義於組態檔中。 語法遵循下面的模式。

```xml
<source>
    type oms_omi  
    object_name "Processor"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```


下表說明此元素中的參數。

| 參數 | 說明 |
|:--|:--|
| object\_name | 收集的物件名稱。 |
| instance\_regex |  此「規則運算式」** 定義要收集的執行個體。 值 `.*` 指定所有執行個體。 若只要收集 \_Total 執行個體的處理器計量，您可以指定 `_Total`。 若只要收集 crond 或 sshd 執行個體的程序計量，您可以指定： `(crond\|sshd)`。 |
| counter\_name\_regex | 此「規則運算式」** 定義要收集的計數器 (針對物件)。 若要收集物件的所有計數器，請指定︰ `.*`。 若只要收集記憶體物件的交換空間計數器，舉例來說，您可以指定︰`.+Swap.+` |
| interval | 物件計數器的收集頻率。 |


下表列出您可以在組態檔中指定的物件和計數器。  還有其他計數器適用於特定應用程式，如[在 Azure 監視器中收集 Linux 應用程式的效能計數器](data-sources-linux-applications.md)中所述。

| 物件名稱 | 計數器名稱 |
|:--|:--|
| 邏輯磁碟 | % Free Inodes |
| 邏輯磁碟 | % Free Space |
| 邏輯磁碟 | % Used Inodes |
| 邏輯磁碟 | % Used Space |
| 邏輯磁碟 | Disk Read Bytes/sec |
| 邏輯磁碟 | Disk Reads/sec |
| 邏輯磁碟 | Disk Transfers/sec |
| 邏輯磁碟 | Disk Write Bytes/sec |
| 邏輯磁碟 | Disk Writes/sec |
| 邏輯磁碟 | Free Megabytes |
| 邏輯磁碟 | Logical Disk Bytes/sec |
| Memory | % Available Memory |
| Memory | % Available Swap Space |
| Memory | % Used Memory |
| Memory | % Used Swap Space |
| Memory | Available MBytes Memory |
| Memory | Available MBytes Swap |
| Memory | Page Reads/sec |
| Memory | Page Writes/sec |
| Memory | Pages/sec |
| Memory | Used MBytes Swap Space |
| Memory | Used Memory MBytes |
| 網路 | Total Bytes Transmitted |
| 網路 | Total Bytes Received |
| 網路 | Total Bytes |
| 網路 | Total Packets Transmitted |
| 網路 | Total Packets Received |
| 網路 | Total Rx Errors |
| 網路 | Total Tx Errors |
| 網路 | Total Collisions |
| Physical Disk | Avg.Disk sec/Read |
| Physical Disk | Avg.Disk sec/Transfer |
| Physical Disk | Avg.Disk sec/Write |
| Physical Disk | Physical Disk Bytes/sec |
| 程序 | Pct Privileged Time |
| 程序 | Pct User Time |
| 程序 | Used Memory kBytes |
| 程序 | Virtual Shared Memory |
| 處理器 | % DPC Time |
| 處理器 | % Idle Time |
| 處理器 | % Interrupt Time |
| 處理器 | % IO Wait Time |
| 處理器 | % Nice Time |
| 處理器 | % Privileged Time |
| 處理器 | % Processor Time |
| 處理器 | % User Time |
| 系統 | Free Physical Memory |
| 系統 | Free Space in Paging Files |
| 系統 | Free Virtual Memory |
| 系統 | 處理程序 |
| 系統 | Size Stored In Paging Files |
| 系統 | Uptime |
| 系統 | 使用者 |


以下是效能計量的預設組態。

```xml
<source>
    type oms_omi
    object_name "Physical Disk"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Logical Disk"
    instance_regex ".*
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Processor"
    instance_regex ".*
    counter_name_regex ".*"
    interval 30s
</source>

<source>
    type oms_omi
    object_name "Memory"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```

## <a name="data-collection"></a>資料收集
只要代理程式有安裝相關計數器，Azure 監視器就會依照其指定的取樣間隔時間，收集全部代理程式上所有指定的效能計數器。  資料不會匯總，而且原始資料會在 log analytics 工作區所指定的持續期間內，于所有記錄查詢檢視中提供。

## <a name="performance-record-properties"></a>效能記錄屬性
效能記錄都具有 **Perf** 類型以及下表中的屬性。

| 屬性 | 描述 |
|:--- |:--- |
| 電腦 |收集事件的來源電腦。 |
| CounterName |效能計數器的名稱 |
| CounterPath |[表單 \\ \\ \<Computer> \\ 物件（實例）] 計數器中計數器的完整路徑 \\ 。 |
| CounterValue |計數器的數值。 |
| InstanceName |事件執行個體的名稱。  如果沒有執行個體即為空白。 |
| ObjectName |效能物件的名稱 |
| SourceSystem |收集資料的來源代理程式類型。 <br><br>OpsManager – Windows 代理程式，直接連接或 SCOM <br> Linux – 所有的 Linux 代理程式  <br> AzureStorage – Azure 診斷 |
| TimeGenerated |資料取樣的日期和時間。 |

## <a name="sizing-estimates"></a>大小估計值
 特定計數器集合的約略估計是依照每個執行個體 10 秒間隔，每天約 1 MB。  您可以使用下列公式，評估特定計數器的儲存需求。

> 1 MB x （計數器數目） x （代理程式數目） x （實例數目）

## <a name="log-queries-with-performance-records"></a>記錄查詢與效能記錄
下表提供擷取效能記錄的不同記錄查詢範例。

| 查詢 | 描述 |
|:--- |:--- |
| Perf |所有效能資料 |
| Perf &#124; where Computer == "MyComputer" |來自特定電腦的所有效能資料 |
| Perf &#124; where CounterName == "Current Disk Queue Length" |來自特定計數器的所有效能資料 |
| 效能 &#124;，其中 ObjectName = = "Processor" and CounterName = = "% Processor Time" 和 InstanceName = = "_Total" &#124; 依電腦匯總 AVGCPU = avg （CounterValue） |所有電腦的平均 CPU 使用率 |
| 效能 &#124;，其中 CounterName = = "% Processor Time" &#124; 摘要 AggregatedValue = 最大值（CounterValue）（依電腦） |所有電腦的最大 CPU 使用率 |
| 效能 &#124;，其中 ObjectName = = "LogicalDisk" 和 CounterName = = "目前磁片佇列長度" 和 Computer = = "MyComputerName" &#124; 摘要 AggregatedValue = avg （CounterValue） by InstanceName |指定電腦之所有執行個體的平均目前磁碟佇列長度 |
| 效能 &#124;，其中 CounterName = = "磁片傳輸/秒" &#124; 總結 AggregatedValue = 每台電腦的百分位數（CounterValue，95） |所有電腦之第 95 個百分位數的 Disk Transfers/Sec |
| Perf &#124; where CounterName == "% Processor Time" and InstanceName == "_Total" &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), Computer |所有電腦每小時平均 CPU 使用率 |
| Perf &#124; where Computer == "MyComputer" and CounterName startswith_cs "%" and InstanceName == "_Total" &#124; summarize AggregatedValue = percentile(CounterValue, 70) by bin(TimeGenerated, 1h), CounterName | 特定電腦每小時每個 % 百分比計數器的 70 個百分位數 |
| Perf &#124; where CounterName == "% Processor Time" and InstanceName == "_Total" and Computer == "MyComputer" &#124; summarize ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg(CounterValue), ["percentile75(CounterValue)"] = percentile(CounterValue, 75), ["max(CounterValue)"] = max(CounterValue) by bin(TimeGenerated, 1h), Computer |特定電腦每小時平均、最小、最大和 75 個百分位數的 CPU 使用量 |
| Perf &#124; where ObjectName == "MSSQL$INST2:Databases" and InstanceName == "master" | 資料庫效能物件中的所有效能資料適用於來自具名 SQL Server 執行個體 INST2 的 master 資料庫。  




## <a name="next-steps"></a>後續步驟
* [從 Linux 應用程式收集效能計數器](data-sources-linux-applications.md)，包括 MySQL 和 Apache HTTP Server。
* 了解[記錄查詢](../log-query/log-query-overview.md)，以分析從資料來源和解決方案收集到的資料。  
* 將收集的資料匯出至 [Power BI](powerbi.md) 以進行其他視覺效果和分析。
