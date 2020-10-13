---
title: 如何從適用于 Windows 的群組原則基準建立來賓設定原則定義
description: 瞭解如何將群組原則從 Windows Server 2019 安全性基準轉換成原則定義。
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: dce22885981ab01fe37fac8588899d12a5afb87d
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893368"
---
# <a name="how-to-create-guest-configuration-policy-definitions-from-group-policy-baseline-for-windows"></a>如何從適用于 Windows 的群組原則基準建立來賓設定原則定義

建立自訂原則定義之前，最好先閱讀 [Azure 原則來賓](../concepts/guest-configuration.md)設定的概念總覽資訊。 若要瞭解如何建立適用于 Linux 的自訂來賓設定原則定義，請參閱 [如何建立適用于 linux 的來賓](./guest-configuration-create-linux.md)設定原則。 若要瞭解如何建立 Windows 的自訂來賓設定原則定義，請參閱 [如何建立 windows 的來賓](./guest-configuration-create.md)設定原則。

在稽核 Windows 時，客體設定會使用[預期狀態設定](/powershell/scripting/dsc/overview/overview) (DSC) 資源模組來建立設定檔。 DSC 設定會定義電腦所應處的條件。 如果設定的評估不 **符合規範**，就會觸發原則效果 *auditIfNotExists* 。
[Azure 原則來賓](../concepts/guest-configuration.md) 設定只會審核機器內的設定。

