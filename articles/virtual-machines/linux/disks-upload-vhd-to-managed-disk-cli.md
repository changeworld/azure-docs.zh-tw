---
title: 將 VHD 上傳至 Azure 或跨區域複製磁片-Azure CLI
description: 瞭解如何使用 Azure CLI，透過直接上傳，將 VHD 上傳至 Azure 受控磁片，以及跨區域複製受控磁片。
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 8656f0396aff7f20c867a5fae3d929236a3aa0d5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86510441"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>將 VHD 上傳至 Azure，或將受控磁碟複製到另一個區域-Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>先決條件

- 下載最新[版本的 AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)。
- [安裝 Azure CLI](/cli/azure/install-azure-cli)。
- 如果您想要從內部部署上傳 VHD：已[針對 Azure 備妥的](../windows/prepare-for-upload-vhd-image.md)固定大小 vhd，並儲存在本機。
- 或者，如果您想要執行複製動作，則是 Azure 中的受控磁片。

## <a name="getting-started"></a>開始使用

如果您想要透過 GUI 上傳磁片，您可以使用 Azure 儲存體總管。 如需詳細資訊，請參閱：[使用 Azure 儲存體總管來管理 Azure 受控磁片](disks-use-storage-explorer-managed-disks.md)

若要將您的 VHD 上傳至 Azure，您必須建立為此上傳程式設定的空受控磁片。 建立之前，您應該先瞭解這些磁片的一些額外資訊。

這種受控磁片有兩種獨特的狀態：

- ReadToUpload，這表示磁片已準備好接收上傳，但未產生任何[安全存取](../../storage/common/storage-sas-overview.md)簽章（SAS）。
- ActiveUpload，這表示磁片已準備好接收上傳，並已產生 SAS。

> [!NOTE]
> 在上述任一種狀態中，不論實際的磁片類型為何，受控磁片都會以[標準 HDD 定價](https://azure.microsoft.com/pricing/details/managed-disks/)計費。 例如，P10 會以 S10 計費。 在對受控磁片呼叫之前，這會是 true `revoke-access` ，這是將磁片連結至 VM 所需的值。

## <a name="create-an-empty-managed-disk"></a>建立空的受控磁片

在您可以建立空的標準 HDD 以進行上傳之前，您需要上傳的 VHD 檔案大小（以位元組為單位）。 若要取得此項，您可以使用 `wc -c <yourFileName>.vhd` 或 `ls -al <yourFileName>.vhd` 。 指定 **--upload-size-bytes**參數時，會使用這個值。

藉由在[disk create](/cli/azure/disk#az-disk-create) Cmdlet 中指定 **--for-upload**參數和 **--upload-size-bytes**參數，建立空的標準 HDD 來進行上傳：

`<yourdiskname>` `<yourresourcegroupname>` `<yourregion>` 以您選擇的值取代、。 `--upload-size-bytes`參數包含的範例值 `34359738880` ，請將它取代為適合您的值。

> [!TIP]
> 如果您要建立 OS 磁片，請將--hyper-v 世代新增 <yourGeneration> 至 `az disk create` 。

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

如果您想要上傳 premium SSD 或標準 SSD，請將**standard_lrs**取代為**premium_LRS**或**standardssd_lrs**。 目前不支援 Ultra 磁片。

既然您已建立為上傳程式設定的空受控磁片，您可以將 VHD 上傳至其中。 若要將 VHD 上傳至磁片，您將需要可寫入的 SAS，以便將它當做您上傳的目的地。

若要產生空受控磁片的可寫入 SAS，請將和取代為 `<yourdiskname>` `<yourresourcegroupname>` ，然後使用下列命令：

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

既然您有空的受控磁片的 SAS，您可以使用它將受控磁片設定為上傳命令的目的地。

使用 AzCopy v10，藉由指定您產生的 SAS URI，將本機 VHD 檔案上傳至受控磁片。

這項上傳與對等的[標準 HDD](disks-types.md#standard-hdd)具有相同的輸送量。 例如，如果您的大小等於 S4，則輸送量最高可達 60 MiB/秒。 但是，如果您的大小等於 S70，則輸送量最高可達 500 MiB/秒。

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

上傳完成之後，而且您不再需要將任何其他資料寫入磁片，請撤銷 SAS。 撤銷 SAS 將會變更受控磁片的狀態，並可讓您將磁片連結至 VM。

取代 `<yourdiskname>` 和 `<yourresourcegroupname>` ，然後使用下列命令將磁片設為可用：

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>複製受控磁碟

直接上傳也會簡化複製受控磁片的程式。 您可以在相同區域內或跨區域（到另一個區域）複製。

下列腳本會為您執行此作業，此程式類似于先前所述的步驟，因為您使用的是現有的磁片，所以有一些差異。

> [!IMPORTANT]
> 當您從 Azure 提供受控磁片的磁片大小（以位元組為單位）時，您需要新增512的位移。 這是因為在傳回磁片大小時，Azure 會省略頁尾。 如果您不這麼做，複製將會失敗。 下列腳本已經為您執行這項工作。

`<sourceResourceGroupHere>` `<sourceDiskNameHere>` 使用您的值取代、、 `<targetDiskNameHere>` 、 `<targetResourceGroupHere>` 和 `<yourTargetLocationHere>` （位置值的範例），然後執行下列腳本以複製受控磁片。

> [!TIP]
> 如果您要建立 OS 磁片，請將--hyper-v 世代新增 <yourGeneration> 至 `az disk create` 。

```azurecli
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[diskSizeBytes]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>後續步驟

既然您已成功將 VHD 上傳至受控磁片，您可以將該磁片當做[資料磁片連接到現有的 vm](add-disk.md) ，或[將磁片連結至 VM 作為 OS 磁片](upload-vhd.md#create-the-vm)，以建立新的 vm。 
