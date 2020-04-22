---
title: 解決變更追蹤及庫存問題
description: 瞭解如何使用 Azure 自動化更改追蹤和清單解決方案解決問題。
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 11c1fd05055922b07801c20d525d852d5360b069
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679356"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>解決變更追蹤及庫存問題

本文介紹如何解決更改跟蹤和庫存問題。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](../automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>方案:未為 Windows 電腦顯示變更追蹤及庫存記錄

#### <a name="issue"></a>問題

您看不到已上載的 Windows 計算機的任何更改追蹤和清單結果。

#### <a name="cause"></a>原因

錯誤可能有以下原因:

* Windows 的日誌分析代理未運行。
* 回自動化帳戶的通信被阻止。
* 不會下載更改追蹤和庫存管理包。
* 正在裝載的 VM 可能來自未與安裝的 Windows 的日誌分析代理結合的克隆電腦。

#### <a name="resolution"></a>解決方案

在紀錄分析代理電腦上,導航到**C:\程式檔案\Microsoft監視代理\代理\工具**並執行以下指令:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

如果仍需要説明,可以收集診斷資訊並聯繫支持人員。 

> [!NOTE]
> 默認情況下,日誌分析代理支援錯誤跟蹤。 要啟用詳細錯誤訊息,如上例所示,請使用參數`VER`。 對於資訊追蹤，請在叫用 `StartTracing.cmd` 時使用 `INF`。

##### <a name="log-analytics-agent-for-windows-not-running"></a>未執行的 Windows 的紀錄分析代理

驗證 Windows 的日誌分析代理(**Health Service.exe**) 是否在電腦上運行。

##### <a name="communication-to-automation-account-blocked"></a>與自動化帳戶的通訊被阻止

請檢查機器上的事件檢視器，並尋找其中有 `changetracking` 字組的任何事件。

請參閱[使用混合 Runbook Worker 來自動執行資料中心或雲端中的資源](../automation-hybrid-runbook-worker.md#network-planning),以瞭解更改追蹤和清單正常工作必須允許的位址和埠。

##### <a name="management-packs-not-downloaded"></a>未下載的管理套件

驗證以下變更追蹤及庫存管理套件是否安裝在本地:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>未進行壓縮的複製的 VM

如果使用複製檔,請先對映射進行系統準備,然後安裝 Windows 的日誌分析代理。

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>方案:Linux 電腦上無變更追蹤和庫存結果

#### <a name="issue"></a>問題

您看不到為解決方案而加入的 Linux 電腦的任何庫存和更改追蹤結果。 

#### <a name="cause"></a>原因
以下是特定於此問題的可能原因:
* Linux 的日誌分析代理未運行。
* Linux 的日誌分析代理配置不正確。
* 存在檔完整性監視 (FIM) 衝突。

#### <a name="resolution"></a>解決方案 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Linux 未執行的紀錄分析代理

驗證 Linux**的日誌**分析代理的守護程式是否在您的電腦上運行。 在連結到自動化帳戶的日誌分析工作區中運行以下查詢。

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

如果在查詢結果中看不到計算機,則計算機最近未簽入。 可能存在本地設定問題,您應該重新安裝代理。 有關安裝與設定的資訊,請參閱[使用紀錄分析代理收集紀錄資料](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)。 

如果電腦顯示在查詢結果中,請驗證範圍配置。 請參考[Azure 監視器 中的目標監視解決方案](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)。

有關此問題的更多故障排除,請參閱[問題:您沒有看到任何 Linux 資料](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)。

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Linux 的紀錄分析代理設定不正確

使用 OMS 日誌收集器工具,可能未正確配置 Linux 的日誌分析代理以用於日誌和命令行輸出集合。 請參考[追蹤器與清單' 的解決方案追蹤環境中的變更](../change-tracking.md)。

##### <a name="fim-conflicts"></a>職能指令手冊衝突

Azure 安全中心的 FIM 功能可能錯誤地驗證了 Linux 檔的完整性。 驗證 FIM 是否可操作並正確配置用於 Linux 檔案監視。 請參考[追蹤器與清單' 的解決方案追蹤環境中的變更](../change-tracking.md)。

## <a name="next-steps"></a>後續步驟

如果您在上面看不到問題或無法解決問題,請嘗試以下管道之一以獲取其他支援:

* 通過[Azure 論壇](https://azure.microsoft.com/support/forums/)從 Azure 專家那裡獲得答案。
* 與[@AzureSupport](https://twitter.com/azuresupport)連接 ,官方 Microsoft Azure 帳戶通過將 Azure 社區連接到正確的資源(答案、支援和專家)來改善客戶體驗。
* 提出 Azure 支援事件。 跳到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **「取得支援**」。
