---
title: 啟用混合式環境的 Azure 監視器 |Microsoft Docs
description: 本文說明如何針對包含一或多部虛擬機器的混合式雲端環境，啟用適用於 VM 的 Azure 監視器。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: ea060ec9ba755a197e2969c0bf58050eb1d62a03
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325962"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-environment"></a>針對混合式環境啟用適用於 VM 的 Azure 監視器

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

本文說明如何針對您資料中心或其他雲端環境中裝載的虛擬機器或實體電腦啟用適用於 VM 的 Azure 監視器。 在此程式結束時，您將已成功開始監視環境中的虛擬機器，並瞭解它們是否遇到任何效能或可用性問題。

開始之前，請務必檢閱[必要條件](vminsights-enable-overview.md)並確認訂用帳戶和資源已符合需求。 檢閱適用於 [Log Analytics Linux 和 Windows 代理程式](../platform/log-analytics-agent.md)的需求和部署方法。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>適用於 VM 的 Azure 監視器對應 Dependency Agent 本身不會傳輸任何資料，因此不需變更防火牆或連接埠。 如果您的 IT 安全性原則不允許網路上的電腦連線到網際網路，則對應資料一律會由 Log Analytics 代理程式直接傳輸到 Azure 監視器服務，或是透過[Operations Management Suite 閘道](../platform/gateway.md)來傳送。

完成這項工作的步驟摘要如下：

1. 安裝適用于 Windows 或 Linux 的 Log Analytics 代理程式。 安裝代理程式之前，請先參閱[Log Analytics 代理程式總覽](../platform/log-analytics-agent.md)一文，以瞭解系統必要條件和部署方法。

