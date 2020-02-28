---
title: 在 Azure 監視器中使用適用于 IIS 的 blob 儲存體和適用于事件的資料表儲存體 |Microsoft Docs
description: Azure 監視器可以讀取 Azure 服務（將診斷寫入表格儲存體）的記錄，或寫入至 blob 儲存體的 IIS 記錄。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 44368ab90abd189c6a8a0792494828c87142eb20
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672390"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>將資料從 Azure 診斷延伸模組收集到 Azure 監視器記錄
Azure 診斷擴充功能是[Azure 監視器中的代理程式](agents-overview.md)，可從 Azure 計算資源（包括虛擬機器）的客體作業系統收集監視資料。 本文說明如何將診斷擴充功能所收集的資料從 Azure 儲存體收集到 Azure 監視器記錄。

> [!NOTE]
> Azure 監視器中的 Log Analytics 代理程式通常是從客體作業系統將資料收集到 Azure 監視器記錄檔的慣用方法。 如需代理程式的詳細比較，請參閱[Azure 監視器代理](agents-overview.md)程式的總覽。

## <a name="supported-data-types"></a>支援的資料類型
Azure 診斷擴充功能會將資料儲存在 Azure 儲存體帳戶中。 若要 Azure 監視器記錄檔來收集此資料，它必須位於下列位置：

| 記錄類型 | 資源類型 | 位置 |
| --- | --- | --- |
| IIS 記錄 |虛擬機器 <br> Web 角色 <br> 背景工作角色 |wad-iis-logfiles (Blob 儲存體) |
| Syslog |虛擬機器 |LinuxsyslogVer2v0 (表格儲存體) |
| Service Fabric 運作事件 |Service Fabric 節點 |WADServiceFabricSystemEventTable |
| Service Fabric Reliable Actor 事件 |Service Fabric 節點 |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable Service 事件 |Service Fabric 節點 |WADServiceFabricReliableServiceEventTable |
| Windows 事件記錄 |Service Fabric 節點 <br> 虛擬機器 <br> Web 角色 <br> 背景工作角色 |WADWindowsEventLogsTable (表格儲存體) |
| Windows ETW 記錄 |Service Fabric 節點 <br> 虛擬機器 <br> Web 角色 <br> 背景工作角色 |WADETWEventTable (表格儲存體) |

## <a name="data-types-not-supported"></a>不支援的資料類型

- 來自客體作業系統的效能資料
- 來自 Azure 網站的 IIS 記錄檔


## <a name="enable-azure-diagnostics-extension"></a>啟用 Azure 診斷擴充功能
如需安裝和設定診斷擴充功能的詳細資訊，請參閱[安裝和設定 Windows Azure 診斷擴充功能（WAD）](diagnostics-extension-windows-install.md)或[使用 Linux 診斷擴充功能來監視計量和記錄](../../virtual-machines/extensions/diagnostics-linux.md)。 這會允許您指定儲存體帳戶，以及設定要轉送到 Azure 監視器記錄的資料收集。


## <a name="collect-logs-from-azure-storage"></a>從 Azure 儲存體收集記錄檔
請使用下列程式，啟用從 Azure 儲存體帳戶收集診斷延伸模組資料的功能：

1. 在 Azure 入口網站中，移至  **Log Analytics 工作區** 並選取您的工作區。
1. 在功能表的 [**工作區資料來源**] 區段中，按一下 [**儲存體客戶紀錄**]。
2. 按一下 [**新增**]。
3. 選取包含要收集之資料的**儲存體帳戶**。
4. 選取您想要收集的**資料類型**。
5. [來源] 的值會根據資料類型自動填入。
6. 按一下 [確定] 儲存設定。
7. 針對其他資料類型重複執行。

大約30分鐘內，您就能夠在 Log Analytics 工作區中看到來自儲存體帳戶的資料。 您只會看到套用組態之後寫入儲存體的資料。 工作區不會從儲存體帳戶讀取預先存在的資料。

> [!NOTE]
> 入口網站不會驗證來源是否存在於儲存體帳戶中，或是否正在寫入新資料。



## <a name="next-steps"></a>後續步驟

* 針對支援的 Azure 服務[收集 Azure 服務的記錄與計量](collect-azure-metrics-logs.md)。
* [啟用解決方案](../../azure-monitor/insights/solutions.md) 以提供資料的深入見解。
* [使用搜尋查詢](../../azure-monitor/log-query/log-query-overview.md) 以分析資料。
