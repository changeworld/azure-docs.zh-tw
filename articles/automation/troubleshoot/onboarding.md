---
title: 對 Azure 自動化管理解決方案的載入進行故障排除
description: 瞭解如何排除解決方案載入錯誤。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: da5152b459f54cbaae5ec168f103f23a237edebd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679224"
---
# <a name="troubleshoot-solution-onboarding"></a>容錯排除解決方案

在加入更新管理解決方案或更改追蹤和清單解決方案時,可能會收到錯誤。 本文介紹了可能發生的各種錯誤以及如何解決這些問題。

## <a name="known-issues"></a>已知問題

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>方案:重新命名註冊節點需要取消註冊或重新註冊

#### <a name="issue"></a>問題

節點註冊到 Azure 自動化,然後更改作業系統電腦名稱。 來自節點的報告繼續以原始名稱顯示。

#### <a name="cause"></a>原因

重新命名註冊節點不會更新 Azure 自動化中的節點名稱。

#### <a name="resolution"></a>解決方案

從 Azure 自動化狀態配置中取消註冊節點,然後再次註冊。 在此之前發佈到服務的報告將不再可用。

### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>專案:不支援透過 Htcrics 代理重新簽署憑證

#### <a name="issue"></a>問題

當通過終止 HTTPS 流量然後使用新證書重新加密流量的代理解決方案進行連接時,服務不允許連接。

#### <a name="cause"></a>原因

Azure 自動化不支援重新簽署用於加密流量的證書。

#### <a name="resolution"></a>解決方案

此問題當前沒有解決方法。

## <a name="general-errors"></a>一般錯誤

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>機制:在訊息中加入失敗 ─ 無法啟用解決方案

#### <a name="issue"></a>問題

當您嘗試將 VM 板載到解決方案時,您會收到以下訊息之一:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>原因

此錯誤是由 VM 或工作區或使用者的許可權不正確或缺少引起的。

#### <a name="resolution"></a>解決方案

