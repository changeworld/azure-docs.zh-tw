---
title: Azure Stack Edge Pro 裝置上的 Vm 使用自訂腳本擴充功能
description: 說明如何在虛擬機器上安裝自訂腳本擴充功能， (Vm) 使用範本在 Azure Stack Edge Pro 裝置上執行。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: 8b233211f47250d4742d35cd0782cdd241839496
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98804867"
---
# <a name="deploy-custom-script-extension-on-vms-running-on-your-azure-stack-edge-pro-device"></a>在 Azure Stack Edge Pro 裝置上執行的 Vm 上部署自訂腳本擴充功能

自訂腳本擴充功能會在 Azure Stack Edge Pro 裝置上執行的虛擬機器上下載並執行腳本或命令。 本文將詳細說明如何使用 Azure Resource Manager 範本安裝和執行自訂腳本擴充功能。 

本文適用于 Azure Stack Edge Pro GPU、Azure Stack Edge Pro R 和 Azure Stack Edge 迷你 R 裝置。

## <a name="about-custom-script-extension"></a>關於自訂腳本擴充功能

自訂腳本擴充功能適用于部署後設定、軟體安裝，或任何其他設定/管理工作。 您可以從 Azure 儲存體或其他可存取的網際網路位置下載腳本，也可以提供腳本或命令給延伸模組執行時間。

自訂指令碼功能可以與 Azure Resource Manager 範本整合。 您也可以使用 Azure CLI、PowerShell 或 Azure 虛擬機器 REST API 來執行它。

## <a name="os-for-custom-script-extension"></a>自訂腳本擴充功能的 OS

#### <a name="supported-os-for-custom-script-extension-on-windows"></a>Windows 上自訂腳本擴充功能的支援 OS

適用于 Windows 的自訂腳本擴充功能會在下列 OSs 上執行。 其他版本可能會運作，但尚未在內部的 Azure Stack Edge Pro 裝置上執行的 Vm 上進行測試。

| 散發 | 版本 |
|---|---|
| Windows Server 2019 | 核心 |
| Windows Server 2016 | 核心 |

#### <a name="supported-os-for-custom-script-extension-on-linux"></a>Linux 上自訂腳本擴充功能的支援 OS

適用于 Linux 的自訂腳本擴充功能會在下列 OSs 上執行。 其他版本可能會運作，但尚未在內部的 Azure Stack Edge Pro 裝置上執行的 Vm 上進行測試。

| 散發 | 版本 |
|---|---|
| Linux：Ubuntu | 18.04 LTS |
| Linux：Red Hat Enterprise Linux | 7.4、7.5、7。7 |

<!--### Script location

Instead of the scripts, in this article, we pass a command to execute via the Custom Script Extension. 

### Internet Connectivity

To download a script externally such as from GitHub or Azure Storage, make sure that the port on which you enable compute network, is connected to the internet. 

If your script is on a local server, then you may still need additional firewall and Network Security Group ports need to be opened.

> [!NOTE]
> Before you install the Custom Script extension, make sure that the port enabled for compute network on your device is connected to Internet and has access. -->

## <a name="prerequisites"></a>先決條件

1. 將[VM 範本和參數檔案下載](https://aka.ms/ase-vm-templates)至您的用戶端電腦。 將下載內容解壓縮到您將用作工作目錄的目錄。

1. 您應在您的裝置上建立並部署 VM。 若要建立 Vm，請遵循 [使用範本在您的 Azure Stack Edge Pro 上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)中的所有步驟。

    如果您需要從 GitHub 下載腳本，或從外部 Azure 儲存體，則在設定計算網路時，請啟用連線到網際網路的埠以進行計算。 這可讓您下載腳本。

    在下列範例中，埠2已連線到網際網路，且用來啟用計算網路。 如果您發現在先前的步驟中不需要 Kubernetes，您可以略過 Kubernetes 節點 IP 和外部服務 IP 指派。

    ![在連線到網際網路的埠上啟用計算設定](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

## <a name="install-custom-script-extension"></a>安裝自訂腳本擴充功能

視 VM 的作業系統而定，您可以安裝適用于 Windows 或 Linux 的自訂腳本擴充功能。
 

### <a name="custom-script-extension-for-windows"></a>Windows 的自訂指令碼延伸模組

若要針對在裝置上執行的 VM 部署適用于 Windows 的自訂腳本擴充功能，請編輯參數檔案， `addCSExtWindowsVM.parameters.json` 然後部署範本 `addCSextensiontoVM.json` 。

#### <a name="edit-parameters-file"></a>編輯參數檔案

檔案 `addCSExtWindowsVM.parameters.json` 會使用下列參數：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of VM>" 
        },
        "extensionName": {
            "value": "<Name of extension>" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
提供 VM 名稱、擴充功能名稱和您想要執行的命令。

以下是本文中使用的範例參數檔案。

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM5" 
        },
        "extensionName": {
            "value": "CustomScriptExtension" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "md C:\\Users\\Public\\Documents\\test"
            }
        }
    }
}
```
#### <a name="deploy-template"></a>部署範本

部署範本 `addCSextensiontoVM.json` 。 此範本會將擴充功能部署到現有的 VM。 執行以下命令：

```powershell
$templateFile = "<Path to addCSExtensiontoVM.json file>"
$templateParameterFile = "<Path to addCSExtWindowsVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
```
> [!NOTE]
> 延伸模組部署是長時間執行的作業，大約需要10分鐘的時間才能完成。

以下是範例輸出：

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment7"

DeploymentName          : deployment7
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/17/2020 10:07:44 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM5
                          extensionName    String                     CustomScriptExtension
                          publisher        String                     Microsoft.Compute
                          type             String                     CustomScriptExtension
                          typeHandlerVersion  String                     1.10
                          settings         Object                     {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```
