---
title: 針對 Azure 自動化功能部署問題進行疑難排解
description: 本文說明如何針對部署 Azure 自動化功能時發生的問題進行疑難排解和解決。
services: automation
ms.date: 06/30/2020
ms.topic: conceptual
ms.service: automation
ms.openlocfilehash: ca2f866dc882e003469163a22d32d3d72031443a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85801024"
---
# <a name="troubleshoot-feature-deployment-issues"></a>針對功能部署問題進行疑難排解

您在 VM 上部署 Azure 自動化更新管理功能或變更追蹤和清查功能時，可能會收到錯誤訊息。 本文描述可能發生的錯誤及解決方法。

## <a name="known-issues"></a>已知問題

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>案例：重新命名已註冊的節點時，需要再次取消註冊或註冊

#### <a name="issue"></a>問題

系統會向 Azure 自動化註冊節點，然後再變更作業系統電腦名稱。 節點中的報告會繼續以原始名稱出現。

#### <a name="cause"></a>原因

重新命名已註冊的節點，並不會更新 Azure 自動化中的節點名稱。

#### <a name="resolution"></a>解決方案

從 Azure 自動化狀態設定取消註冊節點，然後再次註冊。 將無法再使用於之前發佈至服務的報告。

### <a name="scenario-re-signing-certificates-via-https-proxy-isnt-supported"></a><a name="resigning-cert"></a>案例：不支援透過 HTTPS Proxy 重新簽署憑證

#### <a name="issue"></a>問題

透過會終止 HTTPS 流量的 Proxy 進行連線，然後使用新的憑證重新加密流量時，服務不會允許連線。

#### <a name="cause"></a>原因

Azure 自動化不支援重新簽署用來加密流量的憑證。

#### <a name="resolution"></a>解決方案

此問題目前沒有任何因應措施。

## <a name="general-errors"></a>一般錯誤

### <a name="scenario-feature-deployment-fails-with-the-message-the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>案例：功能部署失敗並顯示以下訊息：「無法啟用解決方案」

#### <a name="issue"></a>問題

嘗試在 VM 上啟用功能時，會收到下列其中一則訊息：

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>原因

此錯誤是由於 VM 或工作區的權限不正確或遺失所造成。

#### <a name="resolution"></a>解決方案

