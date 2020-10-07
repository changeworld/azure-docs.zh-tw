---
title: 在 Azure 自動化中部署 Linux 混合式 Runbook 背景工作
description: 本文說明如何安裝 Azure 自動化的混合式 Runbook 背景工作角色，以在您的本機資料中心或雲端環境中以 Linux 為基礎的機器上執行 runbook。
services: automation
ms.subservice: process-automation
ms.date: 10/06/2020
ms.topic: conceptual
ms.openlocfilehash: 8295b6bba9703c276bf60a0360ded6f0e195369e
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776267"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>部署 Linux 混合式 Runbook 背景工作角色

您可以使用 Azure 自動化的混合式 Runbook 背景工作角色功能，直接在裝載角色的電腦上以及針對環境中的資源執行 runbook，以管理這些本機資源。 Linux 混合式 Runbook 背景工作角色可利用特殊使用者身分執行 Runbook，此身分的權限經過提升後可執行需要提高權限的命令。 Azure 自動化儲存和管理 runbook，然後將它們傳遞到一或多個指定的機器。 本文說明如何在 Linux 機器上安裝混合式 Runbook 背景工作角色、如何移除背景工作，以及如何移除混合式 Runbook 背景工作角色群組。

在您成功部署 Runbook 背景工作角色後，請檢閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)，以了解如何設定 Runbook 以將內部部署資料中心或其他雲端環境中的程序自動化。

## <a name="prerequisites"></a>必要條件

開始之前，請確定您有下列項目：

### <a name="a-log-analytics-workspace"></a>Log Analytics 工作區

混合式 Runbook 背景工作角色相依于 Azure 監視器 Log Analytics 工作區，以安裝和設定角色。 您可以透過 [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace)、 [PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)或 [Azure 入口網站](../azure-monitor/learn/quick-create-workspace.md)來建立它。

如果您沒有 Azure 監視器 Log Analytics 工作區，請在建立工作區之前，先參閱 [Azure 監視器記錄檔設計指引](../azure-monitor/platform/design-logs-deployment.md) 。

如果您有工作區，但未連結至您的自動化帳戶，則啟用自動化功能會新增 Azure 自動化的功能，包括混合式 Runbook 背景工作角色的支援。 當您在 Log Analytics 工作區中啟用其中一項 Azure 自動化功能時（特別是 [更新管理](update-management/update-mgmt-overview.md) 或 [變更追蹤和清查](change-tracking.md)），背景工作元件會自動推送至代理程式電腦。

若要將更新管理功能新增至您的工作區，請執行下列 PowerShell Cmdlet：

```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

若要將變更追蹤和清查功能新增至您的工作區，請執行下列 PowerShell Cmdlet：

```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Log Analytics 代理程式

混合式 Runbook 背景工作角色需要支援的 Linux 作業系統的 [Log Analytics 代理程式](../azure-monitor/platform/log-analytics-agent.md) 。

>[!NOTE]
>安裝適用于 Linux 的 Log Analytics 代理程式之後，您不應該變更 `sudoers.d` 資料夾或其擁有權的許可權。 **>nxautomation**帳戶需要 Sudo 許可權，這是混合式 Runbook 背景工作角色執行所在的使用者內容。 不應移除許可權。 將此限制為某些資料夾或命令可能會導致中斷性變更。
>

### <a name="supported-linux-operating-systems"></a>支援的 Linux 作業系統

混合式 Runbook 背景工作角色功能支援下列散發：

* Amazon Linux 2012.09 至 2015.09 (x86/x64)
* CentOS Linux 5、6 和 7 (x86/x64)
* Oracle Linux 5、6 和 7 (x86/x64)
* Red Hat Enterprise Linux Server 5、6 和 7 (x86/x64)
* Debian GNU/Linux 6、7 和 8 (x86/x64)
* Ubuntu 12.04 LTS、14.04 LTS、16.04 LTS 和 18.04 (x86/x64)
* SUSE Linux Enterprise Server 12 (x86/x64) 

### <a name="minimum-requirements"></a>最低需求

Linux 混合式 Runbook 背景工作角色的最低需求如下：

* 雙核心
* 4 GB 的 RAM
* 連接埠 443 (輸出)

| **必要封裝** | **說明** | **最低版本**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C 程式庫| 2.5-12 |
|Openssl| OpenSSL 程式庫 | 1.0 (支援 TLS 1.1 和 TLS 1.2)|
|Curl | cURL Web 用戶端 | 7.15.5|
|Python-ctypes | Python 2.x 為必要項目 |
|PAM | 插入式驗證模組|
| **選用套件** | **說明** | **最低版本**|
| PowerShell Core | 若要執行 PowerShell runbook，必須安裝 PowerShell Core。 請參閱[在 Linux 上安裝 PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux) 以了解其安裝方式。 | 6.0.0 |

## <a name="supported-linux-hardening"></a>支援的 Linux 強化

尚不支援下列各項：

* Cis

## <a name="supported-runbook-types"></a>支援的 Runbook 類型

Linux 混合式 Runbook 背景工作角色在 Azure 自動化中支援一組有限的 runbook 類型，如下表所述。

|Runbook 類型 | 支援 |
|-------------|-----------|
|Python 2 |是 |
|PowerShell |是<sup>1</sup> |
|PowerShell 工作流程 |否 |
|圖形化 |否 |
|圖形化 PowerShell 工作流程 |否 |

<sup>1</sup>PowerShell runbook 需要在 Linux 機器上安裝 PowerShell Core。 請參閱[在 Linux 上安裝 PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux) 以了解其安裝方式。

## <a name="install-a-linux-hybrid-runbook-worker"></a>安裝 Linux 混合式 Runbook 背景工作角色

