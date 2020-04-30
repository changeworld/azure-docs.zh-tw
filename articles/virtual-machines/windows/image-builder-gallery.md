---
title: 使用 Azure 映射產生器搭配適用于 Windows Vm 的映射庫（預覽）
description: 使用 Azure 映射產生器和 Azure PowerShell 建立 Azure 共用資源庫映射版本。
author: cynthn
ms.author: cynthn
ms.date: 01/14/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: 48eff11facf0f1432534d61f003f61e6755caf33
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869526"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>預覽：建立 Windows 映像並將它散發到共用映射資源庫 

本文說明如何使用 Azure 映射產生器，並 Azure PowerShell，以在[共用映射資源庫](shared-image-galleries.md)中建立映射版本，然後全域散發映射。 您也可以使用[Azure CLI](../linux/image-builder-gallery.md)來執行此動作。

我們將使用 json 範本來設定映射。 我們所使用的. json 檔案位於這裡： [armTemplateWinSIG。](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json) 我們將下載並編輯範本的本機版本，因此本文是使用本機 PowerShell 會話撰寫的。

若要將映射發佈到共用映射資源庫，範本會使用[sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage)作為範本`distribute`區段的值。

Azure 映射產生器會自動執行 sysprep 以將映射一般化，這是一般的 sysprep 命令，您可以視需要覆[寫](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#vms-created-from-aib-images-do-not-create-successfully)。 

請注意您分層自訂的次數。 您可以在單一 Windows 映像上執行 Sysprep 命令最多8次。 執行 Sysprep 8 次之後，您必須重新建立 Windows 映像。 如需詳細資訊，請參閱[您可以執行 Sysprep 多少次的限制](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep)。 

> [!IMPORTANT]
> Azure 映射產生器目前為公開預覽版。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="register-the-features"></a>註冊功能
若要在預覽期間使用 Azure 映射產生器，您必須註冊新功能。

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

檢查功能註冊的狀態。

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

請等待`RegistrationState` ， `Registered`再移至下一個步驟。

檢查您的提供者註冊。 請確定每個`Registered`都會傳回。

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
```

如果未傳回`Registered`，請使用下列內容來註冊提供者：

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="create-variables"></a>建立變數

我們會重複使用一些資訊，因此我們將建立一些變數來儲存該資訊。 以您自己的資訊取代變數的`username`值`vmpassword`，例如和。

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

建立資源群組，並授與 Azure 映射產生器許可權，以在該資源群組中建立資源。

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

若要將影像產生器與共享映射庫搭配使用，您需要有現有的映射庫和映射定義。 影像產生器不會為您建立映射庫和映射定義。

如果您還沒有資源庫和映射定義可供使用，請先加以建立。 首先，建立映射庫。

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



## <a name="download-and-configure-the-template"></a>下載並設定範本

下載 json 範本，並以您的變數加以設定。

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


## <a name="create-the-image-version"></a>建立映射版本

您的範本必須提交至服務，這會下載任何相依成品（例如腳本），並將其儲存在預備資源群組中，並在前面加上*IT_*。

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -api-version "2019-05-01-preview" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

若要建立映射，您必須在範本上叫用「執行」。

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2019-05-01-preview" `
   -Action Run
```

建立映射並將它複寫到這兩個區域可能需要一段時間。 等到此部分完成後，再繼續建立 VM。

如需自動取得映射組建狀態之選項的相關資訊，請參閱 GitHub 上此範本的[讀我檔案](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/readme.md#get-status-of-the-image-build-and-query)。


## <a name="create-the-vm"></a>建立 VM

從 Azure 映射產生器所建立的映射版本建立 VM。

取得您所建立的映射版本。
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

在複製映射的第二個區域中建立 VM。

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
使用您在建立 VM 時所設定的使用者名稱和密碼，建立 VM 的遠端桌面連線。 在 VM 中，開啟命令提示字元，然後輸入：

```console
dir c:\
```

您應該會看到名為`buildActions`的目錄，它是在映射自訂期間所建立。


## <a name="clean-up-resources"></a>清除資源
如果您想要立即嘗試重新自訂映射版本，以建立相同映射的新版本，請**略過此步驟**，並繼續[使用 Azure 映射產生器來建立另一個映射版本](image-builder-gallery-update-image-version.md)。


這會刪除已建立的映射，以及其他所有資源檔。 請先確定您已完成此部署，再刪除資源。

先刪除資源群組範本，否則 AIB 所使用的預備資源群組（*IT_*）將不會清除。

取得映射範本的 ResourceID。 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2019-05-01-preview"
```

刪除影像範本。

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

刪除資源群組。

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

若要瞭解如何更新您所建立的映射版本，請參閱[使用 Azure 映射產生器來建立另一個映射版本](image-builder-gallery-update-image-version.md)。
