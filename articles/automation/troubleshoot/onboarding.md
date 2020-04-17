---
title: 容錯排除載入 Azure 自動化管理解決方案
description: 了解如何針對將更新管理、變更追蹤和清查解決方案上線時的錯誤進行疑難排解
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ae359e5210a9a11c33dd3ff9b474e28aa2548c57
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536959"
---
# <a name="troubleshoot-errors-when-onboarding-update-management-change-tracking-and-inventory"></a>在載入更新管理、變更追蹤並排除錯誤

當您將更新管理或變更追蹤和清查等解決方案上線時，可能會發生錯誤。 本文描述可能發生的各種錯誤及解決方法。

## <a name="known-issues"></a>已知問題

### <a name="scenario-renaming-a-registered-node-requires-unregister--register-again"></a><a name="node-rename"></a>方案:重新取消註冊/註冊

#### <a name="issue"></a>問題

節點註冊到 Azure 自動化,然後更改作業系統電腦名稱。  來自節點的報告繼續以原始名稱顯示。

#### <a name="cause"></a>原因

重新命名註冊節點不會更新 Azure 自動化中的節點名稱。

#### <a name="resolution"></a>解決方案

從 Azure 自動化狀態配置中取消註冊節點,然後再次註冊。  在此之前發佈到服務的報告將不再可用。


### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>專案:不支援透過 Htcrics 代理重新簽署憑證

#### <a name="issue"></a>問題

客戶報告說,當通過終止 HTHT 的代理解決方案進行連接,然後使用新證書重新加密流量時,該服務不允許連接。

#### <a name="cause"></a>原因

Azure 自動化不支援重新簽署用於加密流量的證書。

#### <a name="resolution"></a>解決方案

此問題目前沒有任何因應措施。

## <a name="general-errors"></a>一般錯誤

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>機制:在訊息中加入失敗 ─ 無法啟用解決方案

#### <a name="issue"></a>問題

當您嘗試將虛擬機器登機到解決方案時,您會收到以下訊息之一:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>原因

此錯誤是由虛擬機、工作區或用戶的許可權不正確或缺少引起的。

#### <a name="resolution"></a>解決方案

