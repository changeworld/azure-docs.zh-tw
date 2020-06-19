---
title: 針對 Azure 自動化中的 Windows 更新代理程式問題進行疑難排解
description: 本文說明如何在更新管理期間對 Windows 更新代理程式進行疑難排解及解決問題。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: ff996227e23836bf85cc3885d9184ae6d7d6c61d
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680837"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>針對 Windows 更新代理程式問題進行疑難排解

電腦在更新管理部署期間未顯示為就緒 (狀況良好)，可能有很多原因。 您可檢查 Windows 混合式 Runbook 背景工作角色代理程式的健康狀態以判斷根本問題。 下列是電腦的三種整備狀態：

* 準備就緒：混合式 Runbook 背景工作角色已部署，上次出現時間為不到一小時前。
* 已中斷連線：混合式 Runbook 背景工作角色已部署，上次出現時間為超過一小時前。
* 未設定：找不到混合式 Runbook 背景工作角色或尚未完成部署。

> [!NOTE]
> Azure 入口網站所顯示的內容，與電腦目前的狀態之間可能會有一點延遲。

本文探討如何以 Azure 入口網站與非 Azure 機器在[離線情況](#troubleshoot-offline)下執行 Azure 機器的疑難排解員。 

> [!NOTE]
> 疑難排解指令碼現在包含 Windows Server Update Services (WSUS) 和自動下載並安裝金鑰的檢查。 

## <a name="start-the-troubleshooter"></a>啟動疑難排解員

針對 Azure 機器，您可選取 [更新代理程式整備程度] 資料行下的 [疑難排解] 連結，即可啟動 [對更新代理程式進行疑難排解] 頁面。 針對非 Azure 機器，此連結會連往本文。 若要對非 Azure 機器進行疑難排解，請參閱[離線疑難排解](#troubleshoot-offline)。

![虛擬機器的更新管理清單螢幕擷取畫面](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> 若要檢查混合式 Runbook 背景工作角色的健康狀態，則必須執行 VM。 如果 VM 未執行，將會出現 [啟動 VM] 按鈕。

在 [對更新代理程式進行疑難排解] 頁面上，選取 [執行檢查] 以啟動疑難排解員。 疑難排解員會使用[執行命令](../../virtual-machines/windows/run-command.md)在機器上執行指令碼，以驗證相依性。 疑難排解員完成後，會傳回檢查結果。

![對更新代理程式進行疑難排解頁面的螢幕擷取畫面](../media/update-agent-issues/troubleshoot-page.png)

結果準備就緒時就會顯示在頁面上。 檢查區段會顯示每項檢查所包含的項目。

![對更新代理程式進行疑難排解檢查的螢幕擷取畫面](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>先決條件檢查

### <a name="operating-system"></a>作業系統

作業系統檢查會確認「混合式 Runbook 背景工作角色」是否正在執行下列其中一個作業系統，如下表所示。

|作業系統  |注意  |
|---------|---------|
|Windows Server 2012 及更新版本 |需要 .NET Framework 4.6 或更新版本。 ([下載 .NET Framework](/dotnet/framework/install/guide-for-developers)。)<br/> 需要 Windows PowerShell 5.1。  ([下載 Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616)。)        |

### <a name="net-462"></a>.NET 4.6.2

.NET Framework 檢查會驗證系統已安裝 [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) 或更新版本。

### <a name="wmf-51"></a>WMF 5.1

WMF 檢查會驗證系統具有所需版本的 Windows Management Framework (WMF)，亦即 [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616)。

### <a name="tls-12"></a>TLS 1.2

此檢查會判斷您是否使用 TLS 1.2 來加密通訊。 此平台已不再支援 TLS 1.0。 使用 TLS 1.2 與更新管理進行通訊。

## <a name="connectivity-checks"></a>連線能力檢查

### <a name="registration-endpoint"></a>註冊端點

此檢查會判斷代理程式是否能夠正確地與代理程式服務進行通訊。

Proxy 和防火牆設定必須允許「混合式 Runbook 背景工作角色」代理程式與註冊端點進行通訊。 如需要開放的位址和連接埠清單，請參閱[網路規劃](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="operations-endpoint"></a>作業端點

此檢查會判斷代理程式是否能夠正確地與「作業執行階段資料服務」進行通訊。

Proxy 和防火牆設定必須允許「混合式 Runbook 背景工作角色」代理程式與「作業執行階段資料服務」進行通訊。 如需要開放的位址和連接埠清單，請參閱[網路規劃](../automation-hybrid-runbook-worker.md#network-planning)。

## <a name="vm-service-health-checks"></a>VM 服務健康情況檢查

### <a name="monitoring-agent-service-status"></a>監視代理程式服務狀態

此檢查會判斷適用於 Windows 的 Log Analytics 代理程式 (`healthservice`) 是否正在電腦上執行。 若要深入了解如何對此服務進行疑難排解，請參閱[適用於 Windows 的 Log Analytics 代理程式未執行](hybrid-runbook-worker.md#mma-not-running)。

若要重新安裝適用於 Windows 的 Log Analytics 代理程式，請參閱[安裝適用於 Windows 的代理程式](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)。

### <a name="monitoring-agent-service-events"></a>監視代理程式服務事件

此檢查會判斷在機器上的 Azure Operations Manager 記錄中，在過去 24 小時內是否有任何 4502 事件。

若要深入了解此事件，請參閱此事件 [Operations Manager 記錄中的 4502 事件](hybrid-runbook-worker.md#event-4502)。

## <a name="access-permissions-checks"></a>存取權限檢查

> [!NOTE]
> 疑難排解員目前不會透過 Proxy 伺服器 (如果已設定) 來路由傳送流量。

### <a name="crypto-folder-access"></a>加密資料夾存取權

Crypto 資料夾存取權檢查會判斷本機系統帳戶是否能夠存取 C:\ProgramData\Microsoft\Crypto\RSA。

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>離線疑難排解

您可以藉由在本機執行指令碼，對「混合式 Runbook 背景工作角色」使用疑難排解員。 從 PowerShell 資源庫取得下列指令碼：[Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration). 若要執行此指令碼，您必須安裝 WMF 4.0 或更新版本。 若要下載最新版的 PowerShell，請參閱[安裝各種版本的 PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell)。

此指令碼的輸出會如下列範例所示：

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Log Analytics for Windows service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Log Analytics agent for Windows, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Log Analytics agent for Windows service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Log Analytics agent for Windows, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>後續步驟

[針對混合式 Runbook 背景工作角色問題進行疑難排解](hybrid-runbook-worker.md)。
