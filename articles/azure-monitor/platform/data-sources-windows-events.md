---
title: 在 Azure 監視器中使用 Log Analytics 代理程式收集 Windows 事件記錄檔資料來源
description: 說明如何透過 Azure 監視器設定收集 Windows 事件記錄，以及它們建立記錄的詳細資料。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: 109e96f862ec2f3ddf879bccba114c44aecfe3c8
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92440598"
---
# <a name="collect-windows-event-log-data-sources-with-log-analytics-agent"></a>使用 Log Analytics 代理程式收集 Windows 事件記錄檔資料來源
Windows 事件記錄是 Windows 虛擬機器上的 Log Analytics 代理程式最常見的其中一個 [資料來源](agent-data-sources.md) ，因為許多應用程式會寫入 windows 事件記錄檔。  除了指定您要監視之應用程式所建立的任何自訂記錄之外，您也可以透過標準記錄 (例如系統和應用程式) 來收集事件。

> [!IMPORTANT]
> 本文說明如何使用 [Log Analytics 代理程式](log-analytics-agent.md) （Azure 監視器所使用的其中一個代理程式）來收集 Windows 事件。 其他代理程式會收集不同的資料，並以不同的方式進行設定。 如需可用的代理程式清單和可收集的資料，請參閱 [Azure 監視器代理](agents-overview.md) 程式的總覽。

![Windows 事件](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>設定 Windows 事件記錄
從 Log Analytics 工作區的 [資料] 功能表中，設定 [ [資料] 功能表中](agent-data-sources.md#configuring-data-sources) 的 [Windows 事件記錄檔]。

Azure 監視器只會從設定中指定的 Windows 事件記錄收集事件。  您可以輸入記錄檔的名稱，然後按一下，以新增事件記錄檔 **+** 。  針對每個記錄檔，僅會收集包含所選嚴重性的事件。  請檢查您想要收集之特定記錄檔的嚴重性。  您無法提供任何其他準則來篩選事件。

輸入事件記錄檔的名稱時，Azure 監視器提供常見的事件記錄檔名稱的建議。 如果您想要新增的記錄檔未出現在清單中，您仍然可以透過輸入記錄檔的完整名稱來新增它。 您可以使用事件檢視器來尋找記錄檔的完整名稱。 在事件檢視器中，開啟記錄檔的 [內容]** 頁面，並從 [完整名稱]** 欄位複製字串。

![設定 Windows 事件](media/data-sources-windows-events/configure.png)

> [!NOTE]
> Windows 事件記錄檔中的重大事件，在 Azure 監視器記錄中會有「錯誤」的嚴重性。

## <a name="data-collection"></a>資料集合
在建立事件時，Azure 監視器會從受監視的事件記錄檔收集符合所選嚴重性的每個事件。  代理程式會在它收集的每個事件記錄檔中記錄它的位置。  如果代理程式離線一段時間，便會從上次停止的地方收集事件，即使這些事件是在代理程式離線時所建立亦同。  如果事件記錄檔是在代理程式離線時使用未收集且已遭覆寫的事件進行包裝，可能就無法收集這些事件。

>[!NOTE]
>Azure 監視器不會收集 SQL Server 從 *MSSQLSERVER* 來源用含有關鍵字 - *Classic* 或 *Audit Success* 的事件 ID 18453 以及用關鍵字 *0xa0000000000000* 所建立的稽核事件。
>

## <a name="windows-event-records-properties"></a>Windows 事件記錄屬性
Windows 事件記錄有一種 **事件** 類型，並具有下表中的屬性：

| 屬性 | 描述 |
|:--- |:--- |
| 電腦 |收集事件的來源電腦名稱。 |
| EventCategory |事件的類別。 |
| EventData |原始格式的所有事件資料。 |
| EventID |事件的編號。 |
| EventLevel |數值格式的事件嚴重性。 |
| EventLevelName |文字格式的事件嚴重性。 |
| EventLog |收集事件的來源事件記錄檔名稱。 |
| ParameterXml |XML 格式的事件參數值。 |
| ManagementGroupName |System Center Operations Manager 代理程式的管理群組名稱。  若為其他代理程式，此值為 `AOI-<workspace ID>` |
| RenderedDescription |含參數值的事件描述 |
| 來源 |事件的來源。 |
| SourceSystem |收集事件的來源代理程式類型。 <br> OpsManager - Windows 代理程式，直接連接或由 Operations Manager 管理 <br> Linux – 所有的 Linux 代理程式  <br> AzureStorage – Azure 診斷 |
| TimeGenerated |在 Windows 中建立事件的日期和時間。 |
| UserName |記錄此事件之帳戶的使用者名稱。 |

## <a name="log-queries-with-windows-events"></a>使用 Windows 事件的記錄查詢
下表提供擷取 Windows 事件記錄的不同記錄查詢範例。

| 查詢 | 描述 |
|:---|:---|
| 事件 |所有的 Windows 事件。 |
| Event &#124; where EventLevelName == "error" |所有 Windows 事件與錯誤的嚴重性。 |
| Event &#124; summarize count() by Source |依據來源的 Windows 事件計數。 |
| Event &#124; where EventLevelName == "error" &#124; summarize count() by Source |依據來源的 Windows 錯誤事件計數。 |


## <a name="next-steps"></a>後續步驟
* 設定 Log Analytics 以收集其他 [資料來源](agent-data-sources.md) 進行分析。
* 了解[記錄查詢](../log-query/log-query-overview.md)，以分析從資料來源和解決方案收集到的資料。  
* 設定從您的 Windows 代理程式進行 [效能計數器收集](data-sources-performance-counters.md) 。
