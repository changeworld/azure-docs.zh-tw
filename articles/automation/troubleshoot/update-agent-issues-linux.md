---
title: 診斷 Linux 混合式 Runbook 背景工作-Azure 更新管理
description: 瞭解如何針對支援更新管理的 Linux 上的 Azure 自動化混合式 Runbook 背景工作角色進行疑難排解及解決問題。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: e60ba71607b99f0ea97e0725ffdd0740f3e9c579
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372842"
---
# <a name="understand-and-resolve-linux-hybrid-runbook-worker-health-for-update-management"></a>瞭解和解決更新管理的 Linux 混合式 Runbook 背景工作健全狀況

您的機器在 [更新管理] 中未顯示為 [就緒] 的原因有很多。 在更新管理中，您可以檢查混合式 Runbook 背景工作角色代理程式的健康情況，以判斷根本問題。 本文討論如何在[離線案例](#troubleshoot-offline)中，從 Azure 入口網站和非 azure 機器執行 Azure 機器的疑難排解員。

下列清單是機器可具備的三種整備狀態：

* **就緒**-混合式 Runbook 背景工作角色已部署，且上次出現時間不到1小時前。
* 已**中斷**連線-混合式 Runbook 背景工作角色已部署，且上次在1小時前出現。
* **未設定**-找不到混合式 Runbook 背景工作角色，或尚未完成上架。

> [!NOTE]
> Azure 入口網站顯示的內容與電腦目前的狀態之間可能會稍有延遲。

## <a name="start-the-troubleshooter"></a>啟動疑難排解員

若為 Azure 機器，在入口網站中按一下 [更新代理程式整備程度] 資料行底下的 [疑難排解] 連結，即可啟動 [對更新代理程式進行疑難排解] 頁面。 針對非 Azure 機器，此連結會將您帶到這篇文章。 請參閱離線指示以對非 Azure 機器進行疑難排解。

![VM 清單頁面](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> VM 必須處於執行中狀態，才能進行檢查。 如果 VM 不是處於執行中狀態，您就會看到 [啟動 VM] 的按鈕。

在 [對更新代理程式進行疑難排解] 頁面上，按一下 [執行檢查] 以啟動疑難排解員。 疑難排解員會使用 [[執行] 命令](../../virtual-machines/linux/run-command.md)來執行電腦上的腳本，以驗證相依性。 當疑難排解員完成時，它會傳回檢查結果。

![疑難排解頁面](../media/update-agent-issues-linux/troubleshoot-page.png)

完成時，會在視窗中傳回結果。 檢查區段會提供有關每個檢查所尋找項目的資訊。

![更新代理程式檢查頁面](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>先決條件檢查

### <a name="operating-system"></a>作業系統

作業系統檢查會確認「混合式 Runbook 背景工作角色」是否正在執行下列其中一個作業系統：

|作業系統  |注意  |
|---------|---------|
|CentOS 6 (x86/x64) 和 7 (x64)      | Linux 代理程式必須能夠存取更新存放庫。 分類型修補需要 'yum' 才能傳回 CentOS 未內建的安全性資料。         |
|Red Hat Enterprise 6 (x86/x64) 和 7 (x64)     | Linux 代理程式必須能夠存取更新存放庫。        |
|SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)     | Linux 代理程式必須能夠存取更新存放庫。        |
|Ubuntu 14.04 LTS、16.04 LTS 和 18.04 LTS (x86/x64)      |Linux 代理程式必須能夠存取更新存放庫。         |

## <a name="monitoring-agent-service-health-checks"></a>監視代理程式服務健康情況檢查

### <a name="log-analytics-agent"></a>Log Analytics 代理程式

這種檢查可確保已安裝適用于 Linux 的 Log Analytics 代理程式。 如需如何安裝它的相關指示，請參閱[安裝適用於 Linux 的代理程式](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
)。

### <a name="log-analytics-agent-status"></a>Log Analytics 代理程式狀態

這種檢查可確保適用于 Linux 的 Log Analytics 代理程式正在執行。 如果代理程式並未執行，您可以執行下列命令以嘗試重新啟動。 如需如何進行代理程式疑難排解的詳細資訊，請參閱[進行 Linux 混合式 Runbook 背景工作的疑難排解](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>多路連接

此檢查會判斷代理程式是否正在回報給多個工作區。 更新管理不支援多路連接。

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

此檢查會確認適用于 Linux 的 Log Analytics 代理程式是否具有混合式 Runbook 背景工作角色套件。 更新管理需要有此封裝才能運作。

### <a name="hybrid-runbook-worker-status"></a>混合式 Runbook 背景工作狀態

此檢查確保混合式 Runbook 背景工作正在機器上執行。 如果混合式 Runbook 背景工作運作正常，則應會出現下列程序。 若要深入了解，請參閱[進行適用於 Linux 的 Log Analytics 代理程式疑難排解](hybrid-runbook-worker.md#oms-agent-not-running)。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>連線能力檢查

### <a name="general-internet-connectivity"></a>一般網際網路連線

此檢查確保機器具有網際網路存取權。

### <a name="registration-endpoint"></a>註冊端點

此檢查會判斷混合式 Runbook 背景工作角色是否可以與 Log Analytics 工作區 Azure 自動化正確地通訊。

Proxy 和防火牆設定必須允許「混合式 Runbook 背景工作角色」代理程式與註冊端點進行通訊。 如需要開放的位址和連接埠清單，請參閱[混合式背景工作角色的網路規劃](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>作業端點

此檢查會判斷代理程式是否能夠正確地與「作業執行階段資料服務」進行通訊。

Proxy 和防火牆設定必須允許「混合式 Runbook 背景工作角色」代理程式與「作業執行階段資料服務」進行通訊。 如需要開放的位址和連接埠清單，請參閱[混合式背景工作角色的網路規劃](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Log Analytics 端點 1

此檢查會確認您的機器有權存取 Log Analytics 代理程式所需的端點。

### <a name="log-analytics-endpoint-2"></a>Log Analytics 端點 2

此檢查會確認您的機器有權存取 Log Analytics 代理程式所需的端點。

### <a name="log-analytics-endpoint-3"></a>Log Analytics 端點 3

此檢查會確認您的機器有權存取 Log Analytics 代理程式所需的端點。

## <a name="troubleshoot-offline"></a>離線疑難排解

您可以藉由在本機執行指令碼，在「混合式 Runbook 背景工作角色」上離線使用疑難排解員。 您可以在指令碼中心找到 Python 指令碼 [update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6)。 以下範例顯示此指令碼的輸出範例：

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

若要針對混合式 Runbook 背景工作角色的其他問題進行疑難排解，請參閱[疑難排解-混合式 runbook 背景工作角色](hybrid-runbook-worker.md)。
