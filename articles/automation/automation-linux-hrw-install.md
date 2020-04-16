---
title: Azure 自動化 Linux 混合式 Runbook 背景工作
description: 本文提供有關安裝 Azure 自動化混合式 Runbook 背景工作角色的資訊，以便您在本機資料中心或雲端環境內的 Linux 電腦上執行 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 9dc4dce5a7af49529924881321b1a5080293a585
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405618"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>部署 Linux 混合式 Runbook 背景工作角色

您可以使用 Azure 自動化的混合式 Runbook 背景工作角色功能，直接在裝載角色的電腦上以及針對環境中的資源執行 Runbook，從而管理這些本機資源。 Linux 混合式 Runbook 背景工作角色可利用特殊使用者身分執行 Runbook，此身分的權限經過提升後可執行需要提高權限的命令。 Runbook 會儲存並在 Azure 自動化中管理，接著傳遞至一或多個指定的電腦。

本文會說明如何在 Linux 機器上安裝混合式 Runbook 背景工作角色。

## <a name="supported-linux-operating-systems"></a>支援的 Linux 作業系統

混合式 Runbook 背景工作角色功能支援下列散發：

* Amazon Linux 2012.09 至 2015.09 (x86/x64)
* CentOS Linux 5、6 和 7 (x86/x64)
* Oracle Linux 5、6 和 7 (x86/x64)
* Red Hat Enterprise Linux Server 5、6 和 7 (x86/x64)
* Debian GNU/Linux 6、7 和 8 (x86/x64)
* Ubuntu 12.04 LTS、14.04 LTS、16.04 LTS 和 18.04 (x86/x64)
* SUSE Linux Enterprise Server 11 和 12 (x86/x64)

## <a name="supported-runbook-types"></a>支援的 Runbook 類型

Linux 混合式 Runbook 背景工作角色不支援 自動化中整組的 Runbook 類型。

下列 Runbook 類型可在 Linux 混合式背景工作角色運作：

* Python 2
* PowerShell

  > [!NOTE]
  > PowerShell Runbook 要求在 Linux 機器上安裝 PowerShell Core。 請參閱[在 Linux 上安裝 PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux) 以了解其安裝方式。

下列 Runbook 類型不會在 Linux 混合式背景工作角色運作：

* PowerShell 工作流程
* 圖形化
* 圖形化 PowerShell 工作流程

## <a name="installing-a-linux-hybrid-runbook-worker"></a>安裝 Linux 混合式 Runbook 背景工作角色

要在 Linux 電腦上安裝和配置混合 Runbook 輔助角色,請按照簡單的手動過程操作。 您必須在 Azure Log Analytics 工作區中啟用 [自動化混合式背景工作角色] 解決方案，然後執行一組命令將電腦註冊為背景工作角色，再將它新增至群組。

Linux 混合式 Runbook 背景工作角色的最低需求如下：

* 雙核心
* 4 GB 的 RAM
* 連接埠 443 (輸出)

### <a name="package-requirements"></a>封裝需求

| **必要的套件** | **說明** | **最小版本**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C 程式庫| 2.5-12 |
|Openssl| OpenSSL 程式庫 | 1.0 (支援 TLS 1.1 和 TLS 1.2)|
|Curl | cURL Web 用戶端 | 7.15.5|
|Python-ctypes | 需要 Python 2.x |
|PAM | 插入式驗證模組|
| **選用套件** | **說明** | **最小版本**|
| PowerShell Core | 若要執行 PowerShell Runbook，則必須安裝 PowerShell，請參閱[在 Linux 上安裝 PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux) 以了解其安裝方式。  | 6.0.0 |

### <a name="installation"></a>安裝

繼續之前，請記住您的自動化帳戶連結到的 Log Analytics 工作區。 也請記住自動化帳戶的主要金鑰。 您只要選取您的自動化帳戶，再針對工作區識別碼選取 [工作區]****，然後針對主索引鍵選取 [索引鍵]****，即可在 Azure 入口網站中找到這兩項資訊。 如需混合式 Runbook 背景工作角色所需的連接埠和位址資訊，請參閱[設定網路](automation-hybrid-runbook-worker.md#network-planning)。

1. 使用下列其中一個方法，在 Azure 中啟用 [自動化混合式背景工作角色] 解決方案：

   * 使用"[將 Azure 監視器日誌解決方案添加到工作區](../log-analytics/log-analytics-add-solutions.md)「中的過程,將自動化混合輔助角色解決方案添加到訂閱中。
   * 執行下列 Cmdlet：

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. 執行下列命令來安裝「適用於 Linux 的 Log Analytics 代理程式」。 將 \<WorkspaceID\> 和 \<WorkspaceKey\> 取代為您工作區中的適當值。

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. 執行下列命令，變更 -w**、-k**、-g** 和 -e** 參數的值。 對於 -g** 參數，請將其值取代為新的 Linux 混合式 Runbook 背景工作角色應加入之混合式 Runbook 背景工作角色群組的名稱。 如果您的自動化帳戶中沒有該名稱，則會以該名稱建立新的混合式 Runbook 背景工作角色群組。

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. 命令完成之後，Azure 入口網站中的 [混合式背景工作角色群組] 頁面會顯示新的群組和成員數目。 如果這是現有群組，成員數目就會遞增。 您可以從 [Hybrid Worker 群組] 頁面上的清單中選取群組，然後選取 [Hybrid Worker]**** 圖格。 在 [Hybrid Worker] 頁面上，您會看到列出群組的每個成員。

> [!NOTE]
> 如果要為 Azure VM 使用 Linux 的 Azure`autoUpgradeMinorVersion`監視器虛擬機器擴展, 我們建議將設定為 false,因為自動升級版本可能會導致混合 Runbook 輔助角色出現問題。 要瞭解如何手動升級擴展,請參閱[Azure CLI 部署](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)。

## <a name="turning-off-signature-validation"></a>關閉簽章驗證

根據預設，Linux 混合式 Runbook 背景工作角色需要簽章驗證。 如果針對工作人員執行無符號 Runbook,則會看到`Signature validation failed`錯誤 。 若要關閉簽章驗證，請執行下列命令。 以您的 Log Analytics 工作區識別碼來更新第二個參數。

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="next-steps"></a>後續步驟

* 若要了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化，請參閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)。
* 如需移除混合式 Runbook 背景工作角色的指示，請參閱[移除 Azure 自動化混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)。
* 若要了解如何針對混合式 Runbook 背景工作角色進行疑難排解，請參閱[針對 Linux 混合式 Runbook 背景工作角色進行疑難排解](troubleshoot/hybrid-runbook-worker.md#linux)
