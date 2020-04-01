---
title: 將 VHD 上載到 Azure 或跨區域複製磁碟 - Azure CLI
description: 瞭解如何通過直接上載將 VHD 上傳到 Azure 託管磁碟,並使用 Azure CLI 跨區域複製託管磁碟。
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 6813206aebe67e4e6d2afd0d9c78d03f0c20c952
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420955"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>將 VHD 上載到 Azure 或將託管磁碟複製到其他區域 - Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Prerequisites

- 下載[版本的AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)。
- [安裝 Azure CLI](/cli/azure/install-azure-cli)。
- 如果要從本地上載 VHD:[已為 Azure 準備的](../windows/prepare-for-upload-vhd-image.md)固定大小 VHD,存儲在本地。
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

在建立用於上載的空標準 HDD 之前,您需要要上載的 VHD 的檔案大小(以位元組為單位)。 要達到這一點,可以使用`wc -c <yourFileName>.vhd``ls -al <yourFileName>.vhd`或 。 指定 **--upload 大小位元組**參數時使用此值。

透過磁碟中指定 **--for 上傳**參數與 **--upload 大小位元組**參數建立用於上傳的空標準 HDD[建立](/cli/azure/disk#az-disk-create)cmdlet:

用`<yourdiskname>`您選擇`<yourresourcegroupname>`的`<yourregion>`值 替換 。。 參數`--upload-size-bytes``34359738880`包含的範例值 , 將將取代為適合您的值。

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

如果要上傳進階 SSD 或標準 SSD,請將**standard_lrs**取代為**premium_LRS**或**standardssd_lrs**。 目前不支援超磁碟。

現在,您已經創建了為上載過程配置的空託管磁碟,您可以將 VHD 上傳到該磁碟。 要將 VHD 上傳到磁碟,您需要一個可寫入的 SAS,以便您可以將其作為上傳的目標。

要產生空託管磁碟的可寫 SAS,請取代`<yourdiskname>`和`<yourresourcegroupname>`,然後使用以下指令:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

取樣返回值:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>上傳 VHD

現在,您已為空託管磁碟設置了 SAS,您可以使用它將託管磁碟設置為上載命令的目標。

使用 AzCopy v10 透過指定生成的 SAS URI 將本地 VHD 檔上載到託管磁碟。

此上載的輸送量與等效[標準 HDD](disks-types.md#standard-hdd)相同。 例如,如果大小等同於 S4,則吞吐量將高達 60 MiB/s。 但是,如果您的尺寸相當於 S70,則輸送量將高達 500 MiB/s。

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

上載完成後,您不再需要向磁碟寫入任何數據,請撤銷 SAS。 復原 SAS 將更改託管磁碟的狀態,並允許您將磁碟附加到 VM。

取代`<yourdiskname>``<yourresourcegroupname>`與,然後使用以下指令讓磁碟可用:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>複製受控磁碟

直接上傳還簡化了複製託管磁碟的過程。 可以在同一區域或跨區域(複製到其他區域)內進行複製。

以下文本將為您執行此操作,該過程與前面描述的步驟類似,由於您正在使用現有磁碟,因此存在一些差異。

> [!IMPORTANT]
> 當您從 Azure 提供以位元組為單位的磁碟大小時,需要添加 512 的偏移量。 這是因為 Azure 在返回磁碟大小時省略了頁腳。 如果不執行此操作,副本將失敗。 以下文本已為您執行此操作。

將`<sourceResourceGroupHere>`、 `<sourceDiskNameHere>` `<targetDiskNameHere>` `<targetResourceGroupHere>`、`<yourTargetLocationHere>`、 和位置值的範例將為 uswest2 取代為值,然後執行以下文稿以複製託管磁碟。

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

現在,您已成功將 VHD 上載到託管磁碟,您可以將磁碟作為[資料磁碟附加到現有 VM,](add-disk.md)或[將磁碟作為 OS 磁碟附加到 VM,](upload-vhd.md#create-the-vm)以建立新的 VM。 

