---
title: 針對 Azure 自動化的變更追蹤和清查問題進行疑難排解
description: 本文說明如何針對 Azure 自動化的變更追蹤和清查功能進行疑難排解及解決問題。
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3fe28ba0871009785b1bb8b263b42f453c2918be
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684861"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>針對變更追蹤和清查問題進行疑難排解

本文說明如何針對 AAzure 自動化的變更追蹤和清查問題進行疑難排解及解決。 如需變更追蹤和清查的一般資訊，請參閱[變更追蹤和清查概觀](../change-tracking.md)。

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>案例：Windows 電腦不會顯示變更追蹤和清查記錄

#### <a name="issue"></a>問題

您看不到任何已啟用此功能之 Windows 電腦的變更追蹤和清查結果。

#### <a name="cause"></a>原因

此錯誤可能有下列原因：

* 適用於 Windows 的 Azure Log Analytics 代理程式不在執行中。
* 正在封鎖連回自動化帳戶的通訊。
* 無法下載變更追蹤和清查管理組件。
* 正在啟用的 VM 可能來自於未使用系統準備 (sysprep) 做準備，且已安裝適用於 Windows 的 Log Analytics 代理程式的複製電腦。

#### <a name="resolution"></a>解決方案

在 Log Analytics 代理程式電腦上，移至 **C:\Program Files\Microsoft Monitoring Agent\Agent\Tools** 並執行下列命令：

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

如果您仍然需要協助，則可收集診斷資訊並連絡支援人員。

> [!NOTE]
> 根據預設，Log Analytics 代理程式會啟用錯誤追蹤。 若要啟用上述範例中的詳細錯誤訊息，請使用 `VER` 參數。 對於資訊追蹤，請在叫用 `StartTracing.cmd` 時使用 `INF`。

##### <a name="log-analytics-agent-for-windows-not-running"></a>適用於 Windows 的 Log Analytics 代理程式不在執行中

確認電腦上正在執行適用於 Windows 的 Log Analytics 代理程式 (**HealthService**)。

##### <a name="communication-to-automation-account-blocked"></a>已封鎖與自動化帳戶的通訊

請檢查電腦上的 [事件檢視器]，並尋找其中有 `changetracking` 字組的任何事件。

若要了解必須允許才能讓變更追蹤和清查正常運作的位址和連接埠，請參閱[網路規劃](../automation-hybrid-runbook-worker.md#network-planning)。

##### <a name="management-packs-not-downloaded"></a>未下載管理組件

請確認下列變更追蹤和清查管理組件已安裝於本機：

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>複製的電腦中尚未執行 Sysprep 的 VM

如果使用複製的映像，請先對映像執行 Sysprep，然後安裝適用於 Windows 的 Log Analytics 代理程式。

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>案例：Linux 電腦上沒有變更追蹤和清查結果

#### <a name="issue"></a>問題

您看不到任何已啟用此功能之 Linux 電腦的變更追蹤和清查結果。 

#### <a name="cause"></a>原因
以下是此問題特有的可能原因：
* 適用於 Linux 的 Log Analytics 代理程式不在執行中。
* 未正確設定適用於 Linux 的 Log Analytics 代理程式。
* 有檔案完整性監視 (FIM) 衝突。

#### <a name="resolution"></a>解決方案 

##### <a name="log-analytics-agent-for-linux-not-running"></a>適用於 Linux 的 Log Analytics 代理程式不在執行中

確認電腦上正在執行適用於 Linux 的 Log Analytics 代理程式精靈 (**omsagent**)。 請在與您的自動化帳戶連結的 Log Analytics 工作區中執行下列查詢。

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

如果您在查詢結果中看不到您的電腦，則其最近未簽入。 可能是本機設定問題，您應該重新安裝代理程式。 如需安裝和設定的相關資訊，請參閱[使用 Log Analytics 代理程式收集記錄資料](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)。

如果您的電腦顯示在查詢結果中，請檢查範圍設定。 請參閱 [Azure 監視器中的目標監視解決方案](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)。

若要深入了解此問題的疑難排解，請參閱[問題：您沒有看到任何 Linux 資料](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)。

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>未正確設定適用於 Linux 的 Log Analytics 代理程式

可能未使用 OMS 記錄收集器工具，針對記錄和命令列輸出收集，正確地設定適用於 Linux 的 Log Analytics 代理程式。 請參閱[變更追蹤和清查概觀](../change-tracking.md)。

##### <a name="fim-conflicts"></a>FIM 衝突

Azure 資訊安全中心的 FIM 功能可能未正確地驗證 Linux 檔案的完整性。 確認 FIM 運作正常並已針對 Linux 檔案監視正確地設定。 請參閱[變更追蹤和清查概觀](../change-tracking.md)。

## <a name="next-steps"></a>後續步驟

如果您在這裡沒有看到您的問題，或無法解決您的問題，請嘗試下列其中一個管道以取得其他支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答。
* 連線至 [@AzureSupport](https://twitter.com/azuresupport)，這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 Azure 支援會將 Azure 社群連線到解答、支援及專家。
* 提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
