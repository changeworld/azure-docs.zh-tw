---
title: 使用 Azure PowerShell 將 vhd 上傳至 Azure
description: 瞭解如何使用 Azure PowerShell，透過直接上傳，將 vhd 上傳至 Azure 受控磁片，以及跨區域複製受控磁片。
author: roygara
ms.author: rogarana
ms.date: 03/13/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 883fea1e25ded26c35e96d11edd8f417e96db30e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79369551"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>使用 Azure PowerShell 將 vhd 上傳至 Azure

本文說明如何將 vhd 從本機電腦上傳至 Azure 受控磁片。 之前，您必須遵循更牽涉的程式，其中包含將您的資料放在儲存體帳戶中，並管理該儲存體帳戶。 現在，您不再需要管理儲存體帳戶，或在其中暫存資料來上傳 vhd。 相反地，您會建立空的受控磁片，並直接將 vhd 上傳至其中。 這可簡化將內部部署 Vm 上傳至 Azure 的工作，並可讓您直接將 vhd 上傳至 32 TiB 到大型受控磁片。

如果您在 Azure 中提供 IaaS Vm 的備份解決方案，建議您使用直接上傳來將客戶備份還原至受控磁片。 如果您要從 Azure 外部的電腦上傳 VHD，則速度會視您的本機頻寬而定。 如果您使用 Azure VM，則您的頻寬會與標準 Hdd 相同。

目前，標準 HDD、標準 SSD 和 premium SSD 受控磁片支援直接上傳。 Ultra Ssd 尚不支援此程式。

## <a name="prerequisites"></a>Prerequisites

- 下載最新[版本的 AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)。
- [安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。
- 如果您想要從內部部署上傳 VHD：已[針對 Azure 備妥的](prepare-for-upload-vhd-image.md)固定大小 vhd，並儲存在本機。
- 或者，如果您想要執行複製動作，則是 Azure 中的受控磁片。

## <a name="create-an-empty-managed-disk"></a>建立空的受控磁片

若要將您的 vhd 上傳至 Azure，您必須建立為此上傳程式設定的空受控磁片。 建立之前，您應該先瞭解這些磁片的一些額外資訊。

這種受控磁片有兩種獨特的狀態：

- ReadToUpload，這表示磁片已準備好接收上傳，但未產生任何[安全存取](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)簽章（SAS）。
- ActiveUpload，這表示磁片已準備好接收上傳，並已產生 SAS。

在上述任一種狀態中，不論實際的磁片類型為何，受控磁片都會以[標準 HDD 定價](https://azure.microsoft.com/pricing/details/managed-disks/)計費。 例如，P10 會以 S10 計費。 這會是 true，直到在受控磁片上呼叫 `revoke-access` 為止，若要將磁片連結至 VM，這是必要的。

建立空的標準 HDD 以進行上傳之前，您需要您想要上傳之 vhd 的檔案大小（以位元組為單位）。 範例程式碼會為您取得，但是您也可以使用： `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`。 指定 **-UploadSizeInBytes**參數時，會使用這個值。

現在，在您的本機 shell 上，藉由在 **-CreateOption**參數中指定**上傳**設定，並在[new-azdiskconfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0)指令程式中指定 **-UploadSizeInBytes**參數，來建立要上傳的空標準 HDD。 然後呼叫[new-azdisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0)以建立磁片：

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

如果您想要上傳 premium SSD 或標準 SSD，請將**Standard_LRS**取代為**Premium_LRS**或**StandardSSD_LRS**。 尚未支援 Ultra SSD。

您現在已建立一個為上傳程式設定的空白受控磁片。 若要將 vhd 上傳至磁片，您將需要可寫入的 SAS，以便將它當做您上傳的目的地。

若要產生空受控磁片的可寫入 SAS，請使用下列命令：

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>上傳 vhd

既然您有空的受控磁片的 SAS，您可以使用它將受控磁片設定為上傳命令的目的地。

使用 AzCopy v10，藉由指定您產生的 SAS URI，將本機 VHD 檔案上傳至受控磁片。

這項上傳與對等的[標準 HDD](disks-types.md#standard-hdd)具有相同的輸送量。 例如，如果您的大小等於 S4，則輸送量最高可達 60 MiB/秒。 但是，如果您的大小等於 S70，則輸送量最高可達 500 MiB/秒。

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

上傳完成之後，而且您不再需要將任何其他資料寫入磁片，請撤銷 SAS。 撤銷 SAS 將會變更受控磁片的狀態，並可讓您將磁片連結至 VM。

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>複製受控磁碟

直接上傳也會簡化複製受控磁片的程式。 您可以在相同區域內或跨區域（到另一個區域）複製。

下列腳本會為您執行此作業，此程式類似于先前所述的步驟，因為您使用的是現有的磁片，所以有一些差異。

> [!IMPORTANT]
> 當您從 Azure 提供受控磁片的磁片大小（以位元組為單位）時，您需要新增512的位移。 這是因為在傳回磁片大小時，Azure 會省略頁尾。 如果您不這麼做，複製將會失敗。 下列腳本已經為您執行這項工作。

使用您的值取代 `<sourceResourceGroupHere>`、`<sourceDiskNameHere>`、`<targetDiskNameHere>`、`<targetResourceGroupHere>`、`<yourOSTypeHere>` 和 `<yourTargetLocationHere>` （位置值的範例），然後執行下列腳本以複製受控磁片。

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>後續步驟

既然您已成功將 vhd 上傳至受控磁片，您可以將磁片連結至 VM 並開始使用它。

若要瞭解如何將資料磁片連結至 VM，請參閱主旨：[使用 PowerShell 將資料磁片連結至 WINDOWS VM](attach-disk-ps.md)中的文章。 若要使用磁片做為 OS 磁片，請參閱[從特製化磁片建立 WINDOWS VM](create-vm-specialized.md#create-the-new-vm)。
