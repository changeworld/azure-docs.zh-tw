---
title: 變更 Azure 受控磁片的效能
description: 瞭解受控磁片的效能層級，並瞭解如何變更現有受控磁片的效能層級。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 6b7d670f5e38968fa6592e323ede075abf687435
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939012"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>受控磁片的效能層級 (預覽) 

Azure 磁碟儲存體目前提供內建的高載功能，以提供更高的效能來處理短期非預期的流量。 Premium Ssd 具有增加磁片效能的彈性，而不會增加實際的磁片大小。 這項功能可讓您符合工作負載的效能需求，並降低成本。 

> [!NOTE]
> 此功能目前為預覽狀態。 

這項功能適用于暫時需要一致效能等級的事件，例如假日購物、效能測試或執行定型環境。 若要處理這些事件，只要需要，您可以使用較高的效能層級。 當您不再需要額外的效能時，您可以返回原始層。

## <a name="how-it-works"></a>運作方式

當您第一次部署或布建磁片時，該磁片的基準效能層級會根據布建的磁片大小來設定。 您可以使用較高的效能層級來滿足更高的需求。 當您不再需要該效能層級時，您可以回到初始基準效能層級。

當您的定價層變更時，您的帳單會變更。 例如，如果您布建 P10 磁片 (128 GiB) ，您的基準效能層級會設定為 P10 (500 IOPS 和 100 MBps) 。 您將以 P10 費率計費。 您可以升級層級，以符合 P50 (7500 IOPS 和 250 MBps) 的效能，而不會增加磁片大小。 在升級期間，您將以 P50 費率計費。 當您不再需要較高的效能時，您可以回到 P10 層。 磁片會再次以 P10 費率計費。

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

- 這項功能目前僅支援 premium Ssd。
- 您必須先從執行中的 VM 卸離您的磁片，才能變更磁片層。
- 使用 P60、P70 和 P80 效能層級僅限於 4096 GiB 或更高版本的磁片。
- 磁片的效能層級只能每隔24小時變更一次。

## <a name="regional-availability"></a>區域可用性

您目前只能在美國東部2、美國中南部、美國中西部、澳大利亞東部區域的 premium Ssd 上，調整受控磁片的效能層級。

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

如果您需要調整磁片大小以利用較高的效能層級，請參閱下列文章：

- [使用 Azure CLI 擴充 Linux VM 上的虛擬硬碟](linux/expand-disks.md)
- [擴充連接至 Windows 虛擬機器的受控磁片](windows/expand-os-disk.md)
