---
title: 使用 PowerShell 建立具有 Azure 映射產生器的 Windows VM
description: 使用 Azure Image Builder PowerShell 模組建立 Windows VM。
author: cynthn
ms.author: cynthn
ms.date: 06/17/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a221ba8fe14db37729183774197bfc2db8bf2baa
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328100"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder-using-powershell"></a>預覽：使用 PowerShell 建立具有 Azure 映射產生器的 Windows VM

本文示範如何使用 Azure VM 映射產生器 PowerShell 模組來建立自訂的 Windows 映像。

> [!CAUTION]
> Azure Image Builder 目前處於公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供。 不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

如果您選擇在本機使用 PowerShell，本文會要求您安裝 Az PowerShell 模組，並使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) Cmdlet 連線到您的 Azure 帳戶。 如需安裝 Az PowerShell 模組的詳細資訊，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。

> [!IMPORTANT]
> **ImageBuilder**和**az. ManagedServiceIdentity** PowerShell 模組目前為預覽狀態，您必須使用 `Install-Module` Cmdlet 搭配參數來個別安裝這些模組。 `AllowPrerelease` 這些 PowerShell 模組正式推出之後，就會成為未來 Az PowerShell 模組版本的一部分，並以原生方式從 Azure Cloud Shell 內提供。

```azurepowershell-interactive
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

如果您有多個 Azure 訂用帳戶，請選擇資源計費的適當訂用帳戶。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) Cmdlet 來選取特定的訂用帳戶。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

### <a name="register-features"></a>註冊功能

如果這是您第一次在預覽期間使用 Azure image builder，請註冊新的 **VirtualMachineTemplatePreview** 功能。

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

檢查功能註冊的狀態。

> [!NOTE]
> 在變更為之前， **>registrationstate** 可能會處於 `Registering` 數分鐘的狀態 `Registered` 。 等到狀態 **註冊** 後再繼續。

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

如果您的 Azure 訂用帳戶尚未註冊，請註冊下列資源提供者。

- Microsoft.Compute
- Microsoft.KeyVault
- Microsoft.Storage
- Microsoft.VirtualMachineImages

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute, Microsoft.KeyVault, Microsoft.Storage, Microsoft.VirtualMachineImages |
  Where-Object RegistrationState -ne Registered |
    Register-AzResourceProvider
```

## <a name="define-variables"></a>定義變數

您將會重複使用數項資訊。 建立變數來儲存資訊。

```azurepowershell-interactive
# Destination image resource group name
$imageResourceGroup = 'myWinImgBuilderRG'

# Azure region
$location = 'WestUS2'

# Name of the image to be created
$imageTemplateName = 'myWinImage'

# Distribution properties of the managed image upon completion
$runOutputName = 'myDistResults'
```

為您的 Azure 訂用帳戶識別碼建立變數。 若要確認 `subscriptionID` 變數是否包含您的訂用帳戶識別碼，您可以執行下列範例中的第二行。

```azurepowershell-interactive
# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id
Write-Output $subscriptionID
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Cmdlet 來建立 [Azure 資源群組](../../azure-resource-manager/management/overview.md)。 資源群組是一個邏輯容器，Azure 資源會在其中以群組方式部署及管理。

下列範例會根據 `$location` 變數所指定區域中 `$imageResourceGroup` 變數中的名稱，建立資源群組。 此資源群組是用來儲存映射設定範本成品和映射。

```azurepowershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="create-user-identity-and-set-role-permissions"></a>建立使用者身分識別和設定角色許可權

使用下列範例，授與 Azure 映射產生器許可權，以在指定的資源群組中建立映射。 若沒有此許可權，映射建立程式將無法順利完成。

建立角色定義和身分識別名稱的變數。 這些值都必須是唯一的。

```azurepowershell-interactive
[int]$timeInt = $(Get-Date -UFormat '%s')
$imageRoleDefName = "Azure Image Builder Image Def $timeInt"
$identityName = "myIdentity$timeInt"
```

建立使用者身分識別。

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName
```

將身分識別資源和主體識別碼儲存在變數中。

```azurepowershell-interactive
$identityNameResourceId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```

### <a name="assign-permissions-for-identity-to-distribute-images"></a>為身分識別指派權限以發佈映像

下載 json 設定檔，並根據本文中定義的設定加以修改。

```azurepowershell-interactive
$myRoleImageCreationUrl = 'https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
$myRoleImageCreationPath = "$env:TEMP\myRoleImageCreation.json"

Invoke-WebRequest -Uri $myRoleImageCreationUrl -OutFile $myRoleImageCreationPath -UseBasicParsing

$Content = Get-Content -Path $myRoleImageCreationPath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $imageResourceGroup
$Content = $Content -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName
$Content | Out-File -FilePath $myRoleImageCreationPath -Force
```

建立角色定義。

```azurepowershell-interactive
New-AzRoleDefinition -InputFile $myRoleImageCreationPath
```

將角色定義授與給 image builder 服務主體。

```azurepowershell-interactive
$RoleAssignParams = @{
  ObjectId = $identityNamePrincipalId
  RoleDefinitionName = $imageRoleDefName
  Scope = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
}
New-AzRoleAssignment @RoleAssignParams
```

> [!NOTE]
> 如果您收到錯誤：「_>get-azroledefinition：角色定義限制已超過」。無法建立更多角色定義。_」，請參閱 [疑難排解 Azure RBAC](../../role-based-access-control/troubleshooting.md)。

## <a name="create-a-shared-image-gallery"></a>建立共用映像庫

建立資源庫。

```azurepowershell-interactive
$myGalleryName = 'myImageGallery'
$imageDefName = 'winSvrImages'

