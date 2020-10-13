---
title: 將 VHD 上傳至 Azure 或跨區域複製磁片-Azure CLI
description: 瞭解如何使用 Azure CLI，透過直接上傳，將 VHD 上傳至 Azure 受控磁片，並跨區域複製受控磁片。
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 473e87904742395eca6b7eeba0875cd93789104d
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978980"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>將 VHD 上傳至 Azure，或將受控磁碟複製到另一個區域-Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>必要條件

- 下載 [AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)的最新版本。
- [安裝 Azure CLI](/cli/azure/install-azure-cli)。
- 如果您想要從內部部署上傳 VHD：已 [針對 Azure 準備的](../windows/prepare-for-upload-vhd-image.md)固定大小 vhd，儲存在本機。
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

您需要上傳的 VHD 檔案大小（以位元組為單位），才能建立空白的標準 HDD 以進行上傳。 若要取得該方法，您可以使用 `wc -c <yourFileName>.vhd` 或 `ls -al <yourFileName>.vhd` 。 指定 **--upload-size-bytes** 參數時，會使用這個值。

藉由在[disk create](/cli/azure/disk#az-disk-create) Cmdlet 中指定 **--** ----------------------- **bytes**參數，來建立上傳的空白標準 HDD：

`<yourdiskname>` `<yourresourcegroupname>` `<yourregion>` 以您選擇的值取代、和。 `--upload-size-bytes`參數包含的範例值 `34359738880` ，請將它取代為適合您的值。

> [!TIP]
> 如果您要建立 OS 磁片，請將--hyper-v 世代新增 <yourGeneration> 至 `az disk create` 。

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

如果您想要上傳 premium SSD 或標準 SSD，請以**premium_LRS**或**standardssd_lrs**取代**standard_lrs** 。 目前不支援 Ultra 磁片。

現在您已建立為上傳程式設定的空受控磁片，您可以將 VHD 上傳至該磁片。 若要將 VHD 上傳到磁片，您需要可寫入的 SAS，以便您可以將它參考為您上傳的目的地。

若要產生空白受控磁片的可寫入 SAS，請取代 `<yourdiskname>` 和 `<yourresourcegroupname>` ，然後使用下列命令：

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

範例傳回值：

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>上傳 VHD

現在您已有空白受控磁片的 SAS，您可以使用它來將受控磁片設定為上傳命令的目的地。

使用 AzCopy v10，藉由指定您產生的 SAS URI，將本機 VHD 檔案上傳至受控磁片。

此上傳與對等的 [標準 HDD](../disks-types.md#standard-hdd)具有相同的輸送量。 例如，如果您的大小等於 S4，則最多會有 60 MiB/秒的輸送量。 但是，如果您有相當於 S70 的大小，則最多會有 500 MiB/秒的輸送量。

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

上傳完成之後，您再也不需要再將任何資料寫入磁片，請撤銷 SAS。 撤銷 SAS 將會變更受控磁片的狀態，並可讓您將磁片連結至 VM。

取代 `<yourdiskname>` 和 `<yourresourcegroupname>` ，然後使用下列命令讓磁片可供使用：

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>複製受控磁碟

直接上傳也可簡化複製受控磁片的程式。 您可以在相同區域或跨區域 (中複製到另一個區域) 。

下列腳本會為您執行這項作業，此程式與先前所述的步驟類似，因為您是使用現有的磁片，所以有一些差異。

> [!IMPORTANT]
> 當您在 Azure 中提供受控磁片的磁片大小（以位元組為單位）時，您必須新增512的位移。 這是因為 Azure 會在返回磁片大小時省略頁尾。 如果您沒有這麼做，複製將會失敗。 下列腳本已為您執行這項工作。

取代 `<sourceResourceGroupHere>` 、、 `<sourceDiskNameHere>` `<targetDiskNameHere>` 、 `<targetResourceGroupHere>` 和 `<yourTargetLocationHere>` (位置值的範例會以您的值) uswest2，然後執行下列腳本，以便複製受控磁片。

> [!TIP]
> 如果您要建立 OS 磁片，請將--hyper-v 世代新增 <yourGeneration> 至 `az disk create` 。

```azurecli
sourceDiskName=<sourceDiskNameHere>
sourceRG=<sourceResourceGroupHere>
targetDiskName=<targetDiskNameHere>
targetRG=<targetResourceGroupHere>
targetLocation=<yourTargetLocationHere>

sourceDiskSizeBytes=$(az disk show -g $sourceRG -n $sourceDiskName --query '[diskSizeBytes]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocation --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI=$(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>後續步驟

既然您已成功將 VHD 上傳至受控磁片，您可以將磁片以資料磁片的形式連結 [至現有的 vm](add-disk.md) ，或 [將磁片連接至 vm 作為 OS 磁片](upload-vhd.md#create-the-vm)，以建立新的 vm。