若要安裝和設定 Linux 混合式 Runbook 背景工作角色，請執行下列步驟。

1. 將 Log Analytics 代理程式部署到目的電腦。

    * 針對 Azure Vm，請使用 [適用于 linux 的虛擬機器擴充](../virtual-machines/extensions/oms-linux.md)功能來安裝適用于 Linux 的 Log Analytics 代理程式。 此擴充功能會在 Azure 虛擬機器上安裝 Log Analytics 代理程式，並使用 Azure Resource Manager 範本或 Azure CLI，將虛擬機器註冊到現有的 Log Analytics 工作區。 安裝代理程式之後，您就可以將 VM 新增至自動化帳戶中的混合式 Runbook 背景工作角色群組。

    * 針對非 Azure Vm，請使用將 [linux 電腦連接到 Azure 監視器](../azure-monitor/platform/agent-linux.md) 一文中描述的部署選項，安裝適用于 Linux 的 Log Analytics 代理程式。 您可以針對多部電腦重複此程式，以將多個背景工作角色新增至您的環境。 安裝代理程式之後，即可將 Vm 新增至您的自動化帳戶中的混合式 Runbook 背景工作角色群組。

    > [!NOTE]
    > 若要使用 Desired State Configuration (DSC) 來管理支援混合式 Runbook 背景工作角色的電腦設定，您必須將電腦新增為 DSC 節點。

    > [!NOTE]
    > 在 Linux 混合式背景工作角色安裝期間，必須要有具有對應 sudo 權限的 [nxautomation 帳戶](automation-runbook-execution.md#log-analytics-agent-for-linux)。 如果您嘗試安裝背景工作，但帳戶不存在或沒有適當的權限，則安裝會失敗。

2. 確認代理程式已向工作區報告。

    適用于 Linux 的 Log Analytics 代理程式會將機器連接到 Azure 監視器的 Log Analytics 工作區。 當您在電腦上安裝代理程式並將它連線到您的工作區時，它會自動下載混合式 Runbook 背景工作角色所需的元件。

    幾分鐘後，當代理程式已成功連線到 Log Analytics 工作區時，您可以執行下列查詢，以驗證代理程式是否正在將活動訊號資料傳送至工作區。

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    在搜尋結果中，您應該會看到機器的心跳記錄，表示它已連線並向服務回報。 根據預設，每個代理程式會將活動訊號記錄轉送至其獲指派的工作區。

3. 執行下列命令，將電腦新增至混合式 Runbook 背景工作角色群組，並指定參數、、 `-w` 和的值 `-k` `-g` `-e` 。

    您可以 `-k` `-e` 從自動化帳戶中的 [ **金鑰** ] 頁面取得參數所需的資訊。 從頁面左側的 [**帳戶設定**] 區段下，選取 [**金鑰**]。

    ![[管理金鑰] 頁面](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * 針對 `-e` 參數，複製 **URL**的值。

    * 針對 `-k` 參數，複製 **主要存取金鑰**的值。

    * 針對 `-g` 參數，請指定新的 Linux 混合式 runbook 背景工作角色應加入之混合式 runbook 背景工作角色群組的名稱。 如果此群組已存在於自動化帳戶中，則會將目前的電腦新增至該群組。 如果此群組不存在，則會以該名稱建立。

    * 針對 `-w` 參數，請指定您的 Log Analytics 工作區識別碼。

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

4. 命令完成後，自動化帳戶中的 [混合式背景工作角色群組] 頁面會顯示新的群組和成員數目。 如果這是現有群組，成員數目就會遞增。 您可以從 [Hybrid Worker 群組] 頁面上的清單中選取群組，然後選取 [Hybrid Worker] 圖格。 在 [Hybrid Worker] 頁面上，您會看到列出群組的每個成員。

    > [!NOTE]
    > 如果您針對 Azure VM 使用適用于 Linux 的 Log Analytics 虛擬機器擴充功能，建議將設定 `autoUpgradeMinorVersion` 為 `false` 自動升級版本可能會導致混合式 Runbook 背景工作角色發生問題。 若要瞭解如何手動升級延伸模組，請參閱 [Azure CLI 部署](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)。

## <a name="turn-off-signature-validation"></a>關閉簽章驗證

根據預設，Linux 混合式 Runbook 背景工作角色需要簽章驗證。 如果您對背景工作角色執行未簽署的 Runbook，您會看到 `Signature validation failed` 錯誤。 若要關閉簽章驗證，請執行下列命令。 以您的 Log Analytics 工作區識別碼來更新第二個參數。

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-machine"></a><a name="remove-linux-hybrid-runbook-worker"></a>從內部部署 Linux 電腦移除混合式 Runbook 背景工作角色

您可以在混合式 Runbook 背景工作角色上使用 `ls /var/opt/microsoft/omsagent` 命令，以取得工作區識別碼。 建立的資料夾會以工作區識別碼命名。

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> 此腳本不會從電腦移除適用于 Linux 的 Log Analytics 代理程式。 它只會移除混合式 Runbook 背景工作角色的功能和設定。

## <a name="remove-a-hybrid-worker-group"></a>移除混合式背景工作角色群組

若要移除 Linux 機器的混合式 Runbook 背景工作角色群組，您可以使用與 Windows 混合式背景工作角色群組相同的步驟。 請參閱[移除混合式背景工作角色群組](automation-windows-hrw-install.md#remove-a-hybrid-worker-group)。

## <a name="next-steps"></a>後續步驟

* 若要了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化，請參閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)。

* 若要瞭解如何針對混合式 Runbook 背景工作角色進行疑難排解，請參閱[針對混合式 Runbook 背景工作角色進行疑難排解 - Linux](troubleshoot/hybrid-runbook-worker.md#linux)。