New-AzGallery -GalleryName $myGalleryName -ResourceGroupName $imageResourceGroup -Location $location
```

建立資源庫定義。

```azurepowershell-interactive
$GalleryParams = @{
  GalleryName = $myGalleryName
  ResourceGroupName = $imageResourceGroup
  Location = $location
  Name = $imageDefName
  OsState = 'generalized'
  OsType = 'Windows'
  Publisher = 'myCo'
  Offer = 'Windows'
  Sku = 'Win2019'
}
New-AzGalleryImageDefinition @GalleryParams
```

## <a name="create-an-image"></a>建立映像

建立 Azure image builder 來源物件。 如需有效參數值的 Azure PowerShell，請參閱在 [Azure Marketplace 中尋找 WINDOWS VM 映射](./cli-ps-findimage.md) 。

```azurepowershell-interactive
$SrcObjParams = @{
  SourceTypePlatformImage = $true
  Publisher = 'MicrosoftWindowsServer'
  Offer = 'WindowsServer'
  Sku = '2019-Datacenter'
  Version = 'latest'
}
$srcPlatform = New-AzImageBuilderSourceObject @SrcObjParams
```

建立 Azure 映射產生器散發者物件。

```azurepowershell-interactive
$disObjParams = @{
  SharedImageDistributor = $true
  ArtifactTag = @{tag='dis-share'}
  GalleryImageId = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup/providers/Microsoft.Compute/galleries/$myGalleryName/images/$imageDefName"
  ReplicationRegion = $location
  RunOutputName = $runOutputName
  ExcludeFromLatest = $false
}
$disSharedImg = New-AzImageBuilderDistributorObject @disObjParams
```

建立 Azure 映射產生器自訂物件。

```azurepowershell-interactive
$ImgCustomParams = @{
  PowerShellCustomizer = $true
  CustomizerName = 'settingUpMgmtAgtPath'
  RunElevated = $false
  Inline = @("mkdir c:\\buildActions", "echo Azure-Image-Builder-Was-Here  > c:\\buildActions\\buildActionsOutput.txt")
}
$Customizer = New-AzImageBuilderCustomizerObject @ImgCustomParams
```

建立 Azure image builder 範本。

```azurepowershell-interactive
$ImgTemplateParams = @{
  ImageTemplateName = $imageTemplateName
  ResourceGroupName = $imageResourceGroup
  Source = $srcPlatform
  Distribute = $disSharedImg
  Customize = $Customizer
  Location = $location
  UserAssignedIdentityId = $identityNameResourceId
}
New-AzImageBuilderTemplate @ImgTemplateParams
```

完成時，會傳回一則訊息，並在中建立映射產生器設定範本 `$imageResourceGroup` 。

若要判斷範本建立流程是否成功，您可以使用下列範例。

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

在背景中，image builder 也會在您的訂用帳戶中建立預備資源群組。 此資源群組會用於映射組建。 格式如下： `IT_<DestinationResourceGroup>_<TemplateName>` 。

> [!WARNING]
> 請勿直接刪除暫存資源群組。 刪除映射範本成品，這將會刪除暫存資源群組。

如果服務在映射設定範本提交期間報告失敗：

- 請參閱 [疑難排解 AZURE VM 映射組建 (AIB) 失敗](../linux/image-builder-troubleshoot.md)。
- 請先使用下列範例刪除範本，再重試。

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup
```

## <a name="start-the-image-build"></a>啟動映射組建

將映射設定提交至 VM 映射產生器服務。

```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

等待映射建立程式完成。 此步驟最多可能需要一小時的時間。

如果您遇到錯誤，請參閱 [疑難排解 AZURE VM 映射組建 (AIB) 失敗](../linux/image-builder-troubleshoot.md)。

## <a name="create-a-vm"></a>建立 VM

將 VM 的登入認證儲存在變數中。 必須使用複雜密碼。

```azurepowershell-interactive
$Cred = Get-Credential
```

使用您建立的映射來建立 VM。

```azurepowershell-interactive
$ArtifactId = (Get-AzImageBuilderRunOutput -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup).ArtifactId

New-AzVM -ResourceGroupName $imageResourceGroup -Image $ArtifactId -Name myWinVM01 -Credential $Cred
```

## <a name="verify-the-customization"></a>確認自訂

使用您在建立 VM 時所設定的使用者名稱與密碼，建立與該 VM 的遠端桌面連線。 在 VM 中，開啟 PowerShell 並執行， `Get-Content` 如下列範例所示：

```azurepowershell-interactive
Get-Content -Path C:\buildActions\buildActionsOutput.txt
```

您應該會看到以映射自訂程式期間建立的檔案內容為依據的輸出。

```Output
Azure-Image-Builder-Was-Here
```

## <a name="clean-up-resources"></a>清除資源

如果不需要在本文中建立的資源，您可以執行下列範例將它們刪除。

### <a name="delete-the-image-builder-template"></a>刪除 image builder 範本

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

### <a name="delete-the-image-resource-group"></a>刪除映射資源群組

> [!CAUTION]
> 下列範例會刪除指定的資源群組和其中包含的所有資源。
> 如果本文章範圍以外的資源存在於指定的資源群組中，則也會一併刪除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name $imageResourceGroup
```

## <a name="next-steps"></a>後續步驟

若要深入瞭解本文中所使用之 json 檔案的元件，請參閱影像產生器 [範本參考](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
