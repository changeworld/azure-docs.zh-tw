---
title: 將 VHD 上載到 Azure 或跨區域複製磁碟 - Azure PowerShell
description: 瞭解如何透過直接上載將 VHD 載到 Azure 託管磁碟,並使用 Azure PowerShell 跨區域複製託管磁碟。
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 7c66507989357569828d4ef933cfdca735f71570
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085406"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>將 VHD 上載到 Azure 或將託管磁碟複製到其他區域 - Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Prerequisites

- 下載[版本的AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)。
- [安裝 Azure 電源外殼模組](/powershell/azure/install-Az-ps)。
- 如果要從本地上載 VHD:[已為 Azure 準備的](prepare-for-upload-vhd-image.md)固定大小 VHD,存儲在本地。
- 或者,如果要執行複製操作,則 Azure 中的託管磁碟。

## <a name="getting-started"></a>開始使用

如果希望透過 GUI 上載磁碟,可以使用 Azure 儲存資源管理器執行此操作。 有關詳細資訊,請參閱:[使用 Azure 儲存資源管理員管理 Azure 託管磁碟](disks-use-storage-explorer-managed-disks.md)

要將 VHD 上傳到 Azure,您需要建立為此上傳過程配置的空託管磁碟。 在建立磁碟之前,您應該知道有關這些磁碟的其他資訊。

此類託管磁碟具有兩種唯一狀態:

- ReadToUpload,這意味著磁碟已準備好接收上載,但未生成[安全訪問簽名](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)(SAS)。
- ActiveUpload,這意味著磁碟已準備好接收上載,並且已生成 SAS。

> [!NOTE]
> 在上述任一狀態下,無論實際磁碟類型如何,託管磁碟都將按[標準 HDD 定價](https://azure.microsoft.com/pricing/details/managed-disks/)計費。 例如,P10 將計費為 S10。 在託管磁碟上調用`revoke-access`之前,這一點將是正確的,這是將磁碟連接到 VM 所必需的。

## <a name="create-an-empty-managed-disk"></a>建立空白管磁碟

在建立用於上載的空標準 HDD 之前,您需要要上載的 VHD 的檔案大小(以位元組為單位)。 範例代碼將為您提供,但是,要自己使用:。 `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length` 指定 **-UploadSizeInBYtes**參數時使用此值。

現在,在本地 shell 上,通過在 **-CreateOption 參數**中指定**上傳**設定以及[New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) cmdlet 中的 **-UploadSizeInBytes**參數,創建用於上傳的空標準 HDD。 然後調用[New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0)創建磁碟。

取代`<yourdiskname>``<yourresourcegroupname>`,`<yourregion>`然後執行以下指令:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname' -DiskName '<yourdiskname>' -Disk $diskconfig
```

如果要上傳進階 SSD 或標準 SSD,請將**Standard_LRS**取代為**Premium_LRS**或**StandardSSD_LRS**。 不支援超磁碟。

現在,您已經創建了為上載過程配置的空託管磁碟,您可以將 VHD 上傳到該磁碟。 要將 VHD 上傳到磁碟,您需要一個可寫入的 SAS,以便您可以將其作為上傳的目標。

要產生空託管磁碟的可寫 SAS,請取代`<yourdiskname>`和`<yourresourcegroupname>`,然後使用以下指令:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>上傳 VHD

現在,您已為空託管磁碟設置了 SAS,您可以使用它將託管磁碟設置為上載命令的目標。

使用 AzCopy v10 透過指定生成的 SAS URI 將本地 VHD 檔上載到託管磁碟。

此上載的輸送量與等效[標準 HDD](disks-types.md#standard-hdd)相同。 例如,如果大小等同於 S4,則吞吐量將高達 60 MiB/s。 但是,如果您的尺寸相當於 S70,則輸送量將高達 500 MiB/s。

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

上載完成後,您不再需要向磁碟寫入任何數據,請撤銷 SAS。 復原 SAS 將更改託管磁碟的狀態,並允許您將磁碟附加到 VM。

取代`<yourdiskname>``<yourresourcegroupname>`與,然後執行以下指令:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>複製受控磁碟

直接上傳還簡化了複製託管磁碟的過程。 您可以在同一區域內複製或將託管磁碟複製到其他區域。

遵循的腳本將為您執行此操作,該過程與前面描述的步驟類似,由於您正在使用現有磁碟,因此存在一些差異。

> [!IMPORTANT]
> 當您從 Azure 提供以位元組為單位的磁碟大小時,需要添加 512 的偏移量。 這是因為 Azure 在返回磁碟大小時省略了頁腳。 如果不執行此操作,副本將失敗。 以下文本已為您執行此操作。

將`<sourceResourceGroupHere>`、 `<sourceDiskNameHere>` `<targetDiskNameHere>` `<targetResourceGroupHere>` `<yourOSTypeHere>` 、`<yourTargetLocationHere>`、 和位置值的範例將為 uswest2 取代為值,然後執行以下文稿以複製託管磁碟。

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

現在,您已成功將 VHD 上傳到託管磁碟,您可以將磁碟附加到 VM 並開始使用它。

要瞭解如何將數據磁碟附加到 VM,請參閱我們有關主題的文章:[使用 PowerShell 將數據磁碟附加到 Windows VM。](attach-disk-ps.md) 要將磁碟用作作業系統磁碟,請參閱[從專用磁碟創建 Windows VM。](create-vm-specialized.md#create-the-new-vm)
