---
title: 使用 Azure CLI 上載 VHD
description: 瞭解如何通過直接上載將 vhd 上載到 Azure 託管磁片，並使用 Azure CLI 跨區域複製託管磁片。
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/13/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: d89a4279d425e4b12e92aae81edfd6c1514c3eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062672"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>使用 Azure CLI 將 vhd 上載到 Azure

本文介紹如何將 vhd 從本地電腦上載到 Azure 託管磁片。 以前，您必須遵循一個涉及更複雜的過程，其中包括在存儲帳戶中暫存資料和管理該存儲帳戶。 現在，您不再需要管理存儲帳戶，也無需在其中暫存資料來上載 vhd。 相反，您可以創建一個空託管磁片，並將 vhd 直接上載到它。 這簡化了將本地 VM 上載到 Azure，並使您能夠將高達 32 TiB 的 vhd 直接上載到大型託管磁片。

如果要為 Azure 中的 IaaS VM 提供備份解決方案，我們建議您使用直接上載將客戶備份還原到託管磁片。 如果要將 VHD 從電腦外部上載到 Azure，速度將取決於本地頻寬。 如果使用 Azure VM，則頻寬將與標準 HDD 相同。

目前，標準硬碟、標準 SSD 和高級 SSD 託管磁片支援直接上傳。 目前不支援超 SSD。

## <a name="prerequisites"></a>Prerequisites

- 下載[最新版本的AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)。
- [安裝 Azure CLI](/cli/azure/install-azure-cli)。
- 存儲在本地的 vhd 檔
- 如果要從本地上載 vhd：[已為 Azure 準備的](../windows/prepare-for-upload-vhd-image.md)固定大小 vhd，存儲在本地。
- 或者，如果要執行複製操作，則 Azure 中的託管磁片。

## <a name="create-an-empty-managed-disk"></a>創建空託管磁片

要將 vhd 上載到 Azure，您需要創建為此上載過程配置的空託管磁片。 在創建磁片之前，您應該知道有關這些磁片的其他資訊。

此類託管磁片具有兩種唯一狀態：

- ReadToUpload，這意味著磁片已準備好接收上載，但未生成[安全訪問簽名](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)（SAS）。
- ActiveUpload，這意味著磁片已準備好接收上載，並且已生成 SAS。

在上述任一狀態下，無論實際磁片類型如何，託管磁片都將按[標準 HDD 定價](https://azure.microsoft.com/pricing/details/managed-disks/)計費。 例如，P10 將計費為 S10。 在託管磁片上調用`revoke-access`之前，這一點將是正確的，這是將磁片連接到 VM 所必需的。

在創建用於上載的空標準 HDD 之前，需要以位元組為單位，將要上載的 vhd 的檔案大小。 要達到這一點，可以使用`wc -c <yourFileName>.vhd`或`ls -al <yourFileName>.vhd`。 指定 **--upload 大小位元組**參數時使用此值。

通過在磁片中指定 **--for 上傳**參數和 **--upload 大小位元組**參數創建用於上載的空標準 HDD[創建](/cli/azure/disk#az-disk-create)Cmdlet：

```azurecli
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

如果要上傳高級 SSD 或標準 SSD，請將**standard_lrs**替換為**premium_LRS**或**standardssd_lrs**。 不支援超 SSD。

現在，您已經創建了為上載過程配置的空託管磁片。 要將 vhd 上載到磁片，您需要一個可寫入的 SAS，以便您可以將其作為上載的目標。

要生成空託管磁片的可寫 SAS，請使用以下命令：

```azurecli
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

採樣傳回值：

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>上傳 vhd

現在，您已為空託管磁片設置了 SAS，您可以使用它將託管磁片設置為上載命令的目標。

使用 AzCopy v10 通過指定生成的 SAS URI 將本地 VHD 檔上載到託管磁片。

此上載的輸送量與等效[標準 HDD](disks-types.md#standard-hdd)相同。 例如，如果大小等同于 S4，則輸送量將高達 60 MiB/s。 但是，如果您的尺寸相當於 S70，則輸送量將高達 500 MiB/s。

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

上載完成後，您不再需要向磁片寫入任何資料，請撤銷 SAS。 撤銷 SAS 將更改託管磁片的狀態，並允許您將磁片附加到 VM。

```azurecli
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="copy-a-managed-disk"></a>複製受控磁碟

直接上傳還簡化了複製託管磁片的過程。 可以在同一區域或跨區域（複製到其他區域）內進行複製。

以下腳本將為您執行此操作，該過程與前面描述的步驟類似，由於您正在使用現有磁片，因此存在一些差異。

> [!IMPORTANT]
> 當您從 Azure 提供以位元組為單位的磁片大小時，需要添加 512 的偏移量。 這是因為 Azure 在返回磁片大小時省略了頁腳。 如果不執行此操作，副本將失敗。 以下腳本已為您執行此操作。

將`<sourceResourceGroupHere>`、 `<sourceDiskNameHere>` `<targetDiskNameHere>`、 `<targetResourceGroupHere>`、`<yourTargetLocationHere>`和 （位置值的示例將為 uswest2） 替換為值，然後運行以下腳本以複製託管磁片。

```azurecli
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[uniqueId]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>後續步驟

現在，您已成功將 vhd 上載到託管磁片，您可以將磁片作為[資料磁片附加到現有 VM，](add-disk.md)或[將磁片作為 OS 磁片附加到 VM，](upload-vhd.md#create-the-vm)以創建新的 VM。 