> [!IMPORTANT]
> 使用「來賓設定」的自訂原則定義是預覽功能。
>
> 您需要客體設定延伸模組，才能在 Azure 虛擬機器中執行稽核。 若要在所有 Windows 電腦間大規模部署延伸模組，請指派下列原則定義：
> - [部署必要條件，以在 Windows VM 上啟用客體設定原則。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

DSC 社區已發佈 [BaselineManagement 模組](https://github.com/microsoft/BaselineManagement) ，以將匯出的群組原則範本轉換成 DSC 格式。 BaselineManagement 模組與 >microsoft.guestconfiguration 指令程式搭配使用，可從群組原則內容建立適用于 Windows 的 Azure 原則來賓設定套件。 如需使用 BaselineManagement 模組的詳細資訊，請參閱文章 [快速入門：將群組原則轉換成 DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart)。

在本指南中，我們將逐步解說從群組原則物件 (GPO) 建立 Azure 原則來賓設定套件的流程。 雖然本逐步解說概述 Windows Server 2019 安全性基準的轉換，但相同的程式也可以套用至其他 Gpo。  

## <a name="download-windows-server-2019-security-baseline-and-install-related-powershell-modules"></a>下載 Windows Server 2019 安全性基準並安裝相關的 PowerShell 模組

若要在 PowerShell 中安裝 **DSC**、 **>microsoft.guestconfiguration**、 **基準管理**和相關的 Azure 模組：

1. 從 PowerShell 提示字元中執行下列命令：

   ```azurepowershell-interactive
   # Install the BaselineManagement module, Guest Configuration DSC resource module, and relevant Azure modules from PowerShell Gallery
   Install-Module az.resources, az.policyinsights, az.storage, guestconfiguration, gpregistrypolicyparser, securitypolicydsc, auditpolicydsc, baselinemanagement -scope currentuser -Repository psgallery -AllowClobber
   ```

1. 建立的目錄，並從 Windows 安全性合規性工具組下載 Windows Server 2019 安全性基準。

   ```azurepowershell-interactive
   # Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
   New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
   Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
   ```

1. 解除封鎖並展開下載的伺服器2019基準。

   ```azurepowershell-interactive
   Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
   Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
   ```

1. 使用 **MapGuidsToGpoNames.ps1**驗證服務器2019基準內容。

   ```azurepowershell-interactive
   # Show content details of downloaded GPOs
   C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
   ```

## <a name="convert-from-group-policy-to-azure-policy-guest-configuration"></a>從群組原則轉換為 Azure 原則來賓設定

接下來，我們會使用「來賓設定」和「基準管理」模組，將下載的伺服器2019基準轉換成來賓設定套件。

1. 使用基準管理模組，將群組原則轉換為 Desired State Configuration。

   ```azurepowershell-interactive
   ConvertFrom-GPO -Path 'C:\git\policyfiles\downloads\GPOs\{3657C7A2-3FF3-4C21-9439-8FDF549F1D68}\' -OutputPath 'C:\git\policyfiles\' -OutputConfigurationScript -Verbose
   ```

1. 建立原則內容套件之前，請先重新命名、重新格式化和執行已轉換的腳本。

   ```azurepowershell-interactive
   Rename-Item -Path C:\git\policyfiles\DSCFromGPO.ps1 -NewName C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('DSCFromGPO', 'Server2019Baseline') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('PSDesiredStateConfiguration', 'PSDscResources') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   C:\git\policyfiles\Server2019Baseline.ps1
   ```

1. 建立 Azure 原則的來賓設定內容套件。

   ```azurepowershell-interactive
   New-GuestConfigurationPackage -Name Server2019Baseline -Configuration c:\git\policyfiles\localhost.mof -Verbose
   ```

## <a name="create-azure-policy-guest-configuration"></a>建立 Azure 原則來賓設定

下一步是要將檔案發佈至 Azure Blob 儲存體。 

1. 下列指令碼包含可供用來自動執行這項工作的函式。 請注意，函數中使用的命令 `publish` 需要 `Az.Storage` 模組。

   ```azurepowershell-interactive
    function Publish-Configuration {
        param(
        [Parameter(Mandatory=$true)]
        $resourceGroup,
        [Parameter(Mandatory=$true)]
        $storageAccountName,
        [Parameter(Mandatory=$true)]
        $storageContainerName,
        [Parameter(Mandatory=$true)]
        $filePath,
        [Parameter(Mandatory=$true)]
        $blobName
        )

        # Get Storage Context
        $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
            -Name $storageAccountName | `
            ForEach-Object { $_.Context }

        # Upload file
        $Blob = Set-AzStorageBlobContent -Context $Context `
            -Container $storageContainerName `
            -File $filePath `
            -Blob $blobName `
            -Force

        # Get url with SAS token
        $StartTime = (Get-Date)
        $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
        $SAS = New-AzStorageBlobSASToken -Context $Context `
            -Container $storageContainerName `
            -Blob $blobName `
            -StartTime $StartTime `
            -ExpiryTime $ExpiryTime `
            -Permission rl `
            -FullUri

        # Output
        return $SAS
    }
   ```

1. 建立參數來定義唯一的資源群組、儲存體帳戶和容器。 
   
   ```azurepowershell-interactive
    # Replace the $resourceGroup, $storageAccount, and $storageContainer values below.
    $resourceGroup = 'rfc_customguestconfig'
    $storageAccount = 'guestconfiguration'
    $storageContainer = 'content'
    $path = 'c:\git\policyfiles\Server2019Baseline\Server2019Baseline.zip'
    $blob = 'Server2019Baseline.zip' 
    ```

1. 使用 publish 函式搭配指派的參數，將來賓設定套件發佈至公用 Blob 儲存體。


   ```azurepowershell-interactive
   $PublishConfigurationSplat = @{
       resourceGroup = $resourceGroup
       storageAccountName = $storageAccount
       storageContainerName = $storageContainer
       filePath = $path
       blobName = $blob
       FullUri = $true
   }
   $uri = Publish-Configuration @PublishConfigurationSplat
    ```
1. 在完成建立並上傳客體設定自訂原則套件後，請建立客體設定原則定義。 使用 `New-GuestConfigurationPolicy` Cmdlet 來建立來賓設定。

   ```azurepowershell-interactive
    $NewGuestConfigurationPolicySplat = @{
        ContentUri = $Uri 
        DisplayName = 'Server 2019 Configuration Baseline' 
        Description 'Validation of using a completely custom baseline configuration for Windows VMs' 
        Path = 'C:\git\policyfiles\policy'  
        Platform = Windows 
        }
   New-GuestConfigurationPolicy @NewGuestConfigurationPolicySplat
   ```
    
1. 使用 Cmdlet 發佈原則定義 `Publish-GuestConfigurationPolicy` 。 此 Cmdlet 只有 **Path** 參數，該參數會指向由 `New-GuestConfigurationPolicy` 所建立的 JSON 檔案位置。 若要執行發佈命令，您需要在 Azure 中建立原則定義的存取權。 如需特定的授權需求，請參閱 [Azure 原則概觀](../overview.md#getting-started)頁面。 最佳的內建角色為**資源原則參與者**。

   ```azurepowershell-interactive
   Publish-GuestConfigurationPolicy -Path C:\git\policyfiles\policy\ -Verbose
   ```

## <a name="assign-guest-configuration-policy-definition"></a>指派來賓設定原則定義

在 Azure 中建立原則之後，即可指派方案作為最後一步。 請參閱使用[入口網站](../assign-policy-portal.md)、[Azure CLI](../assign-policy-azurecli.md) 以及 [Azure PowerShell](../assign-policy-powershell.md) 來指派方案的方法。

> [!IMPORTANT]
> 來賓設定原則定義 **一律** 必須使用結合 _AuditIfNotExists_ 和 _DeployIfNotExists_ 原則的方案來指派。 如果僅指派 _AuditIfNotExists_ 原則，則不會部署先決條件，而原則一律會顯示「0」部伺服器符合規範。

若要搭配 _DeployIfNotExists_ 效果指派原則定義，則需要額外的存取層級。 若要授與最小權限，您可建立自訂角色定義來擴充**資源原則參與者**。 下列範例會建立名為**資源原則參與者 DINE** 的角色，並具有 _Microsoft.Authorization/roleAssignments/write_ 的額外權限。

   ```azurepowershell-interactive
   $subscriptionid = '00000000-0000-0000-0000-000000000000'
   $role = Get-AzRoleDefinition "Resource Policy Contributor"
   $role.Id = $null
   $role.Name = "Resource Policy Contributor DINE"
   $role.Description = "Can assign Policies that require remediation."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/$subscriptionid")
   New-AzRoleDefinition -Role $role
   ```

## <a name="next-steps"></a>後續步驟

- 了解如何使用[客體設定](../concepts/guest-configuration.md)來稽核 VM。
- 了解如何[以程式設計方式建立原則](./programmatically-create.md)。
- 了解如何[取得合規性資料](./get-compliance-data.md)。
