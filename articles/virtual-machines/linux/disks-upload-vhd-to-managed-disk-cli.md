---
title: 使用 Azure CLI 上傳 VHD
description: 瞭解如何使用 Azure CLI，透過直接上傳，將 vhd 上傳至 Azure 受控磁片，以及跨區域複製受控磁片。
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 09/20/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 2a5bfec08546d6cf00b1e04017b3879db8f016ee
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970333"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>使用 Azure CLI 將 vhd 上傳至 Azure

本文說明如何將 vhd 從本機電腦上傳至 Azure 受控磁片。 之前，您必須遵循更牽涉的程式，其中包含將您的資料放在儲存體帳戶中，並管理該儲存體帳戶。 現在，您不再需要管理儲存體帳戶，或在其中暫存資料來上傳 vhd。 相反地，您會建立空的受控磁片，並直接將 vhd 上傳至其中。 這可簡化將內部部署 Vm 上傳至 Azure 的工作，並可讓您直接將 vhd 上傳至 32 TiB 到大型受控磁片。

如果您在 Azure 中提供 IaaS Vm 的備份解決方案，建議您使用直接上傳來將客戶備份還原至受控磁片。 如果您要從 Azure 外部的電腦上傳 VHD，速度將取決於您的本機頻寬。 如果您使用 Azure VM，則您的頻寬會與標準 Hdd 相同。

目前，標準 HDD、標準 SSD 和 premium SSD 受控磁片支援直接上傳。 Ultra Ssd 尚不支援此程式。

## <a name="prerequisites"></a>Prerequisites

- 下載最新[版本的 AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)。
- [安裝 Azure CLI](/cli/azure/install-azure-cli)。
- 儲存在本機的 vhd 檔案
- 如果您想要從內部部署環境上傳 vhd：已[針對 Azure 準備](../windows/prepare-for-upload-vhd-image.md)的 vhd，儲存在本機上。
- 或者，如果您想要執行複製動作，則是 Azure 中的受控磁片。

## <a name="create-an-empty-managed-disk"></a>建立空的受控磁片

若要將您的 vhd 上傳至 Azure，您必須建立為此上傳程式設定的空受控磁片。 建立之前，您應該先瞭解這些磁片的一些額外資訊。

這種受控磁片有兩種獨特的狀態：

- ReadToUpload，這表示磁片已準備好接收上傳，但未產生任何[安全存取](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)簽章（SAS）。
- ActiveUpload，這表示磁片已準備好接收上傳，並已產生 SAS。

在上述任一種狀態中，不論實際的磁片類型為何，受控磁片都會以[標準 HDD 定價](https://azure.microsoft.com/pricing/details/managed-disks/)計費。 例如，P10 會以 S10 計費。 這會是 true，直到在受控磁片上呼叫 `revoke-access` 為止，若要將磁片連結至 VM，這是必要的。

您必須要有您想要上傳之 vhd 的檔案大小（以位元組為單位），才可以建立空的標準 HDD 來進行上傳。 若要取得此項，您可以使用 `wc -c <yourFileName>.vhd` 或 `ls -al <yourFileName>.vhd`。 指定 **--upload-size-bytes**參數時，會使用這個值。

藉由在[disk create](/cli/azure/disk#az-disk-create) Cmdlet 中指定 **--for-upload**參數和 **--upload-size-bytes**參數，建立空的標準 HDD 來進行上傳：

```bash
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

如果您想要上傳 premium SSD 或標準 SSD，請將**standard_lrs**取代為**premium_LRS**或**standardssd_lrs**。 尚未支援 Ultra SSD。

您現在已建立一個為上傳程式設定的空白受控磁片。 若要將 vhd 上傳至磁片，您將需要可寫入的 SAS，以便將它當做您上傳的目的地。

若要產生空受控磁片的可寫入 SAS，請使用下列命令：

```bash
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

範例傳回值：

```
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>上傳 vhd

既然您有空的受控磁片的 SAS，您可以使用它將受控磁片設定為上傳命令的目的地。

使用 AzCopy v10，藉由指定您產生的 SAS URI，將本機 VHD 檔案上傳至受控磁片。

這項上傳與對等的[標準 HDD](disks-types.md#standard-hdd)具有相同的輸送量。 例如，如果您的大小等於 S4，則輸送量最高可達 60 MiB/秒。 但是，如果您的大小等於 S70，則輸送量最高可達 500 MiB/秒。

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

如果您的 SAS 在上傳期間過期，而且您尚未呼叫 `revoke-access`，您可以使用 `grant-access`再次取得新的 SAS 來繼續上傳。

上傳完成之後，而且您不再需要將任何其他資料寫入磁片，請撤銷 SAS。 撤銷 SAS 將會變更受控磁片的狀態，並可讓您將磁片連結至 VM。

```bash
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="copy-a-managed-disk"></a>複製受控磁碟

直接上傳也會簡化複製受控磁片的程式。 您可以在相同區域內或跨區域（到另一個區域）複製。

下列腳本會為您執行此作業，此程式類似于先前所述的步驟，因為您使用的是現有的磁片，所以有一些差異。

> [!IMPORTANT]
> 當您從 Azure 提供受控磁片的磁片大小（以位元組為單位）時，您需要新增512的位移。 這是因為在傳回磁片大小時，Azure 會省略頁尾。 如果您不這麼做，複製將會失敗。 下列腳本已經為您執行這項工作。

使用您的值取代 `<sourceResourceGroupHere>`、`<sourceDiskNameHere>`、`<targetDiskNameHere>`、`<targetResourceGroupHere>`和 `<yourTargetLocationHere>` （位置值的範例），然後執行下列腳本以複製受控磁片。

```bash
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

既然您已成功將 vhd 上傳至受控磁片，您可以將該磁片當做[資料磁片連接到現有的 vm](add-disk.md) ，或[將磁片連結至 VM 作為 OS 磁片](upload-vhd.md#create-the-vm)，以建立新的 vm。 

