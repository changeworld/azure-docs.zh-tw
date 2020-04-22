---
title: 在 Azure 自動化更新管理中解決 Windows 更新代理問題
description: 瞭解如何使用更新管理解決方案解決 Windows 更新代理的問題。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 6983a2ac7ab5fafcb00aee0b72221a8540ea1668
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678980"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>解決 Windows 更新代理問題

在更新管理中,計算機未顯示為就緒(正常)的原因有很多。 在更新管理中,可以檢查混合 Runbook 工作線程代理的運行狀況以確定基礎問題。 本文討論如何在[離線方案中](#troubleshoot-offline)從 Azure 門戶和非 Azure 電腦運行 Azure 電腦的疑難解答。

以下是電腦的三個就緒狀態:

* 就緒 - 部署了混合 Runbook 工作線程,最後一次出現不到 1 小時。
* 已斷開連接 - 已部署混合 Runbook 工作線程,最後一次出現是在 1 小時前。
* 未配置 - 找不到混合 Runbook 工作線程或尚未完成載入。

> [!NOTE]
> Azure 門戶顯示的內容與計算機的當前狀態之間可能會有輕微的延遲。

## <a name="start-the-troubleshooter"></a>啟動疑難排解員

若為 Azure 機器，在入口網站中按一下 [更新代理程式整備程度]**** 資料行底下的 [疑難排解]**** 連結，即可啟動 [對更新代理程式進行疑難排解] 頁面。 對於非 Azure 計算機,該連結將帶您到本文。 請參閱[離線說明](#troubleshoot-offline)以排除非 Azure 電腦故障。

![虛擬機器的更新管理清單](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> 要檢查混合 Runbook 輔助角色的運行狀況,VM 必須正在運行。 如果 VM 未執行，將會出現 [啟動 VM]**** 按鈕。

在 [對更新代理程式進行疑難排解] 頁面上，選取 [執行檢查]**** 以啟動疑難排解員。 疑難解答使用[Run 命令](../../virtual-machines/windows/run-command.md)在電腦上運行文本以驗證依賴項。 疑難排解員完成後，會傳回檢查結果。

![對更新代理程式進行疑難排解頁面](../media/update-agent-issues/troubleshoot-page.png)

結果準備就緒時就會顯示在頁面上。 檢查區段會顯示每項檢查所包含的項目。

![對更新代理程式進行疑難排解的檢查](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>先決條件檢查

### <a name="operating-system"></a>作業系統

操作系統檢查驗證混合 Runbook 輔助角色是否執行以下作業系統之一:

|作業系統  |注意  |
|---------|---------|
|Windows 伺服器 2012 及更高版本 |.NET 框架 4.6 或更高版本是必需的。 ([下載 .NET Framework](/dotnet/framework/install/guide-for-developers))<br/> 需要 Windows PowerShell 5.1。  ([下載 Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-462"></a>.NET 4.6.2

.NET 框架檢查驗證系統是否安裝了至少[.NET 框架 4.6.2。](https://www.microsoft.com/en-us/download/details.aspx?id=53345)

### <a name="wmf-51"></a>WMF 5.1

WMF 檢查驗證系統是否具有 Windows 管理框架 (WMF) - [Windows 管理框架 5.1](https://www.microsoft.com/download/details.aspx?id=54616)的必需版本。

### <a name="tls-12"></a>TLS 1.2

此檢查會判斷您是否使用 TLS 1.2 來加密通訊。 此平台已不再支援 TLS 1.0。 建議用戶端使用 TLS 1.2 與「更新管理」進行通訊。

## <a name="connectivity-checks"></a>連線能力檢查

### <a name="registration-endpoint"></a>註冊端點

此檢查會判斷代理程式是否能夠正確地與代理程式服務進行通訊。

Proxy 和防火牆設定必須允許「混合式 Runbook 背景工作角色」代理程式與註冊端點進行通訊。 有關要開啟的位址和連接埠的清單,請參閱[混合輔助角色的網路規劃](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="operations-endpoint"></a>作業端點

此檢查會判斷代理程式是否能夠正確地與「作業執行階段資料服務」進行通訊。

Proxy 和防火牆設定必須允許「混合式 Runbook 背景工作角色」代理程式與「作業執行階段資料服務」進行通訊。 有關要開啟的位址和連接埠的清單,請參閱[混合輔助角色的網路規劃](../automation-hybrid-runbook-worker.md#network-planning)。

## <a name="vm-service-health-checks"></a>VM 服務健康情況檢查

### <a name="monitoring-agent-service-status"></a>監視代理程式服務狀態

此檢查確定 Windows 的日誌`healthservice`分析代理 ( ) 是否在電腦上運行。 要瞭解有關對服務進行故障排除的更多資訊,請參閱[Windows 的紀錄分析代理未執行](hybrid-runbook-worker.md#mma-not-running)。

要重新安裝 Windows 的紀錄分析代理,請參閱[安裝和設定 Windows 的紀錄分析代理](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)。

### <a name="monitoring-agent-service-events"></a>監視代理程式服務事件

此檢查確定在過去 24 小時內,Azure 操作管理器日誌中是否顯示任何 4502 事件。

若要深入了解此事件，請參閱此事件的[疑難排解指南](hybrid-runbook-worker.md#event-4502)。

## <a name="access-permissions-checks"></a>存取權限檢查

### <a name="machinekeys-folder-access"></a>MachineKeys 資料夾存取

Crypto 資料夾存取權檢查會判斷「本機系統帳戶」是否能夠存取 C:\ProgramData\Microsoft\Crypto\RSA。

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>離線疑難排解

您可以藉由在本機執行指令碼，對「混合式 Runbook 背景工作角色」使用疑難排解員。 您可以在「PowerShell 資源庫」中取得 [Troubleshoot-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration) 指令碼。 您必須安裝 WMF 4.0 或更高,才能執行文稿。 要下載最新版本的 PowerShell,請參閱[安裝各種版本的 PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell)。

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
