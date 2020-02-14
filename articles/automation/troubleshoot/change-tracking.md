---
title: 針對 Azure 變更追蹤問題進行疑難排解
description: 瞭解如何針對 Azure 自動化變更追蹤和清查功能進行疑難排解及解決問題。
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198525"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>針對變更追蹤和清查進行疑難排解

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>案例：未顯示 Windows 機器的變更追蹤記錄

#### <a name="issue"></a>問題

針對變更追蹤上架的 Windows 機器，您看不到任何變更追蹤或清查結果。

#### <a name="cause"></a>原因

此錯誤可能有下列原因：

* Microsoft Monitoring Agent 不在執行中。
* 已封鎖送回自動化帳戶的通訊。
* 變更追蹤的管理元件不會下載。
* 所上架的 VM 可能來自未與安裝 Microsoft Monitoring Agent 執行過 sysprep 的複製電腦。

#### <a name="resolution"></a>解決方案

下面所述的解決方案可能有助於解決您的問題。 如果您仍然需要協助，您可以收集診斷資訊和連絡人支援。 在代理程式電腦上，流覽至 C:\Program Files\Microsoft Monitoring Agent\Agent\Tools，然後執行下列命令：

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> 根據預設，會啟用錯誤追蹤。 若要啟用上述範例中的詳細資訊錯誤訊息，請使用*VER*參數。 如需資訊追蹤，請在叫用**starttracing.cmd**時使用*INF* 。

##### <a name="microsoft-monitoring-agent-not-running"></a>Microsoft Monitoring Agent 未執行

確認 Microsoft Monitoring Agent （HealthService .exe）正在電腦上執行。

##### <a name="communication-to-automation-account-blocked"></a>已封鎖與自動化帳戶的通訊

檢查電腦上的事件檢視器，並尋找其中有 "變更追蹤" 這個字的任何事件。

請參閱[使用混合式 Runbook 背景工作角色將資料中心或雲端中的資源自動化](../automation-hybrid-runbook-worker.md#network-planning)，以瞭解必須允許變更追蹤工作的位址和埠。

##### <a name="management-packs-not-downloaded"></a>未下載管理元件

確認下列變更追蹤和清查管理元件已安裝在本機：

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>尚未執行過 sysprep 的已複製電腦上的 VM

如果使用複製的映射，請先對映射進行 sysprep，然後再安裝 Microsoft Monitoring Agent。

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>案例： Linux 機器上沒有變更追蹤或清查結果

#### <a name="issue"></a>問題

針對變更追蹤上架的 Linux 機器，您看不到任何清查或變更追蹤結果。 

#### <a name="cause"></a>原因
以下是此問題特定的可能原因：
* 適用于 Linux 的 Log Analytics 代理程式未執行。
* 適用于 Linux 的 Log Analytics 代理程式未正確設定。
* 有檔案完整性監視（FIM）衝突。

#### <a name="resolution"></a>解決方案 

##### <a name="log-analytics-agent-for-linux-not-running"></a>適用于 Linux 的 Log Analytics 代理程式未執行

確認適用于 Linux 的 Log Analytics 代理程式（omsagent）的 daemon 正在您的電腦上執行。 在與您的自動化帳戶連結的 Log Analytics 工作區中，執行下列查詢。

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

如果您在查詢結果中看不到您的電腦，它最近未簽入。 可能是本機設定問題，您應該重新安裝代理程式。 如需安裝和設定的相關資訊，請參閱[使用 Log Analytics 代理程式收集記錄資料](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)。 

如果您的電腦顯示在查詢結果中，請確認範圍設定。 請參閱[Azure 監視器中的目標監視解決方案](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)。

如需此問題的詳細疑難排解，請參閱[問題：您沒有看到任何 Linux 資料](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)。

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>適用于 Linux 的 Log Analytics 代理程式未正確設定

適用于 Linux 的 Log Analytics 代理程式可能未正確設定，無法使用 OMS 記錄收集器工具進行記錄檔和命令列輸出收集。 請參閱[使用變更追蹤解決方案追蹤環境中的變更](../change-tracking.md)。

##### <a name="fim-conflicts"></a>FIM 衝突

Azure 資訊安全中心的 FIM 功能可能不正確地驗證您 Linux 檔案的完整性。 確認 FIM 運作正常且已正確設定，以進行 Linux 檔案監視。 請參閱[使用變更追蹤解決方案追蹤環境中的變更](../change-tracking.md)。

## <a name="next-steps"></a>後續步驟

如果您看不到問題或無法解決問題，請使用下列其中一個通道以取得更多支援。

* 透過[Azure 論壇](https://azure.microsoft.com/support/forums/)取得 azure 專家的解答。
* 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
* 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
