---
title: 尋找和使用 Azure Marketplace 映射和方案
description: 使用 Azure PowerShell 來尋找並使用 Marketplace VM 映射的發行者、供應專案、SKU、版本和方案資訊。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: 45e6b157dba5ef7410d8a5c0223fd3ecb52f39d0
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906262"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>使用 Azure PowerShell 尋找並使用 Azure Marketplace VM 映射     

本文描述如何使用 Azure PowerShell 在 Azure Marketplace 中尋找 Windows VM 映像。 然後，您可以在建立 VM 時指定 Marketplace 映射和方案資訊。

您也可以使用 [Azure Marketplace](https://azuremarketplace.microsoft.com/) 店面、[Azure 入口網站](https://portal.azure.com)或 [Azure CLI](../linux/cli-ps-findimage.md) 來瀏覽可用的映像和供應項目。 


[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]


## <a name="create-a-vm-from-vhd-with-plan-information"></a>使用方案資訊從 VHD 建立 VM

如果您現有的 VHD 是使用 Azure Marketplace 映射建立的，則當您從該 VHD 建立新的 VM 時，可能需要提供購買方案資訊。

如果您仍有原始 VM，或從相同映射建立的其他 VM，您可以使用 New-azvm 取得方案名稱、發行者和產品資訊。 此範例會在 *myResourceGroup* 資源群組中取得名為 *myVM* 的 VM，然後顯示購買方案資訊。

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

如果您在刪除原始 VM 之前未取得方案資訊，您可以 [提出支援要求](https://ms.portal.azure.com/#create/Microsoft.Support)。 他們將需要 VM 名稱、訂用帳戶識別碼，以及刪除作業的時間戳記。

若要使用 VHD 建立 VM，請參閱這篇文章 [從特製化 VHD 建立 vm](create-vm-specialized.md) ，並新增一行以將方案資訊新增至 VM 設定，其方式是使用 [>set-azvmplan](/powershell/module/az.compute/set-azvmplan) ，如下所示：

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```

## <a name="create-a-new-vm-from-a-marketplace-image"></a>從 marketplace 映射建立新的 VM

如果您已經有想要使用之映射的相關資訊，您可以將該資訊傳遞至 [>set-azvmsourceimage](/powershell/module/az.compute/set-azvmsourceimage) 指令程式，以將映射資訊新增至 VM 設定。 請參閱下一節，以搜尋並列出 marketplace 中可用的映射。

有些付費映射也需要您使用 [>set-azvmplan](/powershell/module/az.compute/set-azvmplan)來提供購買方案資訊。 

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$publisherName = "microsoft-ads"
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...
```

接著，您會將 VM 設定以及其他設定物件傳遞給 `New-AzVM` Cmdlet。 如需搭配 PowerShell 使用 VM 設定的詳細範例，請參閱此 [腳本](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1)。

如果您收到關於接受影像條款的訊息，請參閱本文稍後的「 [接受條款](#accept-the-terms) 」一節。

## <a name="list-images"></a>列出映像

在位置中尋找映像的其中一個方法是依序執行 [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) \(英文\)、[Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) \(英文\) 及 [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) \(英文\) Cmdlet：

1. 列出映像發行者。
2. 針對指定的發行者，列出其供應項目。
3. 針對指定的供應項目，列出其 SKU。

然後，針對選取的 SKU，執行 [Get-AzVMImage](/powershell/module/az.compute/get-azvmimage) \(英文\) 以列出要部署的版本。

1. 列出發行者：

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. 填入您選擇的發行者名稱並列出供應項目：

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. 填入您選擇的供應項目名稱並列出 SKU：

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. 填入您選擇的 SKU 名稱並取得映像版本：

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
從 `Get-AzVMImage` 命令的輸出中，您可以選取要部署新虛擬機器的版本映像。

下列範例顯示完整的命令序列及其輸出：

```powershell
$locName="West US"
Get-AzVMImagePublisher -Location $locName | Select PublisherName
```

部分輸出：

```
PublisherName
-------------
...
abiquo
accedian
accellion
accessdata-group
accops
Acronis
Acronis.Backup
actian-corp
actian_matrix
actifio
activeeon
adgs
advantech
advantech-webaccess
advantys
...
```

針對 MicrosoftWindowsServer 發佈者：

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
```

輸出：

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

針對 WindowsServer 供應項目：

```powershell
$offerName="WindowsServer"
Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
```

部分輸出：

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
2019-Datacenter
2019-Datacenter-Core
2019-Datacenter-Core-smalldisk
2019-Datacenter-Core-with-Containers
...
```

然後，針對 *2019-Datacenter* SKU：

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

現在您可以將選取的發行者、供應項目、SKU 和版本結合為 URN (以 : 分隔的值)。 當您使用 [New-AzVM](/powershell/module/az.compute/new-azvm) \(英文\) Cmdlet 建立 VM 時，請傳遞此 URN 與 `--image` 參數。 您可以視需要以 "latest" 取代 URN 中的版本號碼，以取得最新版的映像。

如果您使用 Resource Manager 範本來部署 VM，則需在 `imageReference` 屬性中個別設定映像參數。 請參閱[範本參考](/azure/templates/microsoft.compute/virtualmachines)。

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>檢視方案屬性

若要檢視映像的購買方案資訊，請執行 `Get-AzVMImage` Cmdlet。 如果輸出中的 `PurchasePlan` 屬性不是 `null`，在以程式設計方式部署之前，必須接受映像包含的條款。  

例如，*Windows Server 2016 Datacenter* 映像並沒有額外的條款，因此 `PurchasePlan` 資訊為 `null`：

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

輸出：

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

下列範例顯示 *資料科學虛擬機器-Windows 2016* 映射的類似命令，其中包含下列 `PurchasePlan` 屬性： `name` 、 `product` 和 `publisher` 。 有些映像也有 `promotion code` 屬性。 若要部署此映像，請參閱下列各節來接受條款，並啟用以程式設計方式部署。

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

輸出：

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>接受條款

若要檢視授權條款，請使用 [Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) \(英文\) Cmdlet，並傳入購買方案參數。 輸出會提供 Marketplace 映像的條款連結，並顯示您先前是否已接受條款。 在參數值中務必使用全小寫字母。

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

輸出：

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

使用 [Set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) \(英文\) Cmdlet 來接受或拒絕條款。 您只需針對映像的每個訂用帳戶接受一次條款。 在參數值中務必使用全小寫字母。 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

輸出：

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```



## <a name="next-steps"></a>後續步驟

若要使用基本映像資訊透過 `New-AzVM` Cmdlet 快速建立虛擬機器，請參閱[使用 PowerShell 來建立 Windows 虛擬機器](quick-create-powershell.md)。

如需使用 Azure Marketplace 映射在共用映射庫中建立自訂映射的詳細資訊，請參閱在 [建立映射時提供 Azure Marketplace 購買方案資訊](../marketplace-images.md)。