#### <a name="track-deployment"></a>追蹤部署

若要檢查指定 VM 的擴充功能部署狀態，請執行下列命令： 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
以下是範例輸出：

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> 當部署完成時，會 `ProvisioningState` 變更為 `Succeeded` 。

擴充功能輸出會記錄至目標虛擬機器上下列資料夾中的檔案。 

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

指定檔案會下載至目標虛擬機器上的下列資料夾之中。

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
其中 <n> 是十進位整數，可能會在擴充功能的執行之間變更。 1. * 值符合延伸模組的實際目前 `typeHandlerVersion` 值。 例如，這個實例中的實際目錄是 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.9\Downloads\0` 。 


在此情況下，要為自訂延伸模組執行的命令為： `md C:\\Users\\Public\\Documents\\test` 。 當擴充功能安裝成功時，您可以在命令中指定的路徑上，確認已在 VM 中建立目錄。 


### <a name="custom-script-extension-for-linux"></a>Linux 的自訂指令碼擴充功能

若要針對在裝置上執行的 VM 部署適用于 Windows 的自訂腳本擴充功能，請編輯參數檔案， `addCSExtLinuxVM.parameters.json` 然後部署範本 `addCSExtensiontoVM.json` 。

#### <a name="edit-parameters-file"></a>編輯參數檔案

檔案 `addCSExtLinuxVM.parameters.json` 會使用下列參數：

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of your VM>" 
        },
        "extensionName": {
            "value": "<Name of your extension>" 
        },
        "publisher": {
            "value": "Microsoft.Azure.Extensions" 
        },
        "type": {
            "value": "CustomScript" 
        },
        "typeHandlerVersion": {
            "value": "2.0" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
提供 VM 名稱、擴充功能名稱和您想要執行的命令。

以下是本文中使用的範例參數檔案：

```powershell
$templateFile = "<Path to addCSExtensionToVM.json file>"
$templateParameterFile = "<Path to addCSExtLinuxVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
``` 

> [!NOTE]
> 延伸模組部署是長時間執行的作業，大約需要10分鐘的時間才能完成。

以下是範例輸出：

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensionToVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment99"

DeploymentName          : deployment99
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/18/2020 1:55:23 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM6
                          extensionName    String                     LinuxCustomScriptExtension
                          publisher        String                     Microsoft.Azure.Extensions
                          type             String                     CustomScript
                          typeHandlerVersion  String                     2.0
                          settings         Object                     {
                            "commandToExecute": "sudo echo 'some text' >> /home/Administrator/file2.txt"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```

`commandToExecute`已設定為在 `file2.txt` 目錄中建立檔案，而檔案的 `/home/Administrator` 內容為 `some text` 。 在此情況下，您可以確認已在指定的路徑中建立檔案。

```powershell
Administrator@VM6:~$ dir
file2.txt
Administrator@VM6:~$ cat file2.txt
some text
Administrator@VM6:
```

#### <a name="track-deployment-status"></a>追蹤部署狀態    
    
範本部署是長時間執行的作業。 若要檢查指定 VM 的擴充功能部署狀態，請開啟另一個 PowerShell 會話， (以系統管理員身分執行) 。 執行以下命令： 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
以下是範例輸出： 

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> 當部署完成時，會 `ProvisioningState` 變更為 `Succeeded` 。

擴充功能執行輸出會記錄至下列檔案： `/var/lib/waagent/custom-script/download/0/` 。


## <a name="remove-custom-script-extension"></a>移除自訂腳本擴充功能

若要移除自訂腳本擴充功能，請使用下列命令：

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

以下是範例輸出：

```powershell
PS C:\WINDOWS\system32> Remove-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM6 -Name LinuxCustomScriptExtension
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Yes
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


## <a name="next-steps"></a>後續步驟

[Azure Resource Manager Cmdlet](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
