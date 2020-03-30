---
title: 使用 Azure PowerShell 將 vhd 上載到 Azure
description: 瞭解如何通過直接上載將 vhd 上載到 Azure 託管磁片，並使用 Azure PowerShell 跨區域複製託管磁片。
author: roygara
ms.author: rogarana
ms.date: 03/13/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 883fea1e25ded26c35e96d11edd8f417e96db30e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369551"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>使用 Azure PowerShell 將 vhd 上載到 Azure

本文介紹如何將 vhd 從本地電腦上載到 Azure 託管磁片。 以前，您必須遵循一個涉及更複雜的過程，其中包括在存儲帳戶中暫存資料和管理該存儲帳戶。 現在，您不再需要管理存儲帳戶，也無需在其中暫存資料來上載 vhd。 相反，您可以創建一個空託管磁片，並將 vhd 直接上載到它。 這簡化了將本地 VM 上載到 Azure，並使您能夠將高達 32 TiB 的 vhd 直接上載到大型託管磁片。

如果要為 Azure 中的 IaaS VM 提供備份解決方案，我們建議您使用直接上載將客戶備份還原到託管磁片。 如果要將 VHD 從電腦外部上載到 Azure，則速度取決於本地頻寬。 如果使用 Azure VM，則頻寬將與標準 HDD 相同。

目前，標準硬碟、標準 SSD 和高級 SSD 託管磁片支援直接上傳。 目前不支援超 SSD。

## <a name="prerequisites"></a>Prerequisites

- 下載[最新版本的AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)。
- [安裝 Azure 電源外殼模組](/powershell/azure/install-Az-ps)。
- 如果要從本地上載 VHD：[已為 Azure 準備的](prepare-for-upload-vhd-image.md)固定大小 VHD，存儲在本地。
- 或者，如果要執行複製操作，則 Azure 中的託管磁片。

## <a name="create-an-empty-managed-disk"></a>創建空託管磁片

要將 vhd 上載到 Azure，您需要創建為此上載過程配置的空託管磁片。 在創建磁片之前，您應該知道有關這些磁片的其他資訊。

此類託管磁片具有兩種唯一狀態：

- ReadToUpload，這意味著磁片已準備好接收上載，但未生成[安全訪問簽名](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)（SAS）。
- ActiveUpload，這意味著磁片已準備好接收上載，並且已生成 SAS。

在上述任一狀態下，無論實際磁片類型如何，託管磁片都將按[標準 HDD 定價](https://azure.microsoft.com/pricing/details/managed-disks/)計費。 例如，P10 將計費為 S10。 在託管磁片上調用`revoke-access`之前，這一點將是正確的，這是將磁片連接到 VM 所必需的。

在為上載創建空標準 HDD 之前，您需要以要上載的 vhd 位元組為單位的檔案大小。 示例代碼將為您提供，但是，要自己使用：。 `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length` 指定 **-UploadSizeInBYtes**參數時使用此值。

現在，在本地 shell 上，通過在 **-CreateOption 參數**中指定**上傳**設置以及[New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) Cmdlet 中的 **-UploadSizeInBytes**參數，創建用於上載的空標準 HDD。 然後調用[New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0)創建磁片：

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

如果要上傳高級 SSD 或標準 SSD，請將**Standard_LRS**替換為**Premium_LRS**或**StandardSSD_LRS**。 不支援超 SSD。

現在，您已經創建了為上載過程配置的空託管磁片。 要將 vhd 上載到磁片，您需要一個可寫入的 SAS，以便您可以將其作為上載的目標。

要生成空託管磁片的可寫 SAS，請使用以下命令：

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>上傳 vhd

現在，您已為空託管磁片設置了 SAS，您可以使用它將託管磁片設置為上載命令的目標。

使用 AzCopy v10 通過指定生成的 SAS URI 將本地 VHD 檔上載到託管磁片。

此上載的輸送量與等效[標準 HDD](disks-types.md#standard-hdd)相同。 例如，如果大小等同于 S4，則輸送量將高達 60 MiB/s。 但是，如果您的尺寸相當於 S70，則輸送量將高達 500 MiB/s。

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

上載完成後，您不再需要向磁片寫入任何資料，請撤銷 SAS。 撤銷 SAS 將更改託管磁片的狀態，並允許您將磁片附加到 VM。

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>複製受控磁碟

直接上傳還簡化了複製託管磁片的過程。 可以在同一區域或跨區域（複製到其他區域）內進行複製。

以下腳本將為您執行此操作，該過程與前面描述的步驟類似，由於您正在使用現有磁片，因此存在一些差異。

> [!IMPORTANT]
> 當您從 Azure 提供以位元組為單位的磁片大小時，需要添加 512 的偏移量。 這是因為 Azure 在返回磁片大小時省略了頁腳。 如果不執行此操作，副本將失敗。 以下腳本已為您執行此操作。

將`<sourceResourceGroupHere>`、 `<sourceDiskNameHere>` `<targetDiskNameHere>`、 `<targetResourceGroupHere>` `<yourOSTypeHere>` 、`<yourTargetLocationHere>`和 （位置值的示例將為 uswest2） 替換為值，然後運行以下腳本以複製託管磁片。

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

現在，您已成功將 vhd 上載到託管磁片，您可以將磁片附加到 VM 並開始使用它。

要瞭解如何將資料磁片附加到 VM，請參閱我們有關主題的文章：[使用 PowerShell 將資料磁片附加到 Windows VM。](attach-disk-ps.md) 要將磁片用作作業系統磁片，請參閱[從專用磁片創建 Windows VM。](create-vm-specialized.md#create-the-new-vm)