請確定您有正確的[功能部署權限](../automation-role-based-access-control.md#feature-setup-permissions)，然後再次嘗試部署該功能。 如果您收到錯誤訊息 `The solution cannot be enabled on this VM because the permission to read the workspace is missing` ，請參閱下列[疑難排解資訊](update-management.md#failed-to-enable-error)。

### <a name="scenario-feature-deployment-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>案例：功能部署失敗，並出現「無法設定自動化帳戶以進行診斷記錄」訊息

#### <a name="issue"></a>問題

您嘗試在 VM 上啟用功能時，您會收到下列訊息：

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>原因

這是會由於定價層與訂閱的計費模型不符合而造成的錯誤。 如需詳細資訊，請參閱[在 Azure 監視器中監視使用量和估計成本](https://aka.ms/PricingTierWarning)。

#### <a name="resolution"></a>解決方案

手動建立您的 Log Analytics 工作區，並重複此功能部署程式，以選取所建立的工作區。

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>案例：ComputerGroupQueryFormatError

#### <a name="issue"></a>問題

這個錯誤碼表示，對於用來將目標設定為功能的已儲存搜尋電腦群組查詢，並未正確予以格式化。 

#### <a name="cause"></a>原因

您可能已改變查詢，或者系統可能已改變該查詢。

#### <a name="resolution"></a>解決方案

您可以刪除該功能的查詢，然後再次啟用該功能，即會重新建立查詢。 您可以在工作區內的 [儲存的搜尋] 下方找到該查詢。 查詢的名稱是 **MicrosoftDefaultComputerGroup**，而查詢的類別是相關聯功能的名稱。 如果已啟用多個功能，**MicrosoftDefaultComputerGroup** 查詢會在 [儲存的搜尋] 下方多次顯示。

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>案例：PolicyViolation

#### <a name="issue"></a>問題

這個錯誤碼指出部署因違反一個或多個原則而失敗。

#### <a name="cause"></a>原因 

原則導致作業無法完成。

#### <a name="resolution"></a>解決方案

若要成功部署功能，必須考慮改變指定的原則。 由於可定義的原則有許多不同類型，因此所需的變更就要根據違反的原則來決定。 例如，如果在資源群組上定義原則，但拒絕變更某些包含資源內容的權限，您可以選擇下列其中一個修正：

* 完全移除原則。
* 嘗試為不同的資源群組啟用此功能。
* 將原則的目標重定為特定資源，例如自動化帳戶。
* 對於原則已設定為拒絕的資源集合進行修改。

檢查 Azure 入口網站右上角的通知，或移至包含您自動化帳戶的資源群組，然後選取 [設定] 下方的 [部署] 來檢視失敗的部署。 若要深入了解 Azure 原則，請參閱 [Azure 原則的概觀](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)。

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>案例：嘗試取消連結工作區時發生錯誤

#### <a name="issue"></a>問題

您嘗試取消工作區的連結時，收到下列錯誤訊息：

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>原因

您的 Log Analytics 工作區中仍有使用中的功能時，會發生此錯誤 (取決於您的自動化帳戶和連結的 Log Analytics 工作區)。

### <a name="resolution"></a>解決方案

如果您要使用下列功能，請從您的工作區中移除這些資源：

* 更新管理
* 變更追蹤與詳細目錄
* 於下班時間開始/停止 VM

移除功能資源之後，您可以取消連結您的工作區。 請務必從您的工作區和您的自動化帳戶清除這些功能的任何現有成品：

* 針對更新管理，請從您的自動化帳戶移除**更新部署 (排程)** 。
* 若要在停機期間啟動/停止 VM，請在 [設定] > [鎖定] 下，移除自動化帳戶中功能元件的任何鎖定。 如需詳細資訊，請參閱[移除功能](../automation-solution-vm-management.md#remove-the-feature)。

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>適用於 Windows 的 Log Analytics擴充功能失敗

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

安裝適用於 Windows 的 Log Analytics 程式擴充功能可能會因為各種原因而失敗。 下一節描述在部署適用於 Windows 的 Log Analytics 代理程式期間，可能會導致失敗的功能部署問題。

>[!NOTE]
>適用於 Windows 的 Log Analytics 代理程式是目前在 Microsoft Monitoring Agent (MMA) 的 Azure 自動化中使用的名稱。

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>案例：在 WebClient 要求期間發生例外狀況

VM 上適用於 Windows 的 Log Analytics 延伸模組無法與外部資源通訊，且部署會失敗。

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

* VM 中已設定只允許特定連接埠的 Proxy。
* 防火牆設定已封鎖對必要連接埠和位址的存取。

#### <a name="resolution"></a>解決方案

確定您已開啟適當的連接埠和位址進行通訊。 如需連接埠和位址清單，請參閱[規劃您的網路](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="scenario-install-failed-because-of-transient-environment-issues"></a><a name="transient-environment-issue"></a>案例：安裝因暫時性環境問題而失敗

由於其他安裝或動作封鎖安裝，在部署期間安裝適用於 Windows 的 Log Analytic s擴充功能會失敗。

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

* 其他安裝正在進行。
* 在範本部署期間已觸發系統重新啟動。

#### <a name="resolution"></a>解決方案

此錯誤在本質上是暫時性的。 請重試部署以安裝延伸模組。

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>案例：安裝逾時

適用於 Windows 的 Log Analytics 功能因為逾時之故，並未完成安裝。

#### <a name="issue"></a>問題

以下是可能傳回的錯誤訊息範例：

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>原因

發生這種類型的錯誤是因為在安裝期間，VM 負載過重。

### <a name="resolution"></a>解決方案

VM 負載較低時，請嘗試安裝適用於 Windows 的 Log Analytic s代理程式擴充功能。

## <a name="next-steps"></a>後續步驟

如果您在這裡沒有看到您的問題，或無法解決您的問題，請嘗試下列其中一個管道以取得其他支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答。
* 連線至 [@AzureSupport](https://twitter.com/azuresupport)，這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 Azure 支援會將 Azure 社群連線到解答、支援及專家。
* 提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
