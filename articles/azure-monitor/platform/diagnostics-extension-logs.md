---
title: 對 Azure 監視器中的事件使用 IIS 和表存儲的 blob 存儲 |微軟文檔
description: Azure 監視器可以讀取 Azure 服務的日誌，這些服務將診斷寫入表存儲或寫入 Blob 存儲的 IIS 日誌。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 44368ab90abd189c6a8a0792494828c87142eb20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672390"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>從 Azure 診斷擴展到 Azure 監視器日誌收集資料
Azure 診斷擴展是[Azure 監視器中的代理](agents-overview.md)，用於從 Azure 計算資源（包括虛擬機器）的客體作業系統收集監視資料。 本文介紹如何收集診斷擴展從 Azure 存儲到 Azure 監視器日誌收集的資料。

> [!NOTE]
> Azure 監視器中的日誌分析代理通常是將資料從客體作業系統收集到 Azure 監視器日誌的首選方法。 有關代理的詳細比較，請參閱[Azure 監視器代理的概述](agents-overview.md)。

## <a name="supported-data-types"></a>支援的資料類型
Azure 診斷擴展在 Azure 存儲帳戶中存儲資料。 對於 Azure 監視器日誌要收集此資料，它必須位於以下位置：

| 記錄類型 | 資源類型 | Location |
| --- | --- | --- |
| IIS 記錄 |虛擬機器 <br> Web 角色 <br> 背景工作角色 |wad-iis-logfiles (Blob 儲存體) |
| syslog |虛擬機器 |LinuxsyslogVer2v0 (表格儲存體) |
| Service Fabric 運作事件 |Service Fabric 節點 |WADServiceFabricSystemEventTable |
| Service Fabric Reliable Actor 事件 |Service Fabric 節點 |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable Service 事件 |Service Fabric 節點 |WADServiceFabricReliableServiceEventTable |
| Windows 事件記錄 |Service Fabric 節點 <br> 虛擬機器 <br> Web 角色 <br> 背景工作角色 |WADWindowsEventLogsTable (表格儲存體) |
| Windows ETW 記錄 |Service Fabric 節點 <br> 虛擬機器 <br> Web 角色 <br> 背景工作角色 |WADETWEventTable (表格儲存體) |

## <a name="data-types-not-supported"></a>不支援資料類型

- 來自客體作業系統的效能資料
- 來自 Azure 網站的 IIS 日誌


## <a name="enable-azure-diagnostics-extension"></a>啟用 Azure 診斷擴展
有關[安裝和配置診斷擴展的詳細資訊，請參閱安裝和配置 Windows Azure 診斷擴展 （WAD）](diagnostics-extension-windows-install.md)或使用[Linux 診斷擴展來監視指標和日誌](../../virtual-machines/extensions/diagnostics-linux.md)。 這將讓您指定存儲帳戶，並配置要轉發到 Azure 監視器日誌的資料的集合。


## <a name="collect-logs-from-azure-storage"></a>從 Azure 存儲收集日誌
使用以下過程啟用從 Azure 存儲帳戶收集診斷擴展資料：

1. 在 Azure 門戶中，轉到**日誌分析工作區**並選擇工作區。
1. 按一下功能表的"**工作區資料來源"** 部分中的 **"存儲帳戶日誌**"。
2. 按一下"**添加**"。
3. 選擇包含要收集的資料的**存儲帳戶**。
4. 選擇要收集的**資料類型**。
5. 根據資料類型自動填滿源的值。
6. 按一下 [確定]**** 儲存設定。
7. 對其他資料類型重複上述步驟。

大約 30 分鐘後，您可以在日誌分析工作區中查看來自存儲帳戶的資料。 您只會看到套用組態之後寫入儲存體的資料。 工作區不會從存儲帳戶讀取預先存在的資料。

> [!NOTE]
> 門戶不驗證源是否存在於存儲帳戶中或是否正在寫入新資料。



## <a name="next-steps"></a>後續步驟

* 針對支援的 Azure 服務[收集 Azure 服務的記錄與計量](collect-azure-metrics-logs.md)。
* [啟用解決方案](../../azure-monitor/insights/solutions.md) 以提供資料的深入見解。
* [使用搜尋查詢](../../azure-monitor/log-query/log-query-overview.md) 以分析資料。