確保您具有[在電腦上板所需的正確許可權](../automation-role-based-access-control.md#onboarding-permissions),然後嘗試再次將解決方案加入。 如果收到錯誤`The solution cannot be enabled on this VM because the permission to read the workspace is missing`,請確保具有尋找 VM`Microsoft.OperationalInsights/workspaces/read`是否已連接到工作區的許可權。

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>方案:使用訊息載入失敗:無法設定診斷紀錄記錄的自動化帳戶

#### <a name="issue"></a>問題

當您嘗試將 VM 連接到解決方案時,您會收到以下訊息:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>原因

如果定價層與訂閱的計費模型不匹配,則可能導致此錯誤。 請參考[Azure 監視器 中的監視使用情況和估計成本](https://aka.ms/PricingTierWarning)。

#### <a name="resolution"></a>解決方案

手動創建日誌分析工作區,並重複載入過程以選擇創建的工作區。

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>案例：ComputerGroupQueryFormatError

#### <a name="issue"></a>問題

此錯誤代碼表示用於定位解決方案的保存的搜索計算機組查詢格式不正確。 

#### <a name="cause"></a>原因

您可能更改了查詢,或者系統可能更改了查詢。

#### <a name="resolution"></a>解決方案

您可以刪除解決方案的查詢,然後再次將解決方案重新加入,從而重新創建查詢。 查詢可以在工作區中找到,在 **「已保存」搜索**下。 查詢的名稱為**MicrosoftDefaultComputerGroup**,查詢的類別是關聯解決方案的名稱。 如果啟用了多個解決方案 **,Microsoft 默認計算機組**查詢將多次顯示在 **「已儲存的搜尋**」下。

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>案例：PolicyViolation

#### <a name="issue"></a>問題

此錯誤代碼指示部署由於違反一個或多個策略而失敗。

#### <a name="cause"></a>原因 

策略阻止操作完成。

#### <a name="resolution"></a>解決方案

為了成功部署解決方案,必須考慮更改指示的策略。 由於可以定義許多不同類型的策略,因此所需的更改取決於所違反的策略。 例如,如果在資源組上定義了拒絕更改某些包含資源內容的許可權的策略,則可以選擇以下修補程式之一:

* 完全移除原則。
* 嘗試將解決方案添加到其他資源組。
* 將策略重新定向到特定資源,例如自動化帳戶。
* 修改策略配置為拒絕的資源集。

檢查 Azure 門戶右上角的通知,或導航到包含自動化帳戶的資源組,並在 **「設定」** 下選擇 **「部署**」以查看失敗的部署。 要瞭解有關 Azure 政策的詳細資訊,請參閱[Azure 政策概述](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json)。

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>方案:嘗試取消連結工作區的錯誤

#### <a name="issue"></a>問題

嘗試取消連結工作區時,您會收到以下錯誤:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>原因

當您的日誌分析工作區中仍有依賴於您的自動化帳戶和日誌分析工作區連結的解決方案處於活動狀態時,將發生此錯誤。

### <a name="resolution"></a>解決方案

如果使用以下解決方案,請從工作區中移除這些解決方案:

* 更新管理
* 變更追蹤與詳細目錄
* 於下班時間開始/停止 VM

刪除解決方案後,可以取消連結工作區。 從工作區和自動化帳戶中清除這些解決方案中的任何現有專案非常重要 

* 對於更新管理,請從自動化帳戶中刪除更新部署(計畫)。
* 對於非工作時間啟動/停止 VM,請刪除**設定** > **鎖**「下自動化帳戶中解決方案元件上的任何鎖。 請參閱[在非工作時間解決方案期間刪除開始/停止 VM。](../automation-solution-vm-management.md#remove-the-solution)

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Windows 延伸失敗的紀錄分析

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

由於各種原因,安裝 Windows 擴展的日誌分析代理可能會失敗。 以下部分介紹在部署 Windows 擴展的日誌分析代理期間可能導致故障的載入問題。

>[!NOTE]
>Windows 的日誌分析代理是 Microsoft 監視代理 (MMA) 的 Azure 自動化中目前使用的名稱。

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>案例：在 WebClient 要求期間發生例外狀況

VM 上的 Windows 日誌分析擴展無法與外部資源通信,並且部署失敗。

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

* 在 VM 中配置的代理僅允許特定埠。
* 防火牆設定已封鎖對必要連接埠和位址的存取。

#### <a name="resolution"></a>解決方案

確定您已開啟適當的連接埠和位址進行通訊。 如需連接埠和位址清單，請參閱[規劃您的網路](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>方案:由於暫時性環境問題,安裝失敗

由於其他安裝或操作封鎖安裝,在部署期間安裝 Windows 延伸的紀錄分析失敗

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
* 系統在範本部署期間觸發以重新啟動。

#### <a name="resolution"></a>解決方案

此錯誤本質上是暫時性的。 請重試部署以安裝延伸模組。

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>案例：安裝逾時

由於超時,Windows 擴展的日誌分析安裝未完成。

#### <a name="issue"></a>問題

以下是可能傳回的錯誤訊息的範例:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>原因

出現此類錯誤是因為 VM 在安裝過程中負載很大。

### <a name="resolution"></a>解決方案

當 VM 負載較低時,嘗試安裝 Windows 擴展的日誌分析代理。

## <a name="next-steps"></a>後續步驟

如果您在上面看不到問題或無法解決問題,請嘗試以下管道之一以獲取其他支援:

* 通過[Azure 論壇](https://azure.microsoft.com/support/forums/)從 Azure 專家那裡獲得答案。
* 與[@AzureSupport](https://twitter.com/azuresupport)連接 ,官方 Microsoft Azure 帳戶通過將 Azure 社區連接到正確的資源(答案、支援和專家)來改善客戶體驗。
* 提出 Azure 支援事件。 跳到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **「取得支援**」。
