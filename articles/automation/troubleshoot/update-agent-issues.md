---
title: 針對 Azure 自動化更新管理中的 Windows update 代理程式問題進行疑難排解
description: 瞭解如何使用更新管理解決方案，針對 Windows update 代理程式進行疑難排解及解決問題。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 6983a2ac7ab5fafcb00aee0b72221a8540ea1668
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678980"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>針對 Windows update 代理程式問題進行疑難排解

在更新管理中，您的電腦未顯示為 [就緒] （狀況良好）的原因可能有很多。 在更新管理中，您可以檢查混合式 Runbook 背景工作角色代理程式的健康情況，以判斷根本問題。 本文討論如何在[離線案例](#troubleshoot-offline)中，從 Azure 入口網站和非 azure 機器執行 Azure 機器的疑難排解員。

以下是電腦的三種就緒狀態：

* 就緒-混合式 Runbook 背景工作角色已部署，且上次出現時間不到1小時前。
* 已中斷連線-混合式 Runbook 背景工作角色已部署，且上次在1小時前出現。
* 未設定-找不到混合式 Runbook 背景工作角色，或尚未完成上架。

> [!NOTE]
> Azure 入口網站顯示的內容與電腦目前的狀態之間可能會有些許延遲。

## <a name="start-the-troubleshooter"></a>啟動疑難排解員

若為 Azure 機器，在入口網站中按一下 [更新代理程式整備程度]**** 資料行底下的 [疑難排解]**** 連結，即可啟動 [對更新代理程式進行疑難排解] 頁面。 針對非 Azure 機器，此連結會將您帶到這篇文章。 請參閱[離線指示](#troubleshoot-offline)以對非 Azure 機器進行疑難排解。

![虛擬機器的更新管理清單](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> 若要檢查混合式 Runbook 背景工作角色的健全狀況，VM 必須正在執行。 如果 VM 未執行，將會出現 [啟動 VM]**** 按鈕。

在 [對更新代理程式進行疑難排解] 頁面上，選取 [執行檢查]**** 以啟動疑難排解員。 疑難排解員會使用[執行命令](../../virtual-machines/windows/run-command.md)在電腦上執行腳本，以確認相依性。 疑難排解員完成後，會傳回檢查結果。

![對更新代理程式進行疑難排解頁面](../media/update-agent-issues/troubleshoot-page.png)

結果準備就緒時就會顯示在頁面上。 檢查區段會顯示每項檢查所包含的項目。

![對更新代理程式進行疑難排解的檢查](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>先決條件檢查

### <a name="operating-system"></a>作業系統

作業系統檢查會確認「混合式 Runbook 背景工作角色」是否正在執行下列其中一個作業系統：

|作業系統  |備忘錄  |
|---------|---------|
|Windows Server 2012 和更新版本 |需要 .NET Framework 4.6 或更新版本。 ([下載 .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> 需要 Windows PowerShell 5.1。  ([下載 Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-462"></a>.NET 4.6.2

.NET Framework 檢查會確認系統是否已安裝最少的[.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) 。

### <a name="wmf-51"></a>WMF 5.1

WMF 檢查會確認系統是否具有所需的 Windows Management Framework （WMF）版本- [Windows Management framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616)。

### <a name="tls-12"></a>TLS 1.2

此檢查會判斷您是否使用 TLS 1.2 來加密通訊。 此平台已不再支援 TLS 1.0。 建議用戶端使用 TLS 1.2 與「更新管理」進行通訊。

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

### <a name="machinekeys-folder-access"></a>MachineKeys 資料夾存取

Crypto 資料夾存取權檢查會判斷「本機系統帳戶」是否能夠存取 C:\ProgramData\Microsoft\Crypto\RSA。

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>離線疑難排解

您可以藉由在本機執行指令碼，對「混合式 Runbook 背景工作角色」使用疑難排解員。 您可以在「PowerShell 資源庫」中取得 [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration) 指令碼。 您必須安裝 WMF 4.0 或更新版本，才能執行腳本。 若要下載最新版本的 PowerShell，請參閱[安裝各種版本的 powershell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell)。

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

若要對「混合式 Runbook 背景工作角色」的其他問題進行疑難排解，請參閱[對混合式 Runbook 背景工作角色進行疑難排解](hybrid-runbook-worker.md)。
