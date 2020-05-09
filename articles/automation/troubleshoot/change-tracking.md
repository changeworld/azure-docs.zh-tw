---
title: 針對變更追蹤和清查的問題進行疑難排解
description: 瞭解如何疑難排解和解決 Azure 自動化變更追蹤和清查解決方案的問題。
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4f230cd0965d58f690d333cd62f2c7c1d499e8d1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582157"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>針對變更追蹤和清查問題進行疑難排解

本文說明如何針對 Azure 自動化變更追蹤和清查問題進行疑難排解。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>案例：不會顯示 Windows 機器的變更追蹤和清查記錄

#### <a name="issue"></a>問題

您看不到上架的 Windows 機器的任何變更追蹤和清查結果。

#### <a name="cause"></a>原因

此錯誤可能有下列原因：

* 適用于 Windows 的 Azure Log Analytics 代理程式不在執行中。
* 已封鎖送回自動化帳戶的通訊。
* 變更追蹤和清查管理元件不會下載。
* 所上架的 VM 可能來自于已安裝適用于 Windows 的 Log Analytics 代理程式未執行過 sysprep 的複製電腦。

#### <a name="resolution"></a>解決方案

在 Log Analytics 代理程式電腦上，移至**C:\Program Files\Microsoft Monitoring Agent\Agent\Tools** ，然後執行下列命令：

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

如果您仍然需要協助，您可以收集診斷資訊和連絡人支援。

> [!NOTE]
> 根據預設，Log Analytics 代理程式會啟用錯誤追蹤。 若要啟用上述範例中的`VER`詳細資訊錯誤訊息，請使用參數。 如需資訊追蹤， `INF`請在叫`StartTracing.cmd`用時使用。

##### <a name="log-analytics-agent-for-windows-not-running"></a>適用于 Windows 的 Log Analytics 代理程式未執行

確認電腦上正在執行適用于 Windows 的 Log Analytics 代理程式（**HealthService .exe**）。

##### <a name="communication-to-automation-account-blocked"></a>已封鎖與自動化帳戶的通訊

檢查電腦上的事件檢視器，並尋找其中包含該字`changetracking`的任何事件。

若要瞭解必須允許變更追蹤和清查工作的位址和埠，請參閱[使用混合式 Runbook 背景工作角色將資料中心或雲端中的資源自動化](../automation-hybrid-runbook-worker.md#network-planning)。

##### <a name="management-packs-not-downloaded"></a>未下載管理元件

確認下列變更追蹤和清查管理元件已安裝在本機：

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>尚未執行過 sysprep 的已複製電腦上的 VM

如果使用複製的映射，請先對映射進行 sysprep，然後再安裝適用于 Windows 的 Log Analytics 代理程式。

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>案例： Linux 機器上沒有變更追蹤和清查結果

#### <a name="issue"></a>問題

針對解決方案上架的 Linux 機器，您看不到任何變更追蹤和清查結果。 

#### <a name="cause"></a>原因
以下是此問題特定的可能原因：
* 適用于 Linux 的 Log Analytics 代理程式未執行。
* 適用于 Linux 的 Log Analytics 代理程式未正確設定。
* 有檔案完整性監視（FIM）衝突。

#### <a name="resolution"></a>解決方案 

##### <a name="log-analytics-agent-for-linux-not-running"></a>適用于 Linux 的 Log Analytics 代理程式未執行

確認適用于 Linux 的 Log Analytics 代理程式（**omsagent**）的 daemon 正在您的電腦上執行。 在與您的自動化帳戶連結的 Log Analytics 工作區中，執行下列查詢。

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

如果您在查詢結果中看不到您的電腦，它最近未簽入。 可能是本機設定問題，您應該重新安裝代理程式。 如需安裝和設定的相關資訊，請參閱[使用 Log Analytics 代理程式收集記錄資料](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)。

如果您的電腦顯示在查詢結果中，請確認範圍設定。 請參閱[Azure 監視器中的目標監視解決方案](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)。

如需此問題的詳細疑難排解，請參閱[問題：您沒有看到任何 Linux 資料](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)。

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>適用于 Linux 的 Log Analytics 代理程式未正確設定

適用于 Linux 的 Log Analytics 代理程式可能未正確設定記錄檔和命令列輸出收集，方法是使用 OMS 記錄收集器工具。 請參閱[使用變更追蹤和清查解決方案來追蹤環境中的變更](../change-tracking.md)。

##### <a name="fim-conflicts"></a>FIM 衝突

Azure 資訊安全中心的 FIM 功能可能不正確地驗證您 Linux 檔案的完整性。 確認 FIM 運作正常且已正確設定，以進行 Linux 檔案監視。 請參閱[使用變更追蹤和清查解決方案來追蹤環境中的變更](../change-tracking.md)。

## <a name="next-steps"></a>後續步驟

如果您在這裡沒有看到您的問題，或無法解決您的問題，請嘗試下列其中一個通道以取得其他支援：

* 透過[Azure 論壇](https://azure.microsoft.com/support/forums/)取得 azure 專家的解答。
* 與[@AzureSupport](https://twitter.com/azuresupport)官方 Microsoft Azure 帳戶聯繫，以改善客戶體驗。 Azure 支援將 Azure 社區連接到解答、支援及專家。
* 提出 Azure 支援事件。 前往[Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [**取得支援**]。
