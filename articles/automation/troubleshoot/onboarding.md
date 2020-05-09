---
title: 疑難排解 Azure 自動化管理解決方案上架的問題
description: 瞭解如何針對 Azure 自動化解決方案上架錯誤進行疑難排解。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 371094ecba5168fd32a7af9fb81a71eb722efc91
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836524"
---
# <a name="troubleshoot-solution-onboarding"></a>疑難排解解決方案上架

當您將 Azure 自動化更新管理解決方案或變更追蹤和清查解決方案上架時，可能會收到錯誤訊息。 本文說明可能會發生的各種錯誤，以及解決問題的方式。

## <a name="known-issues"></a>已知問題

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>案例：重新命名已註冊的節點需要再次取消登錄或註冊

#### <a name="issue"></a>問題

系統會向 Azure 自動化註冊節點，然後再變更作業系統電腦名稱稱。 節點中的報表會繼續以原始名稱出現。

#### <a name="cause"></a>原因

重新命名已註冊的節點並不會更新 Azure 自動化中的節點名稱。

#### <a name="resolution"></a>解決方案

從 Azure 自動化狀態設定取消註冊節點，然後重新註冊。 在該時間之前發行至服務的報表將無法再使用。

### <a name="scenario-re-signing-certificates-via-https-proxy-isnt-supported"></a><a name="resigning-cert"></a>案例：不支援透過 HTTPS proxy 重新簽署憑證

#### <a name="issue"></a>問題

當您透過會終止 HTTPS 流量的 proxy 解決方案進行連線，然後使用新的憑證重新加密流量時，服務不會允許連線。

#### <a name="cause"></a>原因

Azure 自動化不支援重新簽署用來加密流量的憑證。

#### <a name="resolution"></a>解決方案

目前沒有解決此問題的因應措施。

## <a name="general-errors"></a>一般錯誤

### <a name="scenario-onboarding-fails-with-the-message-the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>案例：上架失敗並出現「無法啟用解決方案」訊息

#### <a name="issue"></a>問題

當您嘗試將 VM 上架到解決方案時，您會收到下列其中一則訊息：

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>原因

此錯誤是因為 VM 或工作區或使用者的許可權不正確或遺失所造成。

#### <a name="resolution"></a>解決方案

