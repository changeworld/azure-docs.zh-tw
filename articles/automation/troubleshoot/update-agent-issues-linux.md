---
title: 疑難排解 Azure 自動化中的 Linux 更新代理程式問題
description: 本文說明如何疑難排解並解決更新管理中的 Linux Windows 更新代理程式問題。
services: automation
ms.date: 01/25/2021
ms.topic: troubleshooting
ms.subservice: update-management
ms.openlocfilehash: 2fd92d79a3322b17f528194b9d39c26bf4c93b0c
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896252"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>針對 Linux 更新代理程式問題進行疑難排解

您的機器在更新管理中未顯示為就緒 (狀況良好)，可能有很多原因。 您可以檢查 Linux 混合式 Runbook 背景工作角色代理程式的健康狀態，以判斷根本問題。 下列是機器的三種整備狀態：

* 準備就緒：已部署混合式 Runbook 背景工作角色，距離上次出現時間不滿一小時。
* 已中斷連線：已部署混合式 Runbook 背景工作角色，距離上次出現時間超過一小時。
* 未設定：找不到混合式 Runbook 背景工作角色或尚未完成部署。

> [!NOTE]
> Azure 入口網站顯示的內容與機器目前的狀態間，可能略有延遲。

本文探討如何對 Azure 入口網站中的 Azure 機器以及[離線案例](#troubleshoot-offline)中的非 Azure 機器，執行疑難排解員。

> [!NOTE]
> 疑難排解員指令碼目前不會透過 Proxy 伺服器 (如已設定) 路由流量。

## <a name="start-the-troubleshooter"></a>啟動疑難排解員

若為 Azure 機器，請在入口網站的 [更新代理程式整備程度] 資料行下選取 **疑難排解** 連結，即可開啟 [對更新代理程式進行疑難排解] 頁面。 若為非 Azure 機器，則此連結會連往本文。 若要對非 Azure 機器進行疑難排解，請參閱＜離線疑難排解＞一節中的指示。

![VM 清單頁面](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> VM 必須處於執行中狀態，才能進行檢查。 若 VM 未執行，將會出現 [啟動 VM]。

在 [對更新代理程式進行疑難排解] 頁面上，選取 [執行檢查] 以啟動疑難排解員。 疑難排解員會使用[執行命令](../../virtual-machines/linux/run-command.md)，在機器上執行指令碼，以驗證相依性。 疑難排解員完成後，會傳回檢查結果。

![疑難排解頁面](../media/update-agent-issues-linux/troubleshoot-page.png)

當檢查完成後，會在視窗中傳回結果。 檢查區段會提供有關每個檢查所尋找項目的資訊。

![更新代理程式檢查頁面](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>先決條件檢查

### <a name="operating-system"></a>作業系統

作業系統檢查會驗證混合式 Runbook 背景工作角色是否正在執行下列其中一種作業系統。

|作業系統  |注意  |
|---------|---------|
|CentOS 6 (x86/x64) 和 7 (x64)      | Linux 代理程式必須能夠存取更新存放庫。 分類型修補需要 'yum' 傳回 CentOS 未內建的安全性資料。         |
|Red Hat Enterprise 6 (x86/x64) 和 7 (x64)     | Linux 代理程式必須能夠存取更新存放庫。        |
|SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)     | Linux 代理程式必須能夠存取更新存放庫。        |
|Ubuntu 14.04 LTS、16.04 LTS 和 18.04 LTS (x86/x64)      |Linux 代理程式必須能夠存取更新存放庫。         |

## <a name="monitoring-agent-service-health-checks"></a>監視代理程式服務健康情況檢查

### <a name="log-analytics-agent"></a>Log Analytics 代理程式

此檢查可確定已安裝適用於 Linux 的 Log Analytics 代理程式。 如需如何安裝它的相關指示，請參閱[安裝適用於 Linux 的代理程式](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux)。

### <a name="log-analytics-agent-status"></a>Log Analytics 代理程式狀態

此檢查確定適用於 Linux 的 Log Analytics 代理程式正在執行。 如果代理程式並未執行，您可以執行下列命令以嘗試重新啟動。 如需如何對代理程式進行疑難排解的詳細資訊，請參閱 [Linux - 針對混合式 Runbook 背景工作角色問題進行疑難排解](hybrid-runbook-worker.md#linux)。

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>多路連接

此檢查會判斷代理程式是否正在回報給多個工作區。 更新管理不支援多路連接。

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

此檢查會證適用於 Linux 的 Log Analytics 代理程式，是否具有混合式 Runbook 背景工作角色套件。 更新管理需要有此封裝才能運作。 若要深入了解，請參閱[適用於 Linux 的 Log Analytics 代理程式未執行](hybrid-runbook-worker.md#oms-agent-not-running)。

更新管理會從作業端點下載混合式 Runbook 背景工作角色套件。 因此，若混合式 Runbook 背景工作角色未執行，而且[作業端點](#operations-endpoint)檢查失敗，則更新會失敗。

### <a name="hybrid-runbook-worker-status"></a>混合式 Runbook 背景工作狀態

此檢查確保混合式 Runbook 背景工作正在機器上執行。 若混合式 Runbook 背景工作角色正常執行，則應會出現下列範例的處理序。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>連線能力檢查

### <a name="general-internet-connectivity"></a>一般網際網路連線

此檢查確保機器具有網際網路存取權。

### <a name="registration-endpoint"></a>註冊端點

此檢查會判斷混合式 Runbook 背景工作角色是否能夠正常地與 Log Analytics 工作區中的 Azure 自動化進行通訊。

Proxy 和防火牆設定必須允許「混合式 Runbook 背景工作角色」代理程式與註冊端點進行通訊。 如需打算開啟的位址與連接埠清單，請參閱[網路規劃](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="operations-endpoint"></a>作業端點

此檢查會判斷 Log Analytics 代理程式是否能夠正確地與 Job Runtime Data Service (作業執行階段資料服務) 進行通訊。

Proxy 和防火牆設定必須允許「混合式 Runbook 背景工作角色」代理程式與「作業執行階段資料服務」進行通訊。 如需打算開啟的位址與連接埠清單，請參閱[網路規劃](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="log-analytics-endpoint-1"></a>Log Analytics 端點 1

此檢查會確認您的機器有權存取 Log Analytics 代理程式所需的端點。

### <a name="log-analytics-endpoint-2"></a>Log Analytics 端點 2

此檢查會確認您的機器有權存取 Log Analytics 代理程式所需的端點。

### <a name="log-analytics-endpoint-3"></a>Log Analytics 端點 3

此檢查會確認您的機器有權存取 Log Analytics 代理程式所需的端點。

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>離線疑難排解

您可以藉由在本機執行指令碼，在「混合式 Runbook 背景工作角色」上離線使用疑難排解員。 您可以在 GitHub 中找到 Python 腳本[UM_Linux_Troubleshooter_Offline .py。](https://github.com/Azure/updatemanagement/blob/main/UM_Linux_Troubleshooter_Offline.py) 以下範例顯示此指令碼的輸出範例：

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>後續步驟

[針對混合式 Runbook 背景工作角色問題進行疑難排解](hybrid-runbook-worker.md)。
