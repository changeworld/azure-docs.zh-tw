---
title: 提升 Azure 受控磁片效能
description: 瞭解受控磁片的效能層級，以及如何升級受控磁片的效能層級。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 3d6b243ab517f3663f779d01569acf3d46ad8411
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328117"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>受控磁片的效能層級 (預覽) 

Azure 磁碟儲存體目前提供內建的高載功能，可達到更高的效能以處理短期非預期的流量。 Premium Ssd 可彈性地提高磁片效能，而不會增加實際的磁片大小，讓您符合工作負載的效能需求並降低成本，這項功能目前為預覽狀態。 這適用于暫時需要一致效能等級的事件，例如假日購物、效能測試或執行定型環境。 若要處理這些事件，您可以視需要選取較高的效能層級，並在不再需要額外的效能時，返回原始層。

## <a name="how-it-works"></a>運作方式

當您第一次部署或布建磁片時，該磁片的基準效能層級會根據布建的磁片大小來設定。 您可以選取較高的效能層級來滿足更高的需求，而且當不再需要該效能時，您可以回到初始基準效能層級。

當您的定價層變更時，您的帳單會變更。 例如，如果您布建 P10 磁片 (128 GiB) ，您的基準效能層級會設定為 P10 (500 IOPS 和 100 MB/s) ，而且會以 P10 費率計費。 您可以更新階層，以符合 P50 (7500 IOPS 和 250 MB/s) 的效能，而不需要增加磁片大小，在這段期間，您會以 P50 費率計費。 當不再需要較高的效能時，您可以回到 P10 層，磁片會再次以 P10 費率計費。

| 磁碟大小 | 基準效能層級 | 可以升級為 |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2、P3、P4、P6、P10、P15、P20、P30、P40、P50 |
| 8 GiB | P2 | P3、P4、P6、P10、P15、P20、P30、P40、P50 |
| 16 GiB | P3 | P4、P6、P10、P15、P20、P30、P40、P50 | 
| 32 GiB | P4 | P6、P10、P15、P20、P30、P40、P50 |
| 64 GiB | P6 | P10、P15、P20、P30、P40、P50 |
| 128 GiB | P10 | P15、P20、P30、P40、P50 |
| 256 GiB | P15 | P20、P30、P40、P50 |
| 512 GiB | P20 | P30、P40、P50 |
| 1 TiB | P30 | P40、P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | 無 |
| 8 TiB | P60 |  P70、P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | 無 |

如需帳單資訊，請參閱 [受控磁片定價](https://azure.microsoft.com/pricing/details/managed-disks/)。

## <a name="restrictions"></a>限制

- 目前僅支援 premium Ssd。
- 在變更階層之前，必須先從執行中的 VM 卸離磁片。
- 使用 P60、P70 和 P80 效能層級僅限於 4096 GiB 或更高的磁片。
- 磁片效能層級只能每隔24小時變更一次。

## <a name="regional-availability"></a>區域可用性

調整受控磁片的效能層級目前僅適用于下欄區域中的 premium Ssd：

- 美國中西部 

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>建立層級高於基準層的空白資料磁片

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az login

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>從 Azure Marketplace 映射建立層級高於基準層的 OS 磁片

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```
     
## <a name="update-the-tier-of-a-disk"></a>更新磁片層

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```
## <a name="show-the-tier-of-a-disk"></a>顯示磁片的層級

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

## <a name="next-steps"></a>後續步驟

如果您必須調整磁片大小以利用較大的效能層級，請參閱主題的文章：

- [使用 Azure CLI 擴充 Linux VM 上的虛擬硬碟](linux/expand-disks.md)
- [擴充連接至 Windows 虛擬機器的受控磁片](windows/expand-os-disk.md)
