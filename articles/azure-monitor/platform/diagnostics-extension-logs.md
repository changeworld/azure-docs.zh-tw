---
title: 針對 IIS 使用 blob 儲存體，並針對 Azure 監視器中的事件使用資料表儲存體 |Microsoft Docs
description: Azure 監視器可以讀取將診斷寫入表格儲存體的 Azure 服務記錄，或讀取寫入 blob 儲存體的 IIS 記錄。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 095fd0b534c0dffaf80d2464fb9734f295335b84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87317173"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>從 Azure 診斷延伸模組收集資料以 Azure 監視器記錄
Azure 診斷擴充功能是 [Azure 監視器中的代理程式](agents-overview.md) ，可從 Azure 計算資源（包括虛擬機器）的客體作業系統收集監視資料。 本文描述如何將診斷延伸模組所收集的資料從 Azure 儲存體收集到 Azure 監視器記錄。

> [!NOTE]
> Azure 監視器中的 Log Analytics 代理程式通常是將資料從客體作業系統收集到 Azure 監視器記錄檔的慣用方法。 如需代理程式的詳細比較，請參閱 [Azure 監視器代理](agents-overview.md) 程式的總覽。

## <a name="supported-data-types"></a>支援的資料類型
Azure 診斷擴充功能會將資料儲存在 Azure 儲存體帳戶中。 針對 Azure 監視器記錄檔收集此資料，它必須位於下列位置：

| 記錄類型 | 資源類型 | Location |
| --- | --- | --- |
| IIS 記錄 |虛擬機器 <br> Web 角色 <br> 背景工作角色 |wad-iis-logfiles (Blob 儲存體) |
| syslog |虛擬機器 |LinuxsyslogVer2v0 (表格儲存體) |
| Service Fabric 運作事件 |Service Fabric 節點 |WADServiceFabricSystemEventTable |
| Service Fabric Reliable Actor 事件 |Service Fabric 節點 |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable Service 事件 |Service Fabric 節點 |WADServiceFabricReliableServiceEventTable |
| Windows 事件記錄檔 |Service Fabric 節點 <br> 虛擬機器 <br> Web 角色 <br> 背景工作角色 |WADWindowsEventLogsTable (表格儲存體) |
| Windows ETW 記錄 |Service Fabric 節點 <br> 虛擬機器 <br> Web 角色 <br> 背景工作角色 |WADETWEventTable (表格儲存體) |

## <a name="data-types-not-supported"></a>不支援的資料類型

- 來自客體作業系統的效能資料
- Azure 網站的 IIS 記錄


## <a name="enable-azure-diagnostics-extension"></a>啟用 Azure 診斷擴充功能
如需安裝和設定診斷延伸模組的詳細資訊，請參閱 [安裝和設定 Windows Azure 診斷延伸模組 (WAD) ](diagnostics-extension-windows-install.md) 或 [使用 Linux 診斷擴充功能來監視計量和記錄](../../virtual-machines/extensions/diagnostics-linux.md) 。 這會允許您指定儲存體帳戶，並設定您想要轉寄給 Azure 監視器記錄檔的資料收集。


## <a name="collect-logs-from-azure-storage"></a>從 Azure 儲存體收集記錄
使用下列程式，啟用從 Azure 儲存體帳戶收集診斷延伸模組資料的功能：

1. 在 Azure 入口網站中，移至 **Log Analytics 工作區** ，然後選取您的工作區。
1. 在功能表的 [**工作區資料來源**] 區段中，按一下 [**儲存體客戶紀錄**]。
2. 按一下 [新增]  。
3. 選取包含要收集之資料的 **儲存體帳戶** 。
4. 選取您要收集的 **資料類型** 。
5. 來源的值會根據資料類型自動填入。
6. 按一下 [確定] 以儲存組態。
7. 針對其他資料類型重複執行。

大約30分鐘內，您就能夠在 Log Analytics 工作區的儲存體帳戶中看到資料。 您只會看到套用組態之後寫入儲存體的資料。 工作區不會從儲存體帳戶讀取預先存在的資料。

> [!NOTE]
> 入口網站不會驗證來源是否存在於儲存體帳戶中，或是否正在寫入新資料。



## <a name="next-steps"></a>接下來的步驟

* 針對支援的 Azure 服務[收集 Azure 服務的記錄與計量](./resource-logs.md#send-to-log-analytics-workspace)。
* [啟用解決方案](../insights/solutions.md) 以提供資料的深入見解。
* [使用搜尋查詢](../log-query/log-query-overview.md) 以分析資料。

