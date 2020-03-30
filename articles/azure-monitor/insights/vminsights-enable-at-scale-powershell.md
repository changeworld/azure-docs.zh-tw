---
title: 使用 PowerShell 或範本為 VM 啟用 Azure 監視器
description: 本文介紹如何使用 Azure PowerShell 或 Azure 資源管理器範本為一個或多個 Azure 虛擬機器或虛擬機器縮放集啟用 VM 的 Azure 監視器。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/14/2019
ms.openlocfilehash: 75d5203e7c475a44b6a00dbf9286f43114b7b54f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480839"
---
# <a name="enable-azure-monitor-for-vms-using-azure-powershell-or-resource-manager-templates"></a>使用 Azure PowerShell 或資源管理器範本為 VM 啟用 Azure 監視器

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

本文介紹如何使用 Azure PowerShell 或 Azure 資源管理器範本為 Azure 虛擬機器或虛擬機器縮放集啟用 Azure 監視器。 此過程結束時，您將成功開始監視所有虛擬機器，並瞭解是否有任何虛擬機器遇到性能或可用性問題。

## <a name="set-up-a-log-analytics-workspace"></a>設定 Log Analytics 工作區

如果沒有日誌分析工作區，則需要創建一個工作區。 在繼續配置它的步驟之前，請查看["先決條件"](vminsights-enable-overview.md#log-analytics)部分中建議的方法。 然後，可以使用 Azure 資源管理器範本方法完成 VM Azure 監視器的部署。

### <a name="install-the-vminsights-solution"></a>安裝 VMInsights 解決方案

此方法包含一個 JSON 範本，其會指定在 Log Analytics 工作區中啟用解決方案元件的設定。

如果您不知道如何使用範本部署資源，請參閱：
* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../../azure-resource-manager/templates/deploy-powershell.md)
* [使用 Resource Manager 範本與 Azure CLI 部署資源](../../azure-resource-manager/templates/deploy-cli.md)

要使用 Azure CLI，首先需要在本地安裝和使用 CLI。 您必須執行 Azure CLI 2.0.27 版或更新版本。 若要知道您使用的版本，請執行 `az --version`。 要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

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

1. 您已準備好部署此範本。

    * 在包含範本的資料夾中使用下列 PowerShell 命令：

        ```powershell
        New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName <ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
        ```

        配置更改可能需要幾分鐘才能完成。 完成後，將顯示類似于以下內容的消息，其中包含結果：

        ```output
        provisioningState       : Succeeded
        ```

    * 使用 Azure CLI 來執行下列命令：

        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        配置更改可能需要幾分鐘才能完成。 完成後，將顯示類似于以下內容的消息，並包含結果：

        ```output
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>使用 Azure 資源管理器範本啟用

我們已創建用於載入虛擬機器和虛擬機器縮放集的 Azure 資源管理器範本示例。 這些範本包括可用於啟用對現有資源的監視和創建啟用監視的新資源的方案。

>[!NOTE]
>範本需要部署在同一資源組中，以便引入的資源。

如果您不知道如何使用範本部署資源，請參閱：
* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../../azure-resource-manager/templates/deploy-powershell.md)
* [使用 Resource Manager 範本與 Azure CLI 部署資源](../../azure-resource-manager/templates/deploy-cli.md)

要使用 Azure CLI，首先需要在本地安裝和使用 CLI。 您必須執行 Azure CLI 2.0.27 版或更新版本。 若要知道您使用的版本，請執行 `az --version`。 要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="download-templates"></a>下載範本

Azure 資源管理器範本在存檔檔 （.zip） 中提供，可以從 GitHub 存儲庫[下載](https://aka.ms/VmInsightsARMTemplates)。 檔的內容包括使用範本和參數檔表示每個部署方案的資料夾。 在運行參數檔之前，請修改參數檔並指定所需的值。 除非需要自訂範本檔以支援您的特定要求，否則不要修改範本檔。 修改參數檔後，可以使用本文後面介紹的以下方法部署它。

下載檔案包含以下不同方案的範本：

- 如果虛擬機器已存在，**則現有 VmOnsa 範本**支援 VM 的 Azure 監視器。
- **NewVmOnsas 範本**創建虛擬機器，並使 VM 的 Azure 監視器能夠監視它。
- 如果虛擬機器縮放集已存在，**則現有 VmsOnss 板入**範本可為 VM 啟用 Azure 監視器。
- **NewVmsOnsssssss 板入**範本創建虛擬機器縮放集，並使 VM 的 Azure 監視器能夠監視它們。
- **配置工作區**範本通過啟用 Linux 和 Windows 作業系統效能計數器的解決方案和集合，配置日誌分析工作區以支援 Azure 監視器的 VM。

>[!NOTE]
>如果虛擬機器縮放集已存在，並且升級策略設置為**手動**，則在運行 **"現有 VmsonsOns"Azure**資源管理器範本後，預設情況下不會為實例啟用 Azure 監視器。 您必須手動升級實例。

### <a name="deploy-by-using-azure-powershell"></a>使用 Azure PowerShell 進行部署

以下步驟可以使用 Azure PowerShell 進行監視。

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
配置更改可能需要幾分鐘才能完成。 完成後，將顯示類似于以下內容的消息，其中包含結果：

```output
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>使用 Azure CLI 進行部署

以下步驟可以使用 Azure CLI 進行監視。

```azurecli
az login
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```

輸出結果類似下面：

```output
provisioningState       : Succeeded
```

## <a name="enable-with-powershell"></a>使用 PowerShell 啟用

要為多個 VM 或虛擬機器規模集啟用 VM 的 Azure 監視器，請使用 PowerShell 腳本[安裝 VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0)。 可從 Azure PowerShell 庫獲得。 此腳本遍經：

- 訂閱中的每個虛擬機器和虛擬機器規模設置。
- *資源組*指定的作用域資源組。
- 由*Name*指定的單個 VM 或虛擬機器規模集。

針對每個 VM 或虛擬機器擴展集，指令碼會確認是否已經安裝 VM 延伸模組。 如果安裝了 VM 擴展，腳本將嘗試重新安裝它。 如果未安裝 VM 擴展，腳本將安裝日誌分析和依賴項代理 VM 擴展。

驗證是否正在使用 Azure PowerShell 模組 Az 版本 1.0.0 或更高版本，並`Enable-AzureRM`啟用了相容性別名。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

若要取得指令碼引數詳細資料及使用方式範例的清單，請執行 `Get-Help`。

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or reconfigures the following on VMs and virtual machine scale sets:
    - Log Analytics VM extension configured to supplied Log Analytics workspace
    - Dependency agent VM extension

    Can be applied to:
    - Subscription
    - Resource group in a subscription
    - Specific VM or virtual machine scale set
    - Compliance results of a policy for a VM or VM extension

    Script will show you a list of VMs or virtual machine scale sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed, they will not install again.
    Use -ReInstall switch if you need to, for example, update the workspace.

    Use -WhatIf if you want to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a resource group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example, to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source and to reinstall (move to a new workspace)
```

下列範例示範如何在資料夾中使用 PowerShell 命令來啟用適用於 VM 的 Azure 監視器，以及了解預期的輸出：

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or virtual machine scale sets matching criteria specified

VMs or virtual machine scale sets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on the previous two VMs or virtual machine scale sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example, to update workspace.

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="next-steps"></a>後續步驟

現在，已為虛擬機器啟用了監視功能，此資訊可用於使用針對 VM 的 Azure 監視器進行分析。

- 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。

- 要根據 VM 的性能識別瓶頸和總體利用率，請參閱查看[Azure VM 性能](vminsights-performance.md)。