請確定您擁有讓[機器上線所需](../automation-role-based-access-control.md#onboarding-permissions)的正確許可權，然後再次嘗試將解決方案上架。 如果您收到錯誤訊息`The solution cannot be enabled on this VM because the permission to read the workspace is missing`，請確定您有`Microsoft.OperationalInsights/workspaces/read`權能夠找出 VM 是否已上架至工作區。

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>案例：上架失敗並出現「無法設定自動化帳戶以進行診斷記錄」訊息

#### <a name="issue"></a>問題

當您嘗試將 VM 上架到解決方案時，您會收到下列訊息：

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>原因

如果定價層不符合訂用帳戶的計費模型，可能會導致此錯誤。 如需詳細資訊，請參閱[Azure 監視器中的監視使用量和估計成本](https://aka.ms/PricingTierWarning)。

#### <a name="resolution"></a>解決方案

手動建立您的 Log Analytics 工作區，並重複上執行緒序以選取所建立的工作區。

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>案例：ComputerGroupQueryFormatError

#### <a name="issue"></a>問題

這個錯誤碼表示用來作為方案目標的已儲存搜尋電腦群組查詢，格式不正確。 

#### <a name="cause"></a>原因

您可能已改變查詢，或者系統可能已改變它。

#### <a name="resolution"></a>解決方案

您可以刪除方案的查詢，然後重新上架方案，這會重新建立查詢。 您可以在工作區的 [**已儲存的搜尋**] 下找到此查詢。 查詢的名稱是**MicrosoftDefaultComputerGroup**，而查詢的類別是相關聯解決方案的名稱。 如果啟用多個解決方案， **MicrosoftDefaultComputerGroup**查詢會在 [**已儲存的搜尋**] 下顯示多次。

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>案例：PolicyViolation

#### <a name="issue"></a>問題

這個錯誤碼表示部署因違反一或多個原則而失敗。

#### <a name="cause"></a>原因 

原則封鎖作業無法完成。

#### <a name="resolution"></a>解決方案

若要成功部署解決方案，您必須考慮改變指定的原則。 因為有許多不同類型的原則可以定義，所以所需的變更取決於違反的原則。 例如，如果在資源群組上定義原則，但拒絕變更某些包含資源內容的許可權，您可以選擇下列其中一個修正：

* 完全移除原則。
* 嘗試將解決方案上架到不同的資源群組。
* 將原則的目標重定為特定資源，例如自動化帳戶。
* 修改原則已設定為拒絕的資源集合。

檢查 Azure 入口網站右上角的通知，或移至包含您自動化帳戶的資源群組，然後選取 [**設定**] 底下的 [**部署**] 以查看失敗的部署。 若要深入瞭解 Azure 原則，請參閱[Azure 原則的總覽](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)。

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>案例：嘗試取消連結工作區的錯誤

#### <a name="issue"></a>問題

當您嘗試取消連結工作區時，收到下列錯誤訊息：

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>原因

當您的 Log Analytics 工作區中仍有作用中的解決方案，而這取決於您的自動化帳戶和要連結的 Log Analytics 工作區時，會發生此錯誤。

### <a name="resolution"></a>解決方案

如果您要使用下列解決方案，請從工作區中移除：

* 更新管理
* 變更追蹤與詳細目錄
* 於下班時間開始/停止 VM

移除解決方案之後，您可以取消連結您的工作區。 請務必從您的工作區和您的自動化帳戶清除這些解決方案中的任何現有成品：

* 針對更新管理，請從您的自動化帳戶移除**更新部署（排程）** 。
* 若要在離峰時間啟動/停止 vm，請在 [**設定** > ] [**鎖定**] 底下的自動化帳戶中移除解決方案元件的任何鎖定。 如需詳細資訊，請參閱[在離峰期間移除啟動/停止 vm 解決方案](../automation-solution-vm-management.md#remove-the-solution)。

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>適用于 Windows 擴充功能失敗的 Log Analytics

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

安裝適用于 Windows 的 Log Analytics 代理程式擴充功能可能會因為各種原因而失敗。 下一節說明在部署 Log Analytics 代理程式以進行 Windows 擴充時，可能會導致失敗的上架問題。

>[!NOTE]
>適用于 Windows 的 Log Analytics 代理程式是目前在 Microsoft Monitoring Agent 的 Azure 自動化中使用的名稱（MMA）。

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>案例：在 WebClient 要求期間發生例外狀況

VM 上的 Log Analytics for Windows 擴充功能無法與外部資源通訊，部署失敗。

#### <a name="issue"></a>問題

以下是傳回的錯誤訊息範例：

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>原因

此錯誤的一些可能原因包括：

* 在 VM 中設定的 proxy 只允許特定的埠。
* 防火牆設定已封鎖對必要連接埠和位址的存取。

#### <a name="resolution"></a>解決方案

確定您已開啟適當的連接埠和位址進行通訊。 如需埠和位址的清單，請參閱[規劃您的網路](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="scenario-install-failed-because-of-transient-environment-issues"></a><a name="transient-environment-issue"></a>案例：安裝因暫時性環境問題而失敗

因為有其他安裝或動作封鎖安裝，所以在部署期間安裝適用于 Windows 的 Log Analytics 擴充功能失敗。

#### <a name="issue"></a>問題

以下是可能傳回的錯誤訊息範例：

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>原因

此錯誤的一些可能原因包括：

* 另一個安裝正在進行中。
* 系統會在範本部署期間觸發以重新開機。

#### <a name="resolution"></a>解決方案

此錯誤本質上是暫時性的。 請重試部署以安裝延伸模組。

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>案例：安裝逾時

Windows 擴充功能的 Log Analytics 安裝未完成，因為有超時時間。

#### <a name="issue"></a>問題

以下是可能傳回的錯誤訊息範例：

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>原因

發生這種類型的錯誤是因為在安裝期間，VM 負載過重。

### <a name="resolution"></a>解決方案

當 VM 負載較低時，請嘗試安裝適用于 Windows 的 Log Analytics 代理程式擴充功能。

## <a name="next-steps"></a>後續步驟

如果您在這裡沒有看到您的問題，或無法解決您的問題，請嘗試下列其中一個通道以取得其他支援：

* 透過[Azure 論壇](https://azure.microsoft.com/support/forums/)取得 azure 專家的解答。
* 與[@AzureSupport](https://twitter.com/azuresupport)官方 Microsoft Azure 帳戶聯繫，以改善客戶體驗。 Azure 支援將 Azure 社區連接到解答、支援及專家。
* 提出 Azure 支援事件。 移至 [ [Azure 支援] 網站](https://azure.microsoft.com/support/options/)，然後選取 [**取得支援**]。
