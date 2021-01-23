---
title: 將 VHD 上傳至 Azure 或跨區域複製磁片-Azure PowerShell
description: 瞭解如何使用 Azure PowerShell，透過直接上傳將 VHD 上傳至 Azure 受控磁片，並跨區域複製受控磁片。
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 1655c48eeb9227bf934c7fd9bb37610327b2b98c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736266"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>將 VHD 上傳至 Azure，或將受控磁碟複製到另一個區域-Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Prerequisites

- 下載 [AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)的最新版本。
- [安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。
- 如果您想要從內部部署上傳 VHD：已 [針對 Azure 準備的](prepare-for-upload-vhd-image.md)固定大小 vhd，儲存在本機。
- 或者，如果您想要執行複製動作，則為 Azure 中的受控磁片。

## <a name="getting-started"></a>開始使用

如果您想要透過 GUI 上傳磁片，您可以使用 Azure 儲存體總管。 如需詳細資料，請參閱： [使用 Azure 儲存體總管管理 Azure 受控磁片](../disks-use-storage-explorer-managed-disks.md)

若要將您的 VHD 上傳至 Azure，您必須建立針對此上傳程式所設定的空受控磁片。 建立之前，您應該先瞭解這些磁片的其他相關資訊。

這種受控磁片有兩種獨特的狀態：

- ReadToUpload，這表示磁片已準備好接收上傳，但未產生任何 [安全存取](../../storage/common/storage-sas-overview.md) 簽章 (SAS) 。
- ActiveUpload，這表示磁片已準備好接收上傳，而且已產生 SAS。

> [!NOTE]
> 在這兩種狀態下，無論實際的磁片類型為何，受控磁片都會以 [標準 HDD 定價](https://azure.microsoft.com/pricing/details/managed-disks/)計費。 例如，P10 會以 S10 的方式計費。 在 `revoke-access` 受控磁片上呼叫時，這會是 true，必須要有此值，才能將磁片連結至 VM。

## <a name="create-an-empty-managed-disk"></a>建立空的受控磁片

您需要上傳的 VHD 檔案大小（以位元組為單位），才能建立空白的標準 HDD 以進行上傳。 範例程式碼會為您取得，但您可以自行使用： `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length` 。 指定 **-UploadSizeInBytes** 參數時，會使用這個值。

現在，在您的本機 shell 上，藉由在 **-CreateOption** 參數中指定 **上傳** 設定，以及 [New-azdiskconfig](/powershell/module/az.compute/new-azdiskconfig) Cmdlet 中的 **-UploadSizeInBytes** 參數，來建立要上傳的空白標準 HDD。 然後呼叫 [>get-azdisk](/powershell/module/az.compute/new-azdisk) 來建立磁片。

取代 `<yourdiskname>` 、 `<yourresourcegroupname>` ， `<yourregion>` 然後執行下列命令：

> [!TIP]
> 如果您要建立 OS 磁片，請將-HyperVGeneration ' <yourGeneration> ' 新增至 `New-AzDiskConfig` 。

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname' -DiskName '<yourdiskname>' -Disk $diskconfig
```

如果您想要上傳 premium SSD 或標準 SSD，請以 **Premium_LRS** 或 **StandardSSD_LRS** 取代 **Standard_LRS** 。 目前尚不支援 Ultra 磁片。

現在您已建立為上傳程式設定的空受控磁片，您可以將 VHD 上傳至該磁片。 若要將 VHD 上傳到磁片，您需要可寫入的 SAS，以便您可以將它參考為您上傳的目的地。

若要產生空白受控磁片的可寫入 SAS，請取代 `<yourdiskname>` 和 `<yourresourcegroupname>` ，然後使用下列命令：

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>上傳 VHD

現在您已有空白受控磁片的 SAS，您可以使用它來將受控磁片設定為上傳命令的目的地。

使用 AzCopy v10，藉由指定您產生的 SAS URI，將本機 VHD 檔案上傳至受控磁片。

此上傳與對等的 [標準 HDD](../disks-types.md#standard-hdd)具有相同的輸送量。 例如，如果您的大小等於 S4，則最多會有 60 MiB/秒的輸送量。 但是，如果您有相當於 S70 的大小，則最多會有 500 MiB/秒的輸送量。

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

上傳完成之後，您再也不需要再將任何資料寫入磁片，請撤銷 SAS。 撤銷 SAS 將會變更受控磁片的狀態，並可讓您將磁片連結至 VM。

取代 `<yourdiskname>` and `<yourresourcegroupname>` ，然後執行下列命令：

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>複製受控磁碟

直接上傳也可簡化複製受控磁片的程式。 您可以在相同區域內複製，或將受控磁碟複製到另一個區域。

下列腳本會為您執行這項作業，此程式類似于稍早所述的步驟，但有一些差異，因為您正在使用現有的磁片。

> [!IMPORTANT]
> 當您在 Azure 中提供受控磁片的磁片大小（以位元組為單位）時，您必須新增512的位移。 這是因為 Azure 會在返回磁片大小時省略頁尾。 如果您沒有這麼做，複製將會失敗。 下列腳本已為您執行這項工作。

取代 `<sourceResourceGroupHere>` 、、 `<sourceDiskNameHere>` `<targetDiskNameHere>` 、 `<targetResourceGroupHere>` `<yourOSTypeHere>` 和 `<yourTargetLocationHere>` (位置值的範例會以您的值) uswest2，然後執行下列腳本，以便複製受控磁片。

> [!TIP]
> 如果您要建立 OS 磁片，請將-HyperVGeneration ' <yourGeneration> ' 新增至 `New-AzDiskConfig` 。

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

現在您已成功將 VHD 上傳至受控磁片，您可以將磁片連結至 VM 並開始使用。

若要瞭解如何將資料磁片連結至 VM，請參閱下列主題的文章： [使用 PowerShell 將資料磁片連結至 WINDOWS VM](attach-disk-ps.md)。 若要使用磁片作為 OS 磁片，請參閱 [從特製化磁片建立 WINDOWS VM](create-vm-specialized.md#create-the-new-vm)。
