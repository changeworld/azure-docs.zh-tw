---
title: 針對 Azure 自動化更新管理中的 Windows update 代理程式問題進行疑難排解
description: 瞭解如何使用更新管理解決方案來疑難排解和解決 Windows update 代理程式的問題。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 1b4467128fae3fd71a6e588e3c05d287c153e168
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927882"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>針對 Windows update 代理程式問題進行疑難排解

在更新管理中，您的電腦未顯示為 [就緒] （狀況良好）的原因可能有很多。 您可以檢查 Windows 混合式 Runbook 背景工作角色代理程式的健全狀況，以判斷根本問題。 以下是電腦的三種就緒狀態：

* 就緒：混合式 Runbook 背景工作角色已部署，且上次出現時間不到一小時前。
* 已中斷連線：混合式 Runbook 背景工作角色已部署，且上次在一小時前出現。
* 未設定：找不到混合式 Runbook 背景工作角色，或尚未完成上架。

> [!NOTE]
> Azure 入口網站顯示的內容與電腦目前的狀態之間可能會有些許延遲。

本文討論如何在[離線案例](#troubleshoot-offline)中，從 Azure 入口網站和非 Azure 機器執行 azure 機器的疑難排解員。 疑難排解員現在包含 Windows Server Update Services （WSUS）和自動下載和 install 金鑰的檢查。

> [!NOTE]
> 疑難排解員腳本目前不會透過 proxy 伺服器來路由傳送流量（如果已設定的話）。

## <a name="start-the-troubleshooter"></a>啟動疑難排解員

針對 Azure 機器，您可以在入口網站中，選取 [**更新代理程式準備就緒**] 欄底下的 [**疑難排解**] 連結，以啟動 [**疑難排解更新代理程式**] 頁面。 針對非 Azure 機器，此連結會將您帶到這篇文章。 請參閱[離線指示](#troubleshoot-offline)以對非 Azure 機器進行疑難排解。

![虛擬機器更新管理清單的螢幕擷取畫面](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> 若要檢查混合式 Runbook 背景工作角色的健全狀況，VM 必須正在執行。 如果 VM 未執行，將會出現 [啟動 VM]**** 按鈕。

在 [對更新代理程式進行疑難排解]**** 頁面上，選取 [執行檢查]**** 以啟動疑難排解員。 疑難排解員會使用[執行命令](../../virtual-machines/windows/run-command.md)在電腦上執行腳本，以確認相依性。 疑難排解員完成後，會傳回檢查結果。

![[疑難排解更新代理程式] 頁面的螢幕擷取畫面](../media/update-agent-issues/troubleshoot-page.png)

結果準備就緒時就會顯示在頁面上。 檢查區段會顯示每項檢查所包含的項目。

![疑難排解更新代理程式檢查的螢幕擷取畫面](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>先決條件檢查

### <a name="operating-system"></a>作業系統

作業系統檢查會確認「混合式 Runbook 背景工作角色」是否正在執行下列其中一個作業系統：

|作業系統  |注意  |
|---------|---------|
|Windows Server 2012 和更新版本 |需要 .NET Framework 4.6 或更新版本。 （[下載 .NET Framework](/dotnet/framework/install/guide-for-developers)）。<br/> 需要 Windows PowerShell 5.1。  （[下載 Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616)。）        |

### <a name="net-462"></a>.NET 4.6.2

.NET Framework 檢查會確認系統是否已安裝最少的[.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) 。

### <a name="wmf-51"></a>WMF 5.1

WMF 檢查會確認系統是否具有所需的 Windows Management Framework （WMF）版本： [Windows Management framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616)。

### <a name="tls-12"></a>TLS 1.2

此檢查會判斷您是否使用 TLS 1.2 來加密通訊。 此平台已不再支援 TLS 1.0。 使用 TLS 1.2 與更新管理進行通訊。

## <a name="connectivity-checks"></a>連線能力檢查

### <a name="registration-endpoint"></a>註冊端點

此檢查會判斷代理程式是否能夠正確地與代理程式服務進行通訊。

Proxy 和防火牆設定必須允許「混合式 Runbook 背景工作角色」代理程式與註冊端點進行通訊。 如需要開啟的位址和埠清單，請參閱混合式背景[工作角色的網路規劃](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="operations-endpoint"></a>作業端點

此檢查會判斷代理程式是否能夠正確地與「作業執行階段資料服務」進行通訊。

Proxy 和防火牆設定必須允許「混合式 Runbook 背景工作角色」代理程式與「作業執行階段資料服務」進行通訊。 如需要開啟的位址和埠清單，請參閱混合式背景[工作角色的網路規劃](../automation-hybrid-runbook-worker.md#network-planning)。

## <a name="vm-service-health-checks"></a>VM 服務健康情況檢查

### <a name="monitoring-agent-service-status"></a>監視代理程式服務狀態

此檢查會判斷電腦上是否正在執行適用于`healthservice`Windows 的 Log Analytics 代理程式（）。 若要深入瞭解如何對服務進行疑難排解，請參閱[適用于 Windows 的 Log Analytics 代理程式未執行](hybrid-runbook-worker.md#mma-not-running)。

若要重新安裝適用于 Windows 的 Log Analytics 代理程式，請參閱[安裝和設定適用于 windows 的 Log analytics 代理程式](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)。

### <a name="monitoring-agent-service-events"></a>監視代理程式服務事件

這項檢查會判斷電腦上的 Azure Operations Manager 記錄檔中是否有任何4502事件出現在過去24小時內。

若要深入了解此事件，請參閱此事件的[疑難排解指南](hybrid-runbook-worker.md#event-4502)。

## <a name="access-permissions-checks"></a>存取權限檢查

### <a name="crypto-folder-access"></a>加密資料夾存取

加密資料夾存取檢查會判斷本機系統帳戶是否具有 C:\ProgramData\Microsoft\Crypto\RSA. 的存取權

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>離線疑難排解

您可以透過在本機執行腳本，在混合式 Runbook 背景工作角色離線使用疑難排解員。 從 PowerShell 資源庫取得下列腳本：[疑難排解-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration)。 若要執行腳本，您必須安裝 WMF 4.0 或更新版本。 若要下載最新版本的 PowerShell，請參閱[安裝各種版本的 powershell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell)。

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

[混合式 Runbook 背景工作的疑難排解](hybrid-runbook-worker.md)
