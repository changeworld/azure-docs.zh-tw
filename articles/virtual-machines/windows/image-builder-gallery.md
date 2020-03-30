---
title: 將 Azure 映射產生器與 Windows VM 的圖像庫一起使用（預覽）
description: 使用 Azure 映射產生器和 Azure PowerShell 創建 Azure 共用庫映射版本。
author: cynthn
ms.author: cynthn
ms.date: 01/14/2020
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: d5856780d0d9f1a1943bca1c2f076bb3ec914e1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263348"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>預覽：創建 Windows 映像並將其分發到共用圖像庫 

本文將向您展示如何使用 Azure 映射產生器和 Azure PowerShell 在[共用映射庫中](shared-image-galleries.md)創建映射版本，然後全域分發映射。 您還可以使用[Azure CLI](../linux/image-builder-gallery.md)執行此操作。

我們將使用 .json 範本來配置映射。 我們使用的 .json 檔在這裡： [armTemplateWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). 我們將下載並編輯範本的本地版本，因此本文使用本地 PowerShell 會話編寫。

要將映射分發到共用圖像庫，範本使用[共用圖像](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage)作為範本`distribute`部分的值。

Azure 映射產生器自動運行 sysprep 以泛化映射，這是一個通用的 sysprep 命令，如果需要，可以[重寫](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#vms-created-from-aib-images-do-not-create-successfully)該命令。 

請注意分層自訂的次數。 您可以在單個 Windows 映像上運行多達 8 次的 Sysprep 命令。 運行 Sysprep 8 次後，必須重新創建 Windows 映像。 有關詳細資訊，請參閱運行[Sysprep 的次數限制](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep)。 

> [!IMPORTANT]
> Azure 映射產生器當前處於公共預覽版中。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-the-features"></a>註冊功能
要在預覽期間使用 Azure 映射產生器，需要註冊新功能。

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

檢查功能註冊的狀態。

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

等到`RegistrationState`下一`Registered`步再進入下一步。

檢查您的供應商註冊。 確保每個返回`Registered`。

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

如果他們不返回`Registered`，請使用以下內容註冊提供程式：

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>建立變數

我們將反復使用一些資訊片段，因此我們將創建一些變數來存儲該資訊。 將變數的值（如`username`和`vmpassword`）替換為您自己的資訊。

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"
```



## <a name="create-the-resource-group"></a>建立資源群組

創建資源組並授予 Azure 映射產生器在該資源組中創建資源的許可權。

```powershell
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
New-AzRoleAssignment `
   -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 `
   -Scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup `
   -RoleDefinitionName Contributor
```



## <a name="create-the-shared-image-gallery"></a>建立共用映像庫

要將圖像產生器與共享圖像庫一起使用，您需要具有現有的圖像庫和圖像定義。 圖像產生器不會為您創建圖像庫和圖像定義。

如果還沒有要使用的庫和圖像定義，則從創建它們開始。 首先，創建一個圖像庫。

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>下載並配置範本

下載 .json 範本，並將其與變數一起配置。

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath

```


## <a name="create-the-image-version"></a>創建映射版本

您的範本必須提交到服務，這將下載任何從屬專案，如腳本，並將它們存儲在暫存資源組中，以*IT_* 為預綴。

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -api-version "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

要生成映射，您需要在範本上調用"運行"。

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

創建映射並將其複製到兩個區域可能需要一段時間。 等待此部件完成，然後再繼續創建 VM。

有關自動獲取映射生成狀態的選項的資訊，請參閱 GitHub 上的此範本的[Readme。](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query)


## <a name="create-the-vm"></a>建立 VM

從 Azure 映射產生器創建的圖像版本創建 VM。

獲取您創建的圖像版本。
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

在複製映射的第二個區域中創建 VM。

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>驗證自訂
使用創建 VM 時設置的使用者名和密碼創建與 VM 的遠端桌面連線。 在 VM 中，打開 cmd 提示並鍵入：

```console
dir c:\
```

您應該會看到在映射自訂期間`buildActions`創建的名為的目錄。


## <a name="clean-up-resources"></a>清除資源
如果要現在嘗試重新自訂映射版本以創建同一映射的新版本，**請跳過此步驟**，然後繼續[使用 Azure 映射產生器創建另一個映射版本](image-builder-gallery-update-image-version.md)。


這將刪除創建的映射以及所有其他資源檔。 在刪除資源之前，請確保已完成此部署。

請先刪除資源組範本，否則 AIB 使用的暫存資源組 *（IT_*） 將不會清理。

獲取映射範本的資源識別碼。 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

刪除圖像範本。

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

刪除資源組。

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

要瞭解如何更新創建的圖像版本，請參閱使用 Azure[映射產生器創建另一個映射版本](image-builder-gallery-update-image-version.md)。