確定您有正確的權限可讓虛擬機器上線。 檢閱[讓機器上線所需的權限](../automation-role-based-access-control.md#onboarding-permissions)，嘗試讓解決方案再次上線。 如果收到錯誤`The solution cannot be enabled on this VM because the permission to read the workspace is missing`,請確保具有尋找 VM`Microsoft.OperationalInsights/workspaces/read`是否已連接到工作區的許可權。

### <a name="scenario-onboarding-fails-with-the-message---failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>機制:在訊息登入失敗 ─ 無法設定診斷紀錄記錄的自動化帳戶

#### <a name="issue"></a>問題

當您嘗試讓虛擬機器在解決方案上線時，您會收到下列訊息：

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>原因

如果定價層與訂閱的計費模型不匹配,則可能導致此錯誤。 有關詳細資訊,請參閱[Azure 監視器 中的監視使用方式和估計成本](https://aka.ms/PricingTierWarning)。

#### <a name="resolution"></a>解決方案

手動創建日誌分析工作區,並重複載入過程以選擇創建的工作區。

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>案例：ComputerGroupQueryFormatError

#### <a name="issue"></a>問題

這個錯誤碼表示未正確地將用來將目標設定為解決方案的已儲存搜尋電腦群組查詢格式化。 

#### <a name="cause"></a>原因

您可能已變更查詢，或者系統可能已變更該查詢。

#### <a name="resolution"></a>解決方案

您可以刪除此解決方案的查詢，然後將解決方案重新上線，這樣會重新建立查詢。 您可以在工作區內的 [儲存的搜尋]**** 下方找到該查詢。 查詢的名稱是 **MicrosoftDefaultComputerGroup**，而查詢的類別是與這個查詢相關聯的解決方案名稱。 如果已啟用多個解決方案，**MicrosoftDefaultComputerGroup** 會在 [儲存的搜尋]**** 下方多次顯示。

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>案例：PolicyViolation

#### <a name="issue"></a>問題

這個錯誤碼表示部署因違反一個或多個原則而失敗。

#### <a name="cause"></a>原因 

有一個原則導致作業無法完成。

#### <a name="resolution"></a>解決方案

若要成功部署解決方案，您必須考量改變指定的原則。 由於可定義的原則有許多不同類型，因此所需的特定變更就要根據違反的原則來決定。 例如，如果在定義原則的資源群組中，拒絕對於該資源群組內特定類型資源之內容的變更權限，您可以執行下列任何一項作業：

* 完全移除原則。
* 嘗試上架到不同的資源群組。
* 透過下列方法修改原則，例如：
  * 將原則的目標重新設定為特定資源 (例如設定為特定的自動化帳戶)。
  * 對於原則已設定為拒絕的資源集合進行修改。

檢查 Azure 門戶右上角的通知,或導航到包含自動化帳戶的資源組,並在 **「設定」** 下選擇 **「部署**」以查看失敗的部署。 若要深入了解 Azure 原則，請造訪：[Azure 原則的概觀](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)。

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>方案:嘗試取消連結工作區的錯誤

#### <a name="issue"></a>問題

嘗試取消連結工作區時,您會收到以下錯誤:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>原因

當您的日誌分析工作區中仍有依賴於您的自動化帳戶和日誌分析工作區連結的解決方案處於活動狀態時,將發生此錯誤。

### <a name="resolution"></a>解決方案

要解決此問題,您需要從工作區中刪除以下解決方案(如果您正在使用):

* 更新管理
* 變更追蹤
* 於下班時間開始/停止 VM

刪除解決方案後,可以取消連結工作區。 從工作區和自動化帳戶中清理這些解決方案中的任何現有專案也很重要。  

* 更新管理
  * 從自動化帳戶中移除更新部署(計畫)
* 於下班時間開始/停止 VM
  * 在**設置** > **鎖**下刪除自動化帳戶中解決方案元件上的任何鎖。
  * 有關在非工作時間解決方案期間刪除開始/停止 VM 的其他步驟,請參閱[在非工作時間解決方案期間刪除"開始/停止"VM。](../automation-solution-vm-management.md#remove-the-solution)

## <a name="mma-extension-failures"></a><a name="mma-extension-failures"></a>MMA 延伸模組失敗

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

部署解決方案時，會部署各種相關的資源。 其中一個資源是「Microsoft Monitoring Agent 延伸模組」或「適用於 Linux 的 Log Analytics 代理程式」。 這些是虛擬機的來賓代理安裝的虛擬機器擴展,負責與配置的日誌分析工作區通訊,以便以後協調下載要載入的解決方案在開始執行後所依賴的二進位檔案和其他檔的下載。
您通常最先從出現在「通知中樞」中的通知察覺 MMA 或「適用於 Linux 的 Log Analytics 代理程式」安裝失敗。 按一下通知可提供特定失敗的進一步資訊。 巡覽至資源群組資源，再到其中的部署項目，也會提供所發生部署失敗的詳細資料。
MMA 或「適用於 Linux 的 Log Analytics 代理程式」可能因各種原因而安裝失敗，解決這些失敗所需採取的步驟會因問題而有所不同。 以下是特定疑難排解步驟。

以下部分介紹在載入時可能遇到的導致部署 MMA 擴展失敗的各種問題。

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>案例：在 WebClient 要求期間發生例外狀況

虛擬機器上的 MMA 延伸模組無法與外部資源通訊且部署會失敗。

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

* VM 中配置了一個僅允許特定埠的代理。

* 防火牆設定已封鎖對必要連接埠和位址的存取。

#### <a name="resolution"></a>解決方案

確定您已開啟適當的連接埠和位址進行通訊。 如需連接埠和位址清單，請參閱[規劃您的網路](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>方案:由於暫時性環境問題,安裝失敗

由於其他安裝或操作封鎖安裝,部署期間安裝 Microsoft 監視代理擴展失敗

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

* 其他安裝正在進行
* 系統在樣本部署期間觸發以重新啟動

#### <a name="resolution"></a>解決方案

此錯誤本質上是暫時性錯誤。 請重試部署以安裝延伸模組。

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>案例：安裝逾時

由於超時,MMA 擴展的安裝未完成。

#### <a name="issue"></a>問題

以下範例是可能傳回的錯誤訊息:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>原因

出現此錯誤的原因是虛擬機在安裝過程中負載很大。

### <a name="resolution"></a>解決方案

請在 VM 負載較低時，嘗試安裝 MMA 延伸模組。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答
* 與[@AzureSupport](https://twitter.com/azuresupport)— 正式的 Microsoft Azure 帳戶連接,通過將 Azure 社區連接到正確的資源(答案、支援和專家),從而改善客戶體驗。
* 如果需要更多協助，您可以提出 Azure 支援事件。 跳到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **「取得支援**」。