2. 針對 [Windows](https://aka.ms/dependencyagentwindows) 或 [Linux](https://aka.ms/dependencyagentlinux)，下載並安裝適用於 VM 的 Azure 監視器對應 Dependency Agent。

3. 啟用收集效能計數器。

4. 部署適用於 VM 的 Azure 監視器。

>[!NOTE]
>本文中所述用於部署 Dependency agent 的資訊也適用于[服務對應解決方案](service-map.md)。  

## <a name="install-the-dependency-agent-on-windows"></a>在 Windows 上安裝 Dependency Agent

您可以執行 `InstallDependencyAgent-Windows.exe` 以手動將 Dependency Agent 安裝在 Windows 電腦上。 如果您在執行此可執行檔時未使用任何選項，則會啟動以互動方式安裝代理程式的安裝精靈。

>[!NOTE]
>必須有*系統管理員*許可權，才能安裝或卸載代理程式。

下表將強調說明命令列中適用於代理程式的安裝程式所支援的參數。

| 參數 | 描述 |
|:--|:--|
| /? | 傳回命令列選項的清單。 |
| /S | 執行無訊息安裝，而不需要與使用者互動。 |

例如，若要使用參數來執行安裝程式 `/?` ，請輸入**InstallDependencyAgent-Windows.exe/？**。

Windows Dependency Agent 的檔案預設安裝在 *C:\Program Files\Microsoft Dependency Agent* 中。 如果在安裝程式完成之後，Dependency agent 無法啟動，請檢查記錄以取得詳細的錯誤資訊。 記錄目錄是 *%Programfiles%\Microsoft Dependency Agent\logs*。

## <a name="install-the-dependency-agent-on-linux"></a>在 Linux 上安裝 Dependency Agent

將 Dependency Agent 從 *InstallDependencyAgent-Linux64.bin* (具有自我解壓縮二進位檔的殼層指令碼) 安裝在 Linux 伺服器上。 您可以使用 `sh` 來執行檔案，或對檔案本身新增執行權限。

>[!NOTE]
> 必須有 root 權限，以便安裝或設定代理程式。
>

| 參數 | 說明 |
|:--|:--|
| -help | 取得命令列選項的清單。 |
| -S | 執行無訊息安裝，不會出現任何使用者提示。 |
| --check | 檢查權限和作業系統，但不會安裝代理程式。 |

例如，若要使用參數來執行安裝程式 `-help` ，請輸入**installdependencyagent-linux64.bin (**。

執行命令，以 root 身分安裝 Linux Dependency agent `sh InstallDependencyAgent-Linux64.bin` 。

如果 Dependency Agent 無法啟動，請檢查記錄以取得詳細的錯誤資訊。 在 Linux 代理程式上，記錄檔目錄是 */var/opt/microsoft/dependency-agent/log*。

Dependency Agent 的檔案位於下列目錄：

| 檔案 | 位置 |
|:--|:--|
| 核心檔案 | /opt/microsoft/dependency-agent |
| 記錄檔 | /var/opt/microsoft/dependency-agent/log |
| 組態檔 | /etc/opt/microsoft/dependency-agent/config |
| 服務可執行檔 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 二進位儲存體檔案 | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>安裝指令碼範例

若要同時在許多伺服器上輕鬆部署 Dependency Agent ，請使用下列指令碼範例，在 Windows 或 Linux 上下載並安裝 Dependency Agent。

### <a name="powershell-script-for-windows"></a>適用於 Windows 的 PowerShell 指令碼

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>適用於 Linux 的殼層指令碼

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Desired State Configuration

若要使用預期狀態設定 (DSC) 部署 Dependency Agent，您可以使用 xPSDesiredStateConfiguration 模組和下列範例程式碼：

```powershell
configuration VMInsights {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="enable-performance-counters"></a>啟用效能計數器

如果解決方案所參考的 Log Analytics 工作區還未設定為收集此解決方案所需的效能計數器，則必須啟用這些效能計數器。 您可以透過下列兩種方式的其中一種來執行此動作：
* 手動，如 [Log Analytics 中的 Windows 和 Linux 效能資料來源](../platform/data-sources-performance-counters.md)中所述
* 藉由下載並執行可從[Azure PowerShell 資源庫](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)取得的 PowerShell 腳本

## <a name="deploy-azure-monitor-for-vms"></a>部署適用於 VM 的 Azure 監視器

此方法包含一個 JSON 範本，其會指定在 Log Analytics 工作區中啟用解決方案元件的設定。

如果您不知道如何使用範本來部署資源，請參閱：
* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../../azure-resource-manager/templates/deploy-powershell.md)
* [使用 Resource Manager 範本與 Azure CLI 部署資源](../../azure-resource-manager/templates/deploy-cli.md)

若要使用 Azure CLI，您必須先在本機安裝和使用 CLI。 您必須執行 Azure CLI 2.0.27 版或更新版本。 若要知道您使用的版本，請執行 `az --version`。 若要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

### <a name="create-and-execute-a-template"></a>建立並執行範本

1. 複製以下 JSON 語法並貼到您的檔案中：

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. 在本機資料夾中將此檔案儲存為 *installsolutionsforvminsights.json*。

1. 擷取 *WorkspaceName*、*ResourceGroupName* 和 *WorkspaceLocation* 的值。 *WorkspaceName* 的值是您 Log Analytics 工作區的名稱。 *WorkspaceLocation* 的值是定義工作區所在的區域。

1. 您已經準備好使用下列 PowerShell 命令部署此範本：

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    設定變更可能需要幾分鐘的時間才能完成。 完成時，會顯示與下列類似的訊息，並包含結果：

    ```powershell
    provisioningState       : Succeeded
    ```
   啟用監視之後，可能需要約 10 分鐘的時間才能檢視混合式電腦的健康狀態和計量。

## <a name="troubleshooting"></a>疑難排解

### <a name="vm-doesnt-appear-on-the-map"></a>VM 不會出現在對應上

如果您的相依性代理程式安裝成功，但您在地圖上看不到您的電腦，請遵循下列步驟來診斷問題。

1. Dependency Agent 是否安裝成功？ 您可以查看是否已安裝服務並且執行，以便驗證。

    **Windows**：尋找名為「Microsoft Dependency agent」的服務。

    **Linux**：尋找執行中的進程「microsoft dependency 代理程式」。

2. 您在[Log Analytics 的免費定價層](./solutions.md)嗎？ 免費方案最多允許五部唯一的電腦。 任何後續的電腦都不會顯示在地圖上，即使先前的五個已不再傳送資料也一樣。

3. 電腦會將記錄檔和效能資料傳送給 Azure 監視器記錄嗎？ 針對您的電腦執行下列查詢：

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    它是否會傳回一或多個結果？ 是否為最新的資料？ 若是如此，您的 Log Analytics 代理程式會正常運作，並與服務通訊。 如果不是，請檢查您伺服器上的代理程式︰[適用於 Windows 的 Log Analytics 代理程式疑難排解](../platform/agent-windows-troubleshoot.md)或[適用於 Linux 的 Log Analytics 代理程式疑難排解](../platform/agent-linux-troubleshoot.md)。

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>電腦出現在地圖上，但是沒有任何進程

如果您在對應上看到您的伺服器，但它沒有進程或連接資料，則表示相依性代理程式已安裝且正在執行，但未載入核心驅動程式。

請檢查 C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file (Windows) 檔案或 /var/opt/microsoft/dependency-agent/log/service.log 檔案 (Linux)。 檔案的最後幾行應該會指出未載入核心的原因。 例如，若您更新過核心，在 Linux 上可能會不受支援。


## <a name="next-steps"></a>後續步驟

現在已針對您的虛擬機器啟用監視，此資訊可透過適用於 VM 的 Azure 監視器進行分析。

- 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。

- 若要找出 VM 效能的瓶頸和整體使用率，請參閱[查看 AZURE VM 效能](vminsights-performance.md)。

