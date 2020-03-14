---
title: 使用 PowerShell 或範本啟用適用於 VM 的 Azure 監視器（傳統）
description: 本文說明如何使用 Azure PowerShell 或 Azure Resource Manager 範本來啟用一或多個 Azure 虛擬機器或虛擬機器擴展集的適用於 VM 的 Azure 監視器。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/14/2019
ms.openlocfilehash: e28a5dce4dda677ef4e5eb0ed08c42ec1f03c308
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249187"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-powershell-or-resource-manager-templates"></a>使用 Azure PowerShell 或 Resource Manager 範本啟用適用於 VM 的 Azure 監視器（預覽）

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

本文說明如何使用 Azure PowerShell 或 Azure Resource Manager 範本來啟用 Azure 虛擬機器或虛擬機器擴展集的適用於 VM 的 Azure 監視器（預覽）。 在此程式結束時，您將已成功開始監視所有虛擬機器，並瞭解是否有任何效能或可用性問題。

## <a name="set-up-a-log-analytics-workspace"></a>設定 Log Analytics 工作區

如果您沒有 Log Analytics 工作區，則需要建立一個。 請先參閱[必要條件](vminsights-enable-overview.md#log-analytics)一節中所建議的方法，再繼續進行設定的步驟。 然後，您可以使用 Azure Resource Manager 範本方法來完成適用於 VM 的 Azure 監視器的部署。

### <a name="enable-performance-counters"></a>啟用效能計數器

如果解決方案所參考的 Log Analytics 工作區還未設定為收集此解決方案所需的效能計數器，則必須啟用這些效能計數器。 您可以透過下列兩種方式的其中一種來執行此動作：
* 手動，如 [Log Analytics 中的 Windows 和 Linux 效能資料來源](../../azure-monitor/platform/data-sources-performance-counters.md)中所述
* 藉由下載並執行可從[Azure PowerShell 資源庫](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)取得的 PowerShell 腳本

### <a name="install-the-servicemap-solution"></a>安裝 ServiceMap 解決方案

此方法包含一個 JSON 範本，其會指定在 Log Analytics 工作區中啟用解決方案元件的設定。

如果您不知道如何使用範本來部署資源，請參閱：
* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../../azure-resource-manager/templates/deploy-powershell.md)
* [使用 Resource Manager 範本與 Azure CLI 部署資源](../../azure-resource-manager/templates/deploy-cli.md)

若要使用 Azure CLI，您必須先在本機安裝和使用 CLI。 您必須執行 Azure CLI 2.0.27 版或更新版本。 若要知道您使用的版本，請執行 `az --version`。 若要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

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
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
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

        設定變更可能需要幾分鐘的時間才能完成。 完成時，會顯示與下列類似的訊息，並包含結果：

        ```output
        provisioningState       : Succeeded
        ```

    * 使用 Azure CLI 來執行下列命令：

        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --name DeploySolutions --resource-group <ResourceGroupName> --template-file InstallSolutionsForVMInsights.json --parameters WorkspaceName=<workspaceName> WorkspaceLocation=<WorkspaceLocation - example: eastus>
        ```

        設定變更可能需要幾分鐘的時間才能完成。 完成時，會顯示類似下列的訊息，並包含結果：

        ```output
        provisioningState       : Succeeded
        ```

## <a name="enable-with-azure-resource-manager-templates"></a>使用 Azure Resource Manager 範本啟用

我們已建立範例 Azure Resource Manager 範本，用來將您的虛擬機器和虛擬機器擴展集上架。 這些範本包括您可用來在現有資源上啟用監視，以及建立已啟用監視功能的新資源的案例。

>[!NOTE]
>範本必須部署在與資源相同的資源群組中，才能放在面板上。

如果您不知道如何使用範本來部署資源，請參閱：
* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../../azure-resource-manager/templates/deploy-powershell.md)
* [使用 Resource Manager 範本與 Azure CLI 部署資源](../../azure-resource-manager/templates/deploy-cli.md)

若要使用 Azure CLI，您必須先在本機安裝和使用 CLI。 您必須執行 Azure CLI 2.0.27 版或更新版本。 若要知道您使用的版本，請執行 `az --version`。 若要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

### <a name="download-templates"></a>下載範本

您可以從 GitHub 存放庫[下載](https://aka.ms/VmInsightsARMTemplates)的封存檔案（.zip）中提供 Azure Resource Manager 範本。 檔案的內容包括代表每個部署案例的資料夾，以及範本和參數檔案。 在執行之前，請修改參數檔案，並指定所需的值。 請勿修改範本檔案，除非您需要加以自訂以支援您的特定需求。 修改參數檔案之後，您可以使用本文稍後所述的下列方法來部署該檔案。

下載檔案包含適用于不同案例的下列範本：

- 如果虛擬機器已存在， **ExistingVmOnboarding**範本會啟用適用於 VM 的 Azure 監視器。
- **NewVmOnboarding**範本會建立虛擬機器，並讓適用於 VM 的 Azure 監視器加以監視。
- 如果虛擬機器擴展集已存在， **ExistingVmssOnboarding**範本會啟用適用於 VM 的 Azure 監視器。
- **NewVmssOnboarding**範本會建立虛擬機器擴展集，並啟用適用於 VM 的 Azure 監視器來監視它們。
- **ConfigureWorkspace**範本會藉由啟用 Linux 和 Windows 作業系統效能計數器的解決方案和集合，將您的 Log Analytics 工作區設定為支援適用於 VM 的 Azure 監視器。

>[!NOTE]
>如果虛擬機器擴展集已存在，且升級原則設定為**手動**，則在執行**ExistingVmssOnboarding** Azure Resource Manager 範本之後，預設不會針對實例啟用適用於 VM 的 Azure 監視器。 您必須手動升級實例。

### <a name="deploy-by-using-azure-powershell"></a>使用 Azure PowerShell 進行部署

下列步驟會使用 Azure PowerShell 來啟用監視。

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```
設定變更可能需要幾分鐘的時間才能完成。 完成時，會顯示與下列類似的訊息，並包含結果：

```output
provisioningState       : Succeeded
```

### <a name="deploy-by-using-the-azure-cli"></a>使用 Azure CLI 進行部署

下列步驟會使用 Azure CLI 來啟用監視。

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

若要啟用多個 Vm 或虛擬機器擴展集的適用於 VM 的 Azure 監視器，請使用 PowerShell 腳本[install-vminsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0)。 它可以從 Azure PowerShell 資源庫取得。 此腳本會逐一查看：

- 您的訂用帳戶中的每個虛擬機器和虛擬機器擴展集。
- *ResourceGroup*所指定的範圍資源群組。
- 依*名稱*指定的單一 VM 或虛擬機器擴展集。

針對每個 VM 或虛擬機器擴展集，指令碼會確認是否已經安裝 VM 延伸模組。 如果已安裝 VM 延伸模組，腳本會嘗試重新安裝它。 如果未安裝 VM 延伸模組，腳本會安裝 Log Analytics 和 Dependency agent VM 延伸模組。

確認您使用的是已啟用 `Enable-AzureRM` 相容性別名的 Azure PowerShell 模組 Az 1.0.0 版或更新版本。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

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

現在已針對您的虛擬機器啟用監視，此資訊可透過適用於 VM 的 Azure 監視器進行分析。

- 若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。

- 若要找出 VM 效能的瓶頸和整體使用率，請參閱[查看 AZURE VM 效能](vminsights-performance.md)。
