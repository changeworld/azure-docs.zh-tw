---
title: 針對 Azure 變更追蹤問題進行疑難排解
description: 瞭解如何使用 Azure 自動化更改跟蹤和清單功能解決問題。
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198525"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>針對變更追蹤和清查進行疑難排解

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>方案：未為 Windows 電腦顯示更改追蹤記錄

#### <a name="issue"></a>問題

您看不到為更改跟蹤而上載的 Windows 電腦的任何更改跟蹤或清單結果。

#### <a name="cause"></a>原因

此錯誤可能有以下原因：

* 微軟監視代理未運行。
* 回自動化帳戶的通信被阻止。
* 不會下載用於更改跟蹤的管理包。
* 正在裝載的 VM 可能來自未安裝 Microsoft 監視代理的克隆電腦。

#### <a name="resolution"></a>解決方案

下面描述的解決方案可能有助於解決您的問題。 如果仍需要幫助，可以收集診斷資訊並聯系支援人員。 在代理電腦上，導航到 C：\程式檔\微軟監視代理\代理\工具並運行以下命令：

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> 預設情況下，啟用錯誤跟蹤。 要啟用詳細錯誤訊息，如上例所示，請使用*VER*參數。 有關資訊跟蹤，在調用**StartTracing.cmd**時使用*INF。*

##### <a name="microsoft-monitoring-agent-not-running"></a>微軟監控代理未運行

驗證 Microsoft 監視代理 （HealthService.exe） 是否在電腦上運行。

##### <a name="communication-to-automation-account-blocked"></a>與自動化帳戶的通信被阻止

檢查電腦上的事件檢視器，並查找其中具有"更改跟蹤"字樣的任何事件。

請參閱[使用混合 Runbook 輔助角色自動在資料中心或雲中實現資源](../automation-hybrid-runbook-worker.md#network-planning)，以瞭解更改跟蹤工作必須允許的位址和埠。

##### <a name="management-packs-not-downloaded"></a>未下載的管理包

驗證以下更改跟蹤和庫存管理包是否安裝在本地：

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>未進行壓縮的克隆電腦的 VM

如果使用克隆映射，則先對映射進行系統準備，然後安裝 Microsoft 監視代理。

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>方案：Linux 電腦上無更改跟蹤或庫存結果

#### <a name="issue"></a>問題

您看不到已加入以進行更改跟蹤的 Linux 電腦的任何庫存或更改跟蹤結果。 

#### <a name="cause"></a>原因
以下是特定于此問題的可能原因：
* Linux 的日誌分析代理未運行。
* Linux 的日誌分析代理配置不正確。
* 存在檔完整性監視 （FIM） 衝突。

#### <a name="resolution"></a>解決方案 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Linux 未運行的日誌分析代理

驗證 Linux 日誌分析代理（omsagent）的守護進程是否在您的電腦上運行。 在連結到自動化帳戶的日誌分析工作區中運行以下查詢。

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

如果在查詢結果中看不到電腦，則電腦最近未簽入。 可能存在本地配置問題，您應該重新安裝代理。 有關安裝和配置的資訊，請參閱[使用日誌分析代理收集日誌資料](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)。 

如果電腦顯示在查詢結果中，請驗證範圍配置。 請參閱[Azure 監視器 中的目標監視解決方案](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)。

有關此問題的更多故障排除，請參閱[問題：您沒有看到任何 Linux 資料](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)。

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Linux 的日誌分析代理配置不正確

使用 OMS 日誌收集器工具，可能未正確配置 Linux 的日誌分析代理以用於日誌和命令列輸出集合。 請參閱[使用"更改跟蹤"解決方案跟蹤環境中的更改](../change-tracking.md)。

##### <a name="fim-conflicts"></a>職能指令手冊衝突

Azure 安全中心的 FIM 功能可能錯誤地驗證了 Linux 檔的完整性。 驗證 FIM 是否可操作並正確配置用於 Linux 檔監視。 請參閱[使用"更改跟蹤"解決方案跟蹤環境中的更改](../change-tracking.md)。

## <a name="next-steps"></a>後續步驟

如果您沒有看到問題或無法解決問題，請使用以下通道之一獲得更多支援。

* 通過[Azure 論壇](https://azure.microsoft.com/support/forums/)從 Azure 專家那裡獲得答案。
* 與[@AzureSupport](https://twitter.com/azuresupport)— 正式的 Microsoft Azure 帳戶連接，通過將 Azure 社區連接到正確的資源（答案、支援和專家），從而改善客戶體驗。
* 如果需要更多協助，您可以提出 Azure 支援事件。 轉到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **"獲取支援**"。